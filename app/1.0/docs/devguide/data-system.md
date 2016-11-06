---
title: 数据系统原理
---

<!-- toc -->

<style>
table.config-summary pre, table.config-summary code {
  background: transparent !important;
  margin: 0px;
  padding: 0px;
}
table.config-summary td {
  vertical-align: middle;
  padding: 10px 10px 1 1;
}
</style>

Polymer能够观察组件属性的更改然后根据更改采取各种行动.这些行动, 或 *属性效果*, 包括了:


*   观察器. 当数据更改时调用回调.

*   计算属性. 基于其它属性计算出的虚拟属性并在其输入数据更改被重新计算.

*   数据绑定. 使用批注来使得数据更改时更新属性、标记或DOM结点上的文本内容.

每个Polymer组件管理各自的数据模型和local DOM组件. 组件的属性构成组件 *模型*. 数据绑定可以连接一个组件的模型到它的local DOM的元素上.

下面一个简单的组件:


```html
<dom-module id="name-card">
  <template>
    <div>{{name.first}} {{name.last}}</div>
  </template>
  <script>Polymer({ is: 'name-card' });</script>
</dom-module>
```

![A name-card element with a property, name. An arrow labeled 1 connects the name property to
a JavaScript object. An arrow labeled 2 connects the element to a box labeled local DOM tree
which contains a single element, div. An arrow labeled 3 connects the JavaScript object to the
div element.](/images/1.0/data-system/data-binding-overview-new.png)

1.  `<name-card>`组件有一个`name`属性 _引用_ 到一个JavaScript对象.
2.  `<name-card>`组件 _拥有_ 一个local DOM树, 它含有一个 `<div>` 组件.
3.  模板中的数据绑定连接了JavaScript对象和 `<div>` 组件.

数据系统基于 *路径*,而不是对象,其表示一个属性或子属性 *相对于组件* 的路径. 例如`<name-card>`组件对路径`"name.first"` 和 `"name.last"`做了数据绑定.
如果`<name-card>`的`name`属性有以下对象:


```js
{
  first: 'Lizzy',
  last: 'Bennet'
}
```

"`name`"路径引用到了组件的 `name` 属性 (一个对象)."`name.first`"路径和
`"name.last"`也引用到了同一个属性对象.

![The name-card element from the previous figure. An arrow labeled 1 connects the name property
to a JavaScript object that contains two properties, first: 'Lizzy' and last: 'Bennet'. Two arrows
labeled 2 connect the paths name and name.first with the object itself and the subproperty,
first, respectively.](/images/1.0/data-system/paths-overview-new.png)

1.  `name`属性引用到JavaScript对象.
2.  "name"路径引用对象自身. "name.first"路径引用它了子属性`first` (字符串`Lizzy`).

