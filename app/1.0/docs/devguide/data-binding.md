---
title: 数据绑定
---

<!-- toc -->

_数据绑定_将自定义组件(_宿主_组件)的数据连接到组件local DOM(_子_ 或 _目标组件_)中的属性或标记.宿主组件数据可以是属性或子属性表示为一个[数据路径](data-system#data-paths),或基于一个或多个路径生成的数据.

通过添加批注到组件的local DOM模板来添加数据绑定.

```
<dom-module id="host-element">
  <template>
    <target-element target-property="{{hostProperty}}"></target-element>
  </template>
</dom-module>
```

更新数据绑定是一个[属性效应](data-system#property-effects).

## 数据绑定解析

数据绑定在local DOM模板中以一个HTML标记出现:

<pre><code><var>property-name</var><b>=</b><var>annotation-or-compound-binding</var></code>
<code><var>attribute-name</var><b>$=</b><var>annotation-or-compound-binding</var></code></pre>

绑定的左边标识出了目标属性或标记.

-   使用标记形式(中划线而不是camelCase)的属性来绑定, 查看[属性名到标记名的映射](#property-name-mapping):

    ```html
    <my-element my-property="{{hostProperty}}">
    ```

    示例中绑定到目标属性, `<my-element>`上的`myProperty`.

-   绑定到标记时在`$`后使用标记名称:

    ```html
    <a href$="{{hostProperty}}">
    ```

    此例中绑定到锚组件的`href` **标记**.

绑定的右边由一个 _绑定批注_ 或 _复合绑定_ 组成:

<dl>
  <dt>Binding annotation</dt>
  <dd>Text surrounded by double curly bracket (<code>{{ }}</code>) or double square bracket
      (<code>[[ ]]</code>) delimiters. Identifies the host data being bound. </dd>
  <dt>Compound binding</dt>
  <dd>A string literal containing one or more binding annotations.</dd>
</dl>

不论数据流动是从宿主到目标还是目标到宿主,或由绑定批注控制的双向绑定和目标属性的设置.

-   双花括号 (<code>{{ }}</code>) 支持向上和向下的数据流动.

-   双中括号 (<code>[[ ]]</code>) 是 _单向的_, 并且只支持向下的的数据流动.

查看[如何控制数据流动](data-system#data-flow-control)来了解更多信息.

## 绑定到目标属性 {#property-binding}

要绑定到目标属性,指定对应于属性的标记名称, 使用一个[批注](#binding-annotation)或[复合绑定](#compound-binding)
作为标记值:

```
<target-element name="{{myName}}"></target-element>
```

示例绑定了目标组件的`name`属性到宿主组件的`myName`属性. 由于这个批注使用了双向或"自动"分隔符(`{{ }}`),
它创建一个双向绑定如果`name`属性也被配置为支持的话.

可以使用双中括号来确保使用单向绑定:

```
<target-element name="[[myName]]"></target-element>
```

属性名以标记格式指定, 详细格式参考[属性名称到标记名称映射](properties#property-name-mapping).可以在标记名称中使用中划线来绑定到组件的camel-case属性.
例如:

```
<!-- Bind <user-view>.firstName to this.managerName; -->
<user-view first-name="{{managerName}}"></user-view>
```

如果属性绑定到了一个对象或数组,两个组件都引用到了 **同一个对象**.这意味着任意一个组件可以修改对象并且一个真正的单向绑定是不可能的.
查看[对象和数组的数据流动](data-system#data-flow-objects-arrays).

**一些特殊的标记和属性.** 当绑定到`style`, `href`, `class`, `for` 或
`data-*`标记时, 推荐使用[标记绑定](#attribute-binding)
语法. 查看[绑定到原生组件标记](#native-binding)查看更多信息.
{ .alert .alert-info }

### 绑定到文本内容

要绑定到目标组件的`textContent`,可以简单包含目标组件中的批注或复合绑定.

```
<dom-module id="user-view">
  <template>
    <div>[[name]]</div>
  </template>

  <script>
    Polymer({
      is: 'user-view',
      properties: {
        name: String
      }
    });
  </script>
</dom-module>

<!-- usage -->
<user-view name="Samuel"></user-view>
```

绑定到文本内容总是单向由宿主到目标的.

## 绑定到目标标记 {#attribute-binding}

在绝大多数情况下, 绑定数据到其它组件应当使用[属性绑定](#property-binding), 通过为组件上的JavaScript对象设置新值来传递修改.

然而有时相对于属性需要在组件上设置一个标记.例如,当标记选择器被用于CSS或基于标记的API互操作性,
例如为辅助功能信息而设置的Accessible Rich Internet Applications (ARIA)标准.

经绑定到标记,在标记名称为添加一个dollar符(`$`):

```
<div style$="color: {{myColor}};">
```

标记值可以是一个[绑定批注](#binding-annotation)或[复合绑定](#compound-binding).

调用标记绑定结果:


```js
element.setAttribute(attr,value);
```

相反:


```js
element.property = value;
```

例如:


```html
<template>
  <!-- Attribute binding -->
  <my-element selected$="[[value]]"></my-element>
  <!-- results in <my-element>.setAttribute('selected', this.value); -->

  <!-- Property binding -->
  <my-element selected="{{value}}"></my-element>
  <!-- results in <my-element>.selected = this.value; -->
</template>
```


标记绑定总是单向由宿主到目标的.绑定值根据其值的 _当前_ 类型进行序列化,查看
[标记序列化](properties.html#attribute-serialization)获取更多信息.

再说一遍,当绑定到标记时值必须序列化为字符串, 使用属性绑定来纯粹传播数据总是能得到更好的性能.

### 不支持属性绑定的原生属性 {#native-binding}

有一些常见的原生组件属性Polymer不能直接进行数据绑定,原因是绑定会在导致一个或多个浏览器出现问题.

可以使用标记绑定来影响以下属性:

| Attribute | Property | Notes

| `class` | `classList`, `className` | 两个不同格式的属性映射.

| `style` | `style` | 规范中`style`用来引用一个只读的`CSSStyleDeclaration`对象.

| `href` | `href` |

| `for` | `htmlFor` |

| `data-*` |  `dataset` | 自定义数据标记(标记名以`data-`开始)保存在`dataset`属性.

| `value` | `value` | 只适用于`<input type="number">`.

**注意:** 数据绑定到`value`属性在IE中遇到 ***数值输入类型*** 不能工作. 在这个特定情况下可以使用单向标记绑定来设置数值输入的`value`.
或使用如`iron-input`或`paper-input`这样可以正常进行双向绑定的组件.
{.alert .alert-info }

此列表包含当前已知的属性绑定会导致问题的属性.其它可能会受到影响.

有很多原因导致属性不能被绑定:

*   跨浏览器的问题，不能够在某些属性值中放置绑定大括号`{{...}}`.

*   属性映射到了不同名称的JavaScript属性上(如`class`).

*   具有唯一结构的属性 (如`style`).

标记绑定到动态值 (使用`$=`):


```html
<!-- class -->
<div class$="[[foo]]"></div>

<!-- style -->
<div style$="[[background]]"></div>

<!-- href -->
<a href$="[[url]]">

<!-- label for -->
<label for$="[[bar]]"></label>

<!-- dataset -->
<div data-bar$="[[baz]]"></div>

<!-- ARIA -->
<button aria-label$="[[buttonLabel]]"></button>

```

## 绑定批注 {#binding-annotation}

Polymer提供两种数据绑定的分隔符:

<dl>
  <dt>单向分隔符: <code>[[<var>binding</var>]]</code></dt>
  <dd>单向绑定只允许 <strong>向下</strong> 数据流动.</dd>
  <dt>双向或"自动"分隔符: <code>{{<var>binding</var>}}</code></dt>
  <dd>自动绑定允许<strong>向上和向下</strong>数据流动.</dd>
</dl>

查看[Data flow](data-system#data-flow)获取更多关于双向绑定的向上数据流动的信息.

分隔符内的文本可以为以下一种:

*   属性或子属性路径(`users`, `address.street`).
*   计算绑定(`_computeName(firstName, lastName, locale)`).
*   使用否定运算符(`!`)的上面任意一种.

数据绑定批注中的路径是相对于当前[数据绑定范围](data-system#data-binding-scopes).

### 绑定到宿主属性 {#host-property}

最简单的绑定批注是使用一个宿主属性:

```
<simple-view name="{{myName}}"></simple-view>
```

如果属性被绑定到了一个对象或数组,两个组件都得到了 **相同对象** 的引用. 这意味着任意组件要以改变对象以及一个真正的单向绑定是不可能的.查看[对象和数组的数据流动](data-system#data-flow-objects-arrays)获取更多信息.

### 绑定到宿主子属性 {#path-binding}

绑定批注可以包含到子属性的路径,如下所示:

```
<dom-module id="main-view">

  <template>
    <user-view first="{{user.first}}" last="{{user.last}}"></user-view>
  </template>

  <script>
    Polymer({
      is: 'main-view',
      properties: {
        user: Object
      }
    });
  </script>

</dom-module>
```

子属性更改不会自动[被观察到](data-system#observable-changes).

如果宿主组件更新子属性需要使用`set`方法,查看
[设置给定路径的属性或子属性](model-data#set-path). 或`notifyPath`方法,查看
[通知子属性更改给Polymer](model#notify-path).


```
//  Change a subproperty observably
this.set('name.last', 'Maturin');
```

如果绑定是双向且目标组件更新了绑定属性,更改会自动向上传播.

如果子属性被绑定到了一个对象或数组,两个组件都得到了 **相同对象** 的引用. 这意味着任意组件要以改变对象以及一个真正的单向绑定是不可能的.查看[对象和数组的数据流动](data-system#data-flow-objects-arrays)获取更多信息.

### 逻辑不是操作符 {#expressions-in-binding-annotations}

绑定批注支持一个简单逻辑而不是操作符(`!`),位于绑定分隔符的第一个字符上:

```
<template>
  <my-page show-login="[[!isLoggedIn]]"></my-page>
</template>
```

上例中`showLogin`为`false`如果`isLoggedIn`有一个真值.

只支持一个非操作符的逻辑. 不能使用`!!`将值转换为布尔. 查看[计算绑定](#annotated-computed)获取更多关于复杂转换的信息.

**否定绑定是单向的**:
一个非操作符的逻辑绑定 **总是单向由宿主到目标的**.
{.alert .alert-info}

### 计算绑定 {#annotated-computed}

*计算绑定* 类似于[计算属性](observers#computed-properties). 在在给定批注内声明.

```
<div>[[_formatName(first, last, title)]]</div>
```

计算绑定声明包含一个计算函数名,在括号中传入 _依赖_ 列表.

组件模板中可以有多个计算绑定引用相同的计算函数=.

除了在计算属性和复杂观察器中支持的依赖类型, 计算绽的依赖还包括字符串或数字.

如果不需要在组件的API中暴露一个计算属性或在组件其它地方使用,就可以使用计算绑定.计算绑定同样对显示值的过滤或绑定同样有用.

计算绑定同计算属性有以下的不同点:

*   计算绑定依赖解析相对于当前 *绑定范围*.例如[模板重复器](templates.html#dom-repeat)内部,一个依赖属性可能指向当前`item`.

*   计算绑定参数列表包含文字参数.

*   计算绑定可以有 *空* 参数列表, 当计算函数只调用一次的时候.

示例: { .caption }

```
<dom-module id="x-custom">

  <template>
    My name is <span>[[_formatName(first, last)]]</span>
  </template>

  <script>
    Polymer({
      is: 'x-custom',
      properties: {
        first: String,
        last: String
      },
      _formatName: function(first, last) {
        return first + ' ' + last;
      }
      ...
    });
  </script>

</dom-module>
```

此时span的 `textContent` 属性绑定到了`_formatName`的返回值, 当`first`或`last`更改时被重新计算.

**计算绑定是单向的.** 计算绑定总是单向由宿主到目标的.
{.alert .alert-info}

#### 计算绑定的依赖 {#computed-binding-dependencies}

计算绑定的依赖可以包含任意[复杂观察器](observers#complex-observers)支持的依赖类型:

*   当前范围内的简单属性.

*   子属性的路径.

*   带通配符的路径.

*   数组拼接的路径.

另外,计算绑定还可以包含文本.

对于各种类型的依赖属性,传递给计算函数的参数同传递给观察器的参数是同样的.

与观察器和计算属性一样,计算函数 **直到所有依赖都为defined(`!=undefined`后才会调用)**.

查看[绑定到数组元素](#array-binding)来查看使用通配符路径的计算绑定示例.

#### 计算绑定中的文本 {#literals}

计算绑定中的参数可以是字符串或数字.

字符串可以使用单引号或双引号. 在标记或属性绑定中如果使用双引号表示属性值,则使用单引号来表示字符串,反之亦然.

**字符串中的逗号:** 字符串中的任意逗号 **必须** 使用转义符(`\`).
{.alert .alert-info }

例如:

```html
<dom-module id="x-custom">
  <template>
    <span>{{translate('Hello\, nice to meet you', first, last)}}</span>
  </template>
</dom-module>
```

最后,如果计算绑定没有依赖属性则只计算一次:

```
<dom-module id="x-custom">
  <template>
    <span>{{doThisOnce()}}</span>
  </template>

  <script>
    Polymer({

      is: 'x-custom',

      doThisOnce: function() {
        return Math.random();
      }

    });
  </script>
</dom-module>
```

## 复合绑定 {#compound-bindings}

可以在一个属性绑定或文本内容绑定中合并文本和绑定.例如:

```
<img src$="https://www.example.com/profiles/[[userId]].jpg">

<span>Name: [[lastname]], [[firstname]]</span>
```

复合绑定在任意的单独绑定值发生更改后被重新计算. 未定义的值则以空字符串插入.

**复合绑定是单向的**: 可以在复合绑定中使用单向(`[[ ]]`)或自动(`{{ }}`)绑定批注, 但是绑定 **总是单向由宿主到目标的.**
{.alert .alert-info}


## 绑定到数组和数组元素 {#array-binding}

为了保持批注可以被解析, **Polymer不提供直接绑定到一个数组元素的方法**.

```
<!-- Don't do this! -->
<span>{{array[0]}}</span>
<!-- Or this! -->
<span>{{array.0}}</span>
```


在数据绑定中有很多方法与数组交互:

*   `dom-repeat`助手组件可以帮你为数组中的每个元素创建一个模板实例.在`dom-repeat`实例内部, 可以绑定到数组元素的属性.

*   `array-selector`助手组件可以帮你把数据绑定数组的选集,选集可以是单个元素或原始数组的子集.

*   可以使用一个计算绑定来绑定一个单独的数组元素.

相关内容:

*   [模板重复器](templates#dom-repeat)
*   [数组选择器](templates#array-selector)
*   [绑定到数组元素](#bind-array-item)

### 绑定到数组元素 {#bind-array-item}

使用计算绑定来绑定到特定的数组元素或数组元素的子属性例如`array[index].name`.

以下示例展示了如何使用计算绑定来访问数组的属性.
计算函数在子属性值更改后需要被调用,
_或_ 如果数组本身变换了,所以绑定使用一个通配路径`myArray.*`.

```
<dom-module id="bind-array-element">

  <template>
    <div>[[arrayItem(myArray.*, 0, 'name')]]</div>
    <div>[[arrayItem(myArray.*, 1, 'name')]]</div>
  </template>

  <script>
    Polymer({

      is: 'bind-array-element',

      properties: {

        myArray: {
          type: Array,
          value: [{ name: 'Bob' }, { name: 'Doug' }]
        }
      },

      // first argument is the change record for the array change,
      // change.base is the array specified in the binding
      arrayItem: function(change, index, path) {
        // this.get(path, root) returns a value for a path
        // relative to a root object.
        return this.get(path, change.base[index]);
      },

      ready: function() {
        // mutate the array
        this.unshift('myArray', { name: 'Susan' });
        // change a subproperty
        this.set('myArray.1.name', 'Rupert');
      }
    });
  </script>

</dom-module>
```

## 双向绑定

双向绑定可以进行向下(由宿主到目标)和向上(由目标到宿主)传播. 向上传播更改时,必须使用自动数据绑定分隔符(`{{ }}`)以及目标属性必须设置为`notify: true`.查看[Data flow](data-system#data-flow)获取更多信息.

当绑定到一个数组或对象属性时,两个组件都可以访问共享的数组或对象,
并且都可以改变它.使用自动绑定分隔符来确保属性更改向上传播.查看[对象和数组的数据流动](#data-flow-objects-arrays)获取更多信息.

### 双向绑定到一个非Polymer组件 {#two-way-native}

如同[更改通知事件](#change-events)中描述的, Polymer使用事件命名转换来达到双向绑定.

双向绑定到原生组件或 _不_ 遵循事件命名转换的非Polymer组件,可以在批注中使用以下语法来指定一个自定义更改事件名 :

<code><var>target-prop</var>="{{<var>hostProp</var>::<var>target-change-event</var>}}"</code>


示例: { .caption }

```
<!-- Listens for `input` event and sets hostValue to <input>.value -->
<input value="{{hostValue::input}}">

<!-- Listens for `change` event and sets hostChecked to <input>.checked -->
<input type="checkbox" checked="{{hostChecked::change}}">

<!-- Listens for `timeupdate ` event and sets hostTime to <video>.currentTime -->
<video url="..." current-time="{{hostTime::timeupdate}}">
```

当绑定到Polymer组件的标准通知属性时,指定事件名称是不必要的,默认的转换是监听`property-changed`事件.以下构造是等同的:

```
<!-- Listens for `value-changed` event -->
<my-element value="{{hostValue::value-changed}}">

<!-- Listens for `value-changed` event using Polymer convention by default -->
<my-element value="{{hostValue}}">
```


## 已移动的部分

以下部分被移到了[数据系统原理](data-system):

<a id="#property-notification"></a>

-   属性更改通知和双向绑定.查看[如何控制数据流动](data-system#data-flow-control).

<a id="##path-binding"></a>

-   绑定以结构化数据. 查看[可观察更改](data-system#observable-changes),
    [数据路径](data-system#paths)和 [绑定到属性或子属性](#)