Polymer不自动检测所有数据更改.属性效果只在属性或子属性上发生了一个[_可观察更改_](#observable-changes)时.

**为什么使用路径?** 初看起来路径和可观察的更改有点奇怪. 但这样却成就了一个高性能的数据绑定系统.
当发生一个可观察的更改时,Polymer就只需要影响与更改相关的DOM结点.
{ .alert .alert-info }

综上所述:

*   单个JavaScript对象或组件可被多个组件引用,路径 **总是相对于组件的**.
*   一个路径上的 **可观察更改** 产生了 **属性效果**, 例如更新绑定或调用观察器.
*   数据绑定在不同组件上的路径之间建立链接.

## 可观察更改 {#observable-changes}

*可观察更改* 是 **一种Polymer可以关联到一个路径上的数据更改**. 某些更改是自动 *可以观察的*:

*   设置一个组件的 *直接属性*.

   `this.owner = 'Jane';`

   如果一个属性关联了任意的属性效果(如观察器、计算属性或数据绑定), Polymer会为属性创建一个设置方法用来在属性效果中自动调用.

*   使用双向数据绑定设置一个子属性.

    ```
    <local-dom-child name="{{hostProperty.subProperty}}"></local-dom-child>
    ```

    由数据绑定系统产生的更改会自动传播.此例中,如果
    `<local-dom-child>`对`name`属性进行了更改,更改以`"hostProperty.subProperty"`的路径更改向下传播到宿主.


### 不可观察的更改

**对象或数组必然会有变化 *不可* 观察**. 包括了:

*   为子属性赋值一个对象:

    ```js
    // unobservable subproperty change
    this.address.street = 'Elm Street';
    ```

    这里改变子属性`address.street` *不会* 调用`address`上的设置方法,所以不会被自动观察到.

*   数组变换:

    ```js
    // unobservable change using native Array.push
    this.users.push({ name: 'Maturin'});
    ```

    进行数组变换不会调用 `users`上的设置方法, 所以不会被自动观察到.

在这两种情况下,就需要使用Polymer方法来确保更改可以被观察到.

### 进行可被观察的数组变换 {#make-observable-changes}

Polymer提供方法来让子属性和数组上的更改可观察到:

```js
// mutate an object observably
this.set('address.street', 'Half Moon Street');

// mutate an array observably
this.push('users', { name: 'Maturin'});
```

一些情况下,不能使用Polymer方法来变换对象和数组(如在使用一个第三方库时).此时,可以使用`notifyPath` 和 `notifySplices`
方法来 *通知* 组件注意变化 **已经产生.**

```js
// Notify Polymer that the value has changed
this.notifyPath('address.street');
```

当调用 `notifyPath` 或 `notifySplices`, 组件应用了合适的属性效果如同刚刚发生的变化.

当调用 `set` 或 `notifyPath`时, 需要使用发生更改的 **准确路径**. 例如调用`this.notifyPath('address')`不会接受`address.street`的更改,如果`address`对象自身保持不变. 这是因为Polymer执行脏检测并且当指定的路径没有变化时不会产生任何属性效果.

大多数情况下, 如果对象的一个或多个属性发生了更改, 或数组中的一个或多个元素发生了更改, 就可以通过先设置对象或数组的属性为空然后再将恢复原始值的方法来强制Polymer跳过脏检查.

```js
var address = this.address;
this.address = {};
this.address = address;
```


相关任务:

*   [设置对象的子属性](model-data#set-path).
*   [数组变换Mutate an array](model-data#array-mutation).
*   [子属性更改通知给Polymer](model-data#notify-path).
*   [数组变换通知给Polymer](model-data#notifysplices).

## 数据路径 {#paths}

在数据系统中, _路径_ 是一个标识了属性或子属性相对于一个范围的字符串. 大多数情况下范围是一个宿主组件.例如下边的关系:

![Two elements, user-profile and address card. The user-profile element has a primaryAddress
property. An arrow labeled 1 connects the property to a JavaScript object. The address-card
element has an address property. An arrow labeled 2 connects the property to the same JavaScript
object.](/images/1.0/data-system/data-binding-paths.png)

1.  `<user-profile>`组件有一个 `primaryAddress` 属性来引用一个JavaScript对象.
2.  `<address-card>`组件有一个 `address` 属性来引用到相同的对象 .

重要的是, **Polymer不能自动知道这些属性引用到了相同的对象**.
如果 `<address-card>`更改了对象, 在`<user-profile>`上就不会有任何属性效果的调用.

**对于跨组件间的数据更改,数组必须使用数据绑定来连接.**


### 使用数据绑定连接路径

数据绑定在不同组件的路径间创建连接. 事实上, **数据绑定是连接不同组件间路径的唯一方法**. 例如上节中的`<user-profile>`示例. 如果 `<address-card>`位于`<user-profile>`组件的local DOM中,
两个路径就可以使用一个数据绑定来连接:


```
<dom-module id="user-profile">
  <template>
    …
    <address-card
        address="{{primaryAddress}}"></address-card>
  </template>
  …
</dom-module>
```

现在路径通过数据绑定进行了连接.

![Two elements, user-profile and address-card, both referring to a shared JavaScript object. An arrow labeled 1 connects the primaryAddress property on the user-profile element to the object. An arrow labeled 2 connects the address property on the address-card element to the same object. An double-headed arrow labeled 3 connects the path primaryAddress on user-profile to the path address on address-card.](/images/1.0/data-system/data-bound-paths-new.png)

1.  `<user-profile>`组件有一个 `primaryAddress`属性来引用一个JavaScript对象.
2.  `<address-card>`组件有一个 `address` 属性引用到了相同的对象.
3.  数组绑定连接了`<user-profile>`上的 `"primaryAddress"`和`<address-card>`上的`"address"`.

如果 `<address-card>` 使对象有了一个可观察的更改, 属性效果同样在
`<user-profile>`上被调用.

### 数据绑定范围 {#data-binding-scope}

路径都相对于当前数据绑定 *范围*.

对于任意组件的最高层范围都是组件的属性. 某些数据绑定助手组件
(如 [模板重复器](/1.0/docs/devguide/templates#dom-repeat)) 会产生新的嵌套范围.

对于观察器和计算属性,范围都是组件的属性.

### 特殊路径

路径是一系列路径段组成. *大多数情况下*, 每个路径段是一个属性名.

路径段有一些特殊类型.


*   通配符 `*`可被用于最后一个路径段(如 `foo.*`).
    这个通配路径表示 _在给定路径和其子属性上的所有变化_,包括数组变换.
*   字符串 `splices`可被用于最后一个路径段如 (如 `foo.splices`) 来表示在给定数组上的所有数组变换.
*   数组元素路径 (如 `foo.11`) 表示数组中的一个元素.



#### 通配路径 {#wildcard-paths}

通配符(`*`)被用到路径的最后一个路径段时表示对其上一个属性或其子属性的任意更改.假如 `users` 是一个数组, `users.*`表示关心`users`数组上或其子属性上的任何更改.

通配路径只在观察器、计算属性和计算绑定中使用.不能在数据绑定中使用通配符.

#### 数组变换路径

在路径的最后一个路径段中使用`splices`表示对其标识的数组进行数组*变换*(添加或删除). 假如`users` 是一个数组, 路径
`users.splices` 表示了该数组上的任意添加或删除.

`.splices`路径只在观察器、计算属性和计算绑定中用来表示*关心*数组变换. 观察一个`.splices`路径就可以以得到所注册通配路径上的一个变化**子集** (例如不想看到数组*内部*对象的子属性更改). **大多数情况下,对数组使用一个通配符观察器非常有用.**

#### 数组元素路径

Polymer支持两种方式来通过路径定位一个数组元素: 使用索引或使用一个不透明的不可变键.

*   索引,例如`"myArray.1"`来表示处于第一个位置的数组元素.

*   不透明键以(`#`)开始, 例如`"myArray.#1"`表示 *键*为"1"的数组元素.

**为什么使用键?** Polymer内部使用键来为特定数组元素提供一个稳定的路径,
不论元素的当前位置是什么. 这样就可以让管理列表的组件来创建一个数组元素和其所生成DOM组成的稳定关联. 键由Polymer生成并且存在于整个数组元素的生命周期.
{.alert .alert-info }

当**向Polymer通知数组路径上的一个变更时**, 可以使用索引或键.

当**Polymer为数组元素生成一个通知时**, 它用*键*来进行标识,
原因是它为获取索引还要做额外的工作. Polymer提供了通过键来访问原始数组元素的方法.

**大多数情况下不需要直接操作键**. Polymer提供了如 [template repeater element](/1.0/docs/devguide/templates#dom-repeat)这样的助手组件将很多复杂的数组操作进行了抽象.

相关任务:

*   [通过键查找数组元素](model-data#get-array-item).
*   [通过键查找数组索引](model-data#get-array-index).

### 两个路径引用相同的对象 {#two-paths}

有时一个组件中两个路径会指向同一个对象S.

例如一个组件有两个属性, `users` (数组) 和 `selectedUser` (对象). 当选择一个用户时, `selectedUser`指向数组中的一个对象.


![A user-list element and an array with four items labeled \[0\] through \[3\]. The user-list has two properties, users and selectedUser. The users property is connected to the array by an arrow labeled 1. The selectedUser property is connected to the array item, \[1\] by an arrow labeled 2.](/images/1.0/data-system/linked-paths-new.png)

1.  `users`属性引用到数组自身.
2.  `selectedUser`属性引用数组中的一个元素.

示例中, 组件有一上结路径都引用到了数组的第二个元素上:


*   `"selectedUser"`
*   `"users.1"` (1是`users`数组中的元素索引)
*   `"users.#5"` (5是元素的不可变键)

默认情况下Polymer不能能数组路径(如`users.1`)关联到`selectedUsers`.

在这个用例中Polymer提供了一个`<array-selector>`数组绑定助手组件 用来维护数据和其被选元素之间的路径连接.
(`<array-selector>`对选择多个数组元素同样适用.)

在其它用例中有一个命令式方法F[`linkPaths`](/1.0/docs/api/Polymer.Base#method-linkPaths)可用来关联两个路径.
当两个路径*关联*后,在一个路径上的[可观察更改](#observable-changes)也可被另一个观察到 .


相关任务:

*   [连接两个路径到同一个对象](model-data#linkpaths)

*   [数组的数据绑定](templates#array-selector)



## 数据流动 {#data-flow}

Polymer实现了中间人模式, 允许宿主组件管理它和在其内部的local DOM结点之间的数据流动.

当两个组件用一个数组绑定连接后, 数据更改可以向 _下_ 从宿主流动到目标也可以向 _上_ 从目标流动到宿主,还可以双向流动.

当local DOM中的两个组件绑定到相同的属性后数据看起来是从一个组件流动到了另一个组件,但是这个流动是由宿主做为 _中间人的_ .
一个组件的更改 **向上** 传播给宿主,然后宿主再 **向下** 把更改传播给第二个组件.

### 数据流动是同步的

数据流动是 **同步**. 在代码中产生一个[可观察更改](#observable-changes)后,
所有都数据流动和属性效果都会在下一行JavaScript代码执行前发生,除非组件显式推迟了动作(例如调用了一个异步方法).

### 如何控制数据流动 {#data-flow-control}

绑定所支持的数据流动类型依赖于:

*   使用的绑定批注类型.
*   目标属性的配置.

有两种数据绑定批注类型:

*   **自动**, 允许向上(目标到宿主)和向下(宿主到目标)的数据流动.
    自动绑定使用双花括号(`{{ }}`):

    ```
    <my-input value="{{name}}"></my-input>
    ```

*   **单向**, 只允许向下的数据流动. 向上的数据流动是禁止的. 单向绑定使用两个中括号(`[[ ]]`).

    ```
    <name-tag name="[[name]]"></name-tag>
    ```

以下配置参数影响数据流动的来源和目标属性:

*   `notify`. **支持向上数据流动**的通知属性. 默认时属性是不通知的也就是不支持向上数据流动.
*   `readOnly`. **阻止向下数据流动**的只读属性. 默认时属性可读写并支持向下数据流动B.

属性定义示例 {.caption}

```js
properties: {
  // default prop, read/write, non-notifying.
  basicProp: {
  },
  // read/write, notifying
  notifyingProp: {
    notify: true
  },
  // read-only, notifying
  fancyProp: {
    readOnly: true,
    notify: true
  }
}
```

下表列出了基于目标属性配置的自动绑定数据流动类型:

<table class="config-summary">
  <tr>
    <th>
      配置
    </th>
    <th>
      结果
    </th>
  </tr>
  <tr>
    <td><pre><code>notify: false,
readOnly: false</code></pre></td>
    <td>
      单向,向下
    </td>
  </tr>
  <tr><td><pre><code>notify: false,
readOnly: true</code></pre></td>
    <td>
      无数据流动
    </td>
  </tr>
  <tr>
    <td><pre><code>notify: true,
readOnly: false</code></pre></td>
    <td>
      双向
    </td>
  </tr>
  <tr>
    <td><pre><code>notify: true,
readOnly: true</code></pre></td>
    <td>
      单向,向上
    </td>
  </tr>
</table>

相比之下,单向绑定只允许单向且向下的数据流动,所以`notify`标志不影响结果:


<table class="config-summary">
  <tr>
    <th>
      配置
    </th>
    <th>
      结果
    </th>
  </tr>
  <tr>
    </td>
    <td>
      <pre><code>readOnly: false</code></pre>
    </td>
    <td>
      单向,向下
    </td>
  </tr>
  <tr>
    <td>
      <pre><code>readOnly: true</code></pre>
    </td>
    <td>
      无数据流动
    </td>
  </tr>
</table>


 **属性配置 _只影响属性自身_, 不会影响子属性**. 尤其是当绑定的对象或数组属性创建了同宿主和目标组件共享的数据,不能阻止任一组件来操作共享的对象或数组.
查看[对象和数组的数据流动](#data-flow-objects-arrays)获取更多信息
{.alert .alert-warning}

### 数据流动示例

下例展示了上节中提到的多种数据流动场景.


示例1: 双向绑定 { .caption }

```
<script>
  Polymer({
    is: 'custom-element',
    properties: {
      someProp: {
        type: String,
        notify: true
      }
    }
  });
</script>
...

<!-- changes to "value" propagate downward to "someProp" on child -->
<!-- changes to "someProp" propagate upward to "value" on host  -->
<custom-element some-prop="{{value}}"></custom-element>
```

示例2: 单向绑定 (向下) { .caption }

```
<script>
  Polymer({
    is: 'custom-element',
    properties: {
      someProp: {
        type: String,
        notify: true
      }
    }
  });
</script>

...

<!-- changes to "value" propagate downward to "someProp" on child -->
<!-- changes to "someProp" are ignored by host due to square-bracket syntax -->
<custom-element some-prop="[[value]]"></custom-element>
```

示例3: 单向绑定 (向下) { .caption }

```
<script>

  Polymer({
    is: 'custom-element',
    properties: {
      someProp: String    // no notify:true!
    }
  });

</script>
...

<!-- changes to "value" propagate downward to "someProp" on child -->
<!-- changes to "someProp" are not notified to host due to notify:falsey -->
<custom-element some-prop="{{value}}"></custom-element>
```

示例4: 单向绑定 (向上,子元素到宿主) { .caption }

```
<script>
  Polymer({
    is: 'custom-element',
    properties: {
      someProp: {
        type: String,
        notify: true,
        readOnly: true
      }
    }
  });
</script>

...

<!-- changes to "value" are ignored by child due to readOnly:true -->
<!-- changes to "someProp" propagate upward to "value" on host  -->
<custom-element some-prop="{{value}}"></custom-element>
```

示例5: 错误 / 无感觉状态 { .caption }

```
<script>
  Polymer({
    is: 'custom-element',
    properties: {
      someProp: {
        type: String,
        notify: true,
        readOnly: true
      }
    }
  });
</script>
...
<!-- changes to "value" are ignored by child due to readOnly:true -->
<!-- changes to "someProp" are ignored by host due to square-bracket syntax -->
<!-- binding serves no purpose -->
<custom-element some-prop="[[value]]"></custom-element>
```



### 向上和向下数据流动

由于是宿主组件管理数据流动,它可以直接操作目标组件.宿主通过设置目标组件的属性或调用其方法来向下传播数据.


![An element, host-element connected to an element, target-element by an arrow labeled 1.](/images/1.0/data-system/data-flow-down-new.png)

1.  当宿主组件上的属性更改后, 它设置了目标组件上相应的属性, 触发了关联的属性效果.

Polymer组件使用事件来向上传播数据. 当可观察更改发生后目标组件触发一个不冒泡的事件. (更改事件详情参考[更改通知事件](#change-events).)

对于**双向绑定**, 宿主组件监听这些更改事件并传播.例如, 设置一个属性并调用任意相关的属性效果.属性效果可以包括:


*   更新数据绑定将改进传播给兄弟组件.
*   生成另外的更改事件来向上传播更改.


![An element, target-element connected to an element, host-element by an arrow labeled 1. An arrow labeled 2 connects from the host element back to itself.](/images/1.0/data-system/data-flow-up-new.png)

1.  目标组件上的一个属性更改触发了一个属性更改事件.
2.  目标组件指接收事件并设置相应属性以及调用相关属性效果.

对于**单向绑定**批注, 宿主不创建更改监听器所以不会向上传播数据更改.

### 对象和数组的数据流动 {#data-flow-objects-arrays}

对于对象和数组属性,数据流动有一些复杂.一个对象或数组可被多个组件引用并且没有办法来阻止某个组件来转换一个共享数组或修改对象上的子属性.

结果就是Polymer**以**双向绑定来对待数组和对象的内容.也就是:


*   数据更新总是向下流动, 即使目标属性被标记为只读.
*   总是触发向上的更改事件流动,即使目标属性没有被标记为通知.

由于单向绑定批注不创建事件监听器,它阻止了这个更改通知传播给宿主组件.


### 更改通知事件 {#change-events}

当下列[可观察事件](#observable-changes)发生时组件触发一个更改通知事件:

*   通知属性的更改.

*   子属性更改.

*   数组变换.

事件类型属性表示了哪种属性发生了更改:遵循以下命名转换
<code><var>property</var>-changed</code>, <code><var>property</var></code>是属性名以中划线连接(所以`this.firstName`触发了`first-name-changed`).

可以手动附加一个<code><var>property</var>-changed</code>监听器到组件来通知外部组件,框架或属性更改的库.

事件的内容根据更改而有所不同.

*   对于属性更改,属性的新值位于`detail.value`字段.
*   对于子属性更改,子属性的 _路径_ 位于`detail.path`字段,
    其新值位于`detail.value`字段.
*   对于数组变换, `detail.path`字段是一个数组变换路径如"myArray.splices",`detail.value`也是一样.

### 自定义更改通知事件

原生组件如`<input>`不提供Polymer用来向上数据流动的更改通知事件.为了支持原生输入组件上的双向绑定, Polymer可以关联一个**自定义更改通知事件**到数据绑定.
例如,绑定到文件输入时可以指定`input` 或 `change`事件:


```
<input value="{{firstName::change}}">
```


此例中`firstName`属性绑定到了输入的`value`属性. 每当输入组件触发它的`change`事件时, Polymer更新`firstName`属性来匹配输入的值并调用任意关联的属性效果. **事件的内容不重要了.**

这个技术对于原生输入组件特别有用,也可用于为非Polymer组件提供双向绑定,只要组件暴露一个属性并且当属性更改时触发一个事件.

相关任务:

*   [双向绑定到非Polymer组件](data-binding#two-way-native)

### 组件初始化

当组件初始化它的local DOM时,它配置自己的local DOM子元素的属性并初始化数据绑定.

宿主值在初始化时优先使用.例如当宿主属性绑定到了目标属性,如果宿主和目标组件都指定了默认值那么会使用宿主的默认值.

## 属性效果 {#property-effects}

属性效果由给定属性(或路径)上的[可观察更改o](#observable-changes)触发:

*   重新计算属性.
*   更新数据绑定.
*   将属性值映射到宿主组件上的标记.
*   触发更改通知事件.
*   调用观察器.

### 数据绑定

*数据绑定*建立了连接用来连接一个宿主组件上的数据和它local DOM中的属性或`target node`中的标记.通过添加 _批注_ 到组件的local DOM模板来创建数据绑定.

批注是设置在目标组件上的标记值,包括了数据绑定分隔符
`{{ }}` 或 `[[ ]]`.

双向属性绑定:

<code><var>target-property</var>="{{<var>hostProperty</var>}}"</code>

单向属性绑定:

<code><var>target-property</var>="[[<var>hostProperty</var>]]"</code>

标记绑定:

<code><var>target-attribute</var>$="[[<var>hostProperty</var>]]"</code>

可以在组件的html文本内使用数据绑定批注, 等效于绑定组件的`textContent`属性.

```html
<div>{{hostProperty}}</div>
```

分隔符中的文本可以为下列一种:

*   属性或子属性路径 (`users`, `address.street`).
*   计算绑定 (`_computeName(firstName, lastName, locale)`)
*   上面的任意一种使用了否定运算符(`!`).

查看[Data binding](data-binding)获取更多信息.
