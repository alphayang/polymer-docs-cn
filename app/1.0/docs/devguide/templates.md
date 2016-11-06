---
title: 数据绑定的工具组件
---

<!-- toc -->

Polymer提供一组自定义组件来处理常用的数据绑定场景:

-   模板重复生成器.为数组中的每个项目按照模板内容创建一个实例.
-   数据选择器. 维护一个结构化数据数组的选择状态.
-   条件模板. 如果条件成立就添加内容.
-   自动绑定模板. 允许Polymer组件之外的数据绑定.

## 模板重复生成器 (dom-repeat) {#dom-repeat}

模板重复生成器是一个可以绑定数组的特殊模板.
为数组中的每个项目按照模板内容创建一个实例.
在每个实例中都创建一个新的[数据绑定范围](data-system#data-binding-scope)，它包含以下属性
:

*   `item`. 用来创建实例的数组元素.
*   `index`. 数组元素在数组中的索引. (`index`值在数组排序或过滤后改变)

模板重复生成器是一个[自定义类型扩展组件](registering-elements#type-extension),它扩展自内置的
`<template>`组件,所心书写为`<template is="dom-repeat">`.

例如: { .caption }

```
<dom-module id="employee-list">
  <template>

    <div> Employee list: </div>
    <template is="dom-repeat" items="{{employees}}">
        <div># <span>{{index}}</span></div>
        <div>First name: <span>{{item.first}}</span></div>
        <div>Last name: <span>{{item.last}}</span></div>
    </template>

  </template>

  <script>
    Polymer({
      is: 'employee-list',
      ready: function() {
        this.employees = [
            {first: 'Bob', last: 'Smith'},
            {first: 'Sally', last: 'Johnson'},
            ...
        ];
      }
    });
  </script>

</dom-module>
```

数组元素子属性的更改通知会传递给模板实例后,实例触发正常[变动通知事件](data-system#change-events).
如果`items`数组使用了双路绑定分隔符,那么对单个数组元素的变动通知会向上传递.

`items`数组自身的操作(`push`, `pop`, `splice`, `shift`,
`unshift`)则应当使用Polymer的
[数组变换方式](model-data#array-mutation).
这些方式确保更改可以被数据系统[观察到](data-system#observable-changes). 更多数组变换可查看[使用数组](model-data#work-with-arrays).

### 处理`dom-repeat`模板的事件 {#handling-events}

当处理由一个`dom-repeat`模板实例产生的事件时,我们通常希望把组件的事件匹配到生成组件的数据模型.

当在`<dom-repeat>`模板内部添加一个声明式事件处理器时,
重复生成器会每一个事件添加一个`model`属性然后发送给监听器. `model`
对象包含了用于生成模板实例的数据范围所以数据项称为`model.item`:

```
<dom-module id="simple-menu">

  <template>
    <template is="dom-repeat" id="menu" items="{{menuItems}}">
        <div>
          <span>{{item.name}}</span>
          <span>{{item.ordered}}</span>
          <button on-click="order">Order</button>
        </div>
    </template>
  </template>

  <script>
    Polymer({
      is: 'simple-menu',
      ready: function() {
        this.menuItems = [
            { name: "Pizza", ordered: 0 },
            { name: "Pasta", ordered: 0 },
            { name: "Toast", ordered: 0 }
        ];
      },
      order: function(e) {
        var model = e.model;
        model.set('item.ordered', model.item.ordered+1);
      }
    });
  </script>

</dom-module>
```

`model`是一个`Polymer.Base`的实例, 所以`set`, `get`以及数组变换方法在`model`对象上都可以使用,用这些方法来操作模型.

**注意:** `model`属性**不会**添加给以主动注册(使用`addEventListener`)的事件监听器,或添加到`<dom-repeat>`模板父结点的监听器. 此时,可以使用`<dom-repeat>` `modelForElement`方法来获取生成该组件的模型数据. (相应的还有
`itemForElement`和`indexForElement` 方法.)
{ .alert .alert-info }


### 列表过滤和排序

为了对_已显示_的列表数据进行排序或过滤,需要为`dom-repeat`指定一个`filter`或
`sort`属性(或两个都指定):

*   `filter`. 定义一个过滤回调函数,接受单个参数并返回true来显示或false来忽略.
    注意这个很**象**标准的
    `Array` [`filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) API, 只是架设只接受一个数组参数. 出于性能原因不包括`index`索引参数. 查看[对数组索引过滤](#filtering-on-index)获取更多信息.
*   `sort`. 指定一个遵循`Array`
    [`sort`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) API的标准比较函数.

上两情况下,可以使用一个函数对象或定义在宿主组件上的一个函数名称字符串做为值.

默认情况下`filter`和`sort`函数只在以下情况下运行:

*   由数组产生的一个[可被观察到的改变](data-system#observable-changes)
    (例如添加或删除数据项).
*   `filter`或`sort`函数改变了.

当不相关的数据发生更改时可以调用[`render`](#synchronous-renders)来运行`filter`或`sort`.例如组件有一个
`sortOrder`属性来改变`sort`的工作方式,就可以在`sortOrder`中调用`render`.

为了在`items`的特定子字段更改时运行`filter`或`sort`函数, 需要设置`observe`属性为一个空格分隔的`item`子字段列表就可以让列表重新过滤或重新排序.

例如一个`dom-repeat`带有如下的过滤器:

```
isEngineer: function(item) {
    return item.type == 'engineer' || item.manager.type == 'engineer';
}
```

`observe`属性应当配置为如下:

```
<template is="dom-repeat" items="{{employees}}"
    filter="isEngineer" observe="type manager.type">
```

修改`manager.type`字段时列表会被重新排序:

```
this.set('employees.0.manager.type', 'engineer');
```

#### 动态排序和过滤修改Dynamic sort and filter changes

`观察`属性可以用来指定用于观察的数组元素子属性用来进行排序和过滤. 有时候也需要根据一个不相关的值来动态调整排序或过滤,当依赖的一个或多个属性有更改时就可以使用一个经过计算的绑定来_返回_一个动态过滤器或排序方法.

```
<dom-module id="employee-search">

  <template>
    <input value="{{searchString::input}}">
    <template is="dom-repeat" items="{{employees}}" as="employee"
        filter="{{computeFilter(searchString)}}">
        <div>{{employee.lastname}}, {{employee.firstname}}</div>
    </template>
  </template>

  <script>
    Polymer({
      is: "employee-search",
      computeFilter: function(string) {
        if (!string) {
          // set filter to null to disable filtering
          return null;
        } else {
          // return a filter function for the current search string
          string = string.toLowerCase();
          return function(employee) {
            var first = employee.firstname.toLowerCase();
            var last = employee.lastname.toLowerCase();
            return (first.indexOf(string) != -1 ||
                last.indexOf(string) != -1);
          };
        }
      },
      properties: {
        employees: {
          type: Array,
          value: function() {
            return [
              { firstname: "Jack", lastname: "Aubrey" },
              { firstname: "Anne", lastname: "Elliot" },
              { firstname: "Stephen", lastname: "Maturin" },
              { firstname: "Emma", lastname: "Woodhouse" }
            ]
          }
        }
      }
    });
  </script>
</dom-module>
```

在这个示例中一旦`searchString`属性值改变就会调用
`computeFilter`来为`filter`属性计算一个新值.

#### 对数组索引进行过滤 {#filtering-on-index}

由于Polymer内部跟踪数组的方式导致数组的索引没有传递到过滤函数中.在数组索引中查看数据项是一个O(n)的复杂度.在过滤函数中这样做会造成
**显著的性能影响**.

如果你需要在数组索引中查找并且接受这这种性能影响就可以使用如下的代码:

```
filter: function(item) {
  var index = this.items.indexOf(item);
  ...
}
```

过滤函数在`dom-repeat`中的引用值是`this`,就可以值以`this.items`来引用原数组并查找索引.

这个查看返回的是**原**数组的索引, 可能跟已显示的（被过滤和排序过的)数组索引不一致.

### 嵌套dom-repeat模板 {#nesting-templates}

当嵌套多个`dom-repeat`模板时,可能需要在父结点访问嵌套的数据.在一个`dom-repeat`内部, 可以访问任意对父结点公开的属性除非属性被隐藏在自己的范围内.

例如`item`和`index`属性默认添加到`dom-repeat`
来隐藏任意名称相近的属性不被父结点访问.

为了访问嵌套`dom-repeat`模板中的属性, 使用`as`标记来分配一个不同的的属性名称. 使用`index-as`标记来为索引属性分配一个不同的名称.

```
<div> Employee list: </div>
<template is="dom-repeat" items="{{employees}}" as="employee">
    <div>First name: <span>{{employee.first}}</span></div>
    <div>Last name: <span>{{employee.last}}</span></div>

    <div>Direct reports:</div>

    <template is="dom-repeat" items="{{employee.reports}}" as="report" index-as="report_no">
      <div><span>{{report_no}}</span>.
           <span>{{report.first}}</span> <span>{{report.last}}</span>
      </div>
    </template>
</template>
```

### 强制同步渲染 {#synchronous-renders}

调用[`render`](/{{{polymer_version_dir}}}/docs/api/dom-repeat#method-render)
来强制一个`dom-repeat`模板和它的关联数据同步渲染. 通常改动被批量处理和异步渲染N. 同步渲染增加了性能开销但对如下场景很有用:

*   单元测试时确保数据在做DOM生成检查前渲染.
*   确保列表在定位到特定内列表项前被渲染.
*   当数组**外部**的数据(例如排序或过滤条件)发生更改时重新运行`sort`或`filter`函数.

`render` **仅**对Polymer中[数组变换方法](model-data#array-mutation)引起的[可观察改变](data-system#observable-changes)有效.
如果是应用代码或第三方库操作了数组而**没有**用Polymer的方法可以这样做:

*   如果确定知道_数组中的更改e_, 使用
    [`notifySplices`](model-data#notifysplices)来确保观察数组的任意组件都被通知到.
*   如果不确定数组的更改, 可以用[Override dirty
    checking](model-data##override-dirty-check)来强制数据系统重新计算整个数组.

更多的Polymer数据系统和数组变换可以查看[数组变换](model-data#work-with-arrays).

### 改进大列表的性能 {#large-list-perf}

默认情况下`dom-repeat`将一次性渲染列表中的所有数据. 当试图使用`dom-repeat`的来渲染一个非常大的列表时可能会阻塞UI. 遇到这样的问题,可以通过设置[`initialCount`](/{{{polymer_version_dir}}}/docs/api/dom-repeat#property-initialCount)来启用
"chunked"渲染.
在分块渲染模式下,
`dom-repeat`先渲染`initialCount`个数据项, 然后在每一帧递增渲染相同的数据项. 这样做可以让UI线程在分块渲染间隔来处理用户输入.可以使用只读属性[`renderedItemCount`](/{{{polymer_version_dir}}}/docs/api/dom-repeat#property-renderedItemCount)来查看已经渲染的数量.

`dom-repeat`会调整每次的渲染数据来匹配刷新频率并保持这个目标频率. 也可以手动指定
[`targetFramerate`](/{{{polymer_version_dir}}}/docs/api/dom-repeat#property-targetFramerate)来进行优化.

同时可以在传递`filter`或`sort`方法前设置一个去抖时间[`delay`](/{{{polymer_version_dir}}}/docs/api/dom-repeat#property-delay)
属性来延迟过滤或排序的执行.

## 绑定一个数组选择器 (array-selector) {#array-selector}

保持结构化数据同步需要Polymer理解
数据绑定的关联路径.`array-selector`组件可以在选择特定数组元素时确保这个路径有效.

`items`属性收受一个用户数据的数组. 调用`select(item)`
和`deselect(item)`可以更新一个与应用中其它部分绑定的`selected`属性. 任何对已选择数据子字段的改动都会同步到`items`数组.

数组选择器支持单选或多选.
当`multi`为false时, `selected`属性表示最后一个选择的元素. 当`multi`为true时, `selected`是一个包含所有已选择元素的数组.

```
<dom-module id="employee-list">

  <template>

    <div> Employee list: </div>
    <template is="dom-repeat" id="employeeList" items="{{employees}}">
        <div>First name: <span>{{item.first}}</span></div>
        <div>Last name: <span>{{item.last}}</span></div>
        <button on-click="toggleSelection">Select</button>
    </template>

    <array-selector id="selector" items="{{employees}}" selected="{{selected}}" multi toggle></array-selector>

    <div> Selected employees: </div>
    <template is="dom-repeat" items="{{selected}}">
        <div>First name: <span>{{item.first}}</span></div>
        <div>Last name: <span>{{item.last}}</span></div>
    </template>

  </template>

  <script>
    Polymer({
      is: 'employee-list',
      ready: function() {
        this.employees = [
            {first: 'Bob', last: 'Smith'},
            {first: 'Sally', last: 'Johnson'},
            ...
        ];
      },
      toggleSelection: function(e) {
        var item = this.$.employeeList.itemForElement(e.target);
        this.$.selector.select(item);
      }
    });
  </script>

</dom-module>
```


## 条件模板 {#dom-if}

组件可以被基于一个布尔属性的条件来添加，通过包装为一个自定义的`HTMLTemplateElement`名为`dom-if`的扩展类型.
`dom-if`模板的`if`属性为true时便把自己的内容替换到DOM上.

当`if`属性变为falsy时, 默认情况下所有的添加组件都被隐藏
(但仍然在DOM树中). 这样当`if`属性再为true时可以有更好的性能.  可以通过设置`restamp`属性为`true`来禁用此功能. 由于`if`在做功能切换时组件会被销毁和重新遮盖,结果就慢了.

下面的简单示例显示了条件模板是如何工作的. 下边还有使用条件模板的推荐指南.

Example: { .caption }

```
<dom-module id="user-page">

  <template>

    All users will see this:
    <div>{{user.name}}</div>

    <template is="dom-if" if="{{user.isAdmin}}">
      Only admins will see this.
      <div>{{user.secretAdminStuff}}</div>
    </template>

  </template>

  <script>
    Polymer({
      is: 'user-page',
      properties: {
        user: Object
      }
    });
  </script>

</dom-module>
```


尽管隐藏和显示组件比销毁和重新创建它们要快,但条件模板更适用于组件被大量添加并且条件很少(或从不)为true时降低初始化开销.否则任意使用条件模板会*使用*大量资源从而影响性能.

考虑一个4个用户界面的应用再加一个可选的管理界面.如果大多娄用户只使用4个界面来完成日常的使用,那么好的做法是在启动时(当然应用启动时间要做合理范围内)就添加这些组件并且在用户使用应用过程中隐藏/显示界面,比用户使用时每次都要销毁和重新创建所有组件要好的多.  使用条件模板可能是一个不好的主意,因为它只在通过添加来节省启动时间,节省的时间被转到了用户后续操作的延迟时间上.可以通过简单的对`hidden`属性(例如`<div hidden$="{{!shouldShow}}">`)进行绑定来隐藏/显示组件,这样就不需要条件模板了.

但是由于管理界面只显示给应用的管理用户那么使用条件模板就是合适的.  大多数用户并不是管理员,所以这些用户就不用承受管理界面的添加而带来的性能开销特别当管理界面很重时.

## 自动绑定模板 {#dom-bind}

Polymer数据绑定只针对由Polymer管理的模板. 所心数据绑定只对组件内部内部的local DOM模板起作用而不是主文档中的模板.

想要**不**定义一个新自定义组件而使用Polymer绑定的话,就使用`dom-bind`组件.这个模板会立即把自己的内容添加添加到主文档中.自动绑定模板中的数据绑定使用模板自身为绑定范围.

```
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <script src="components/webcomponentsjs/webcomponents-lite.js"></script>
  <link rel="import" href="components/polymer/polymer.html">
  <link rel="import" href="components/iron-ajax/iron-ajax.html">

</head>
<body>

  <!-- Wrap elements with auto-binding template to -->
  <!-- allow use of Polymer bindings in main document -->
  <template id="t" is="dom-bind">

    <iron-ajax url="http://..." last-response="{{data}}" auto></iron-ajax>

    <template is="dom-repeat" items="{{data}}">
        <div><span>{{item.first}}</span> <span>{{item.last}}</span></div>
    </template>

  </template>

</body>
<script>
  var t = document.querySelector('#t');

  // The dom-change event signifies when the template has stamped its DOM.
  t.addEventListener('dom-change', function() {
    // auto-binding template is ready.
  });
</script>
</html>
```

所有的`dom-bind`特性已经存在于每个Polymer的_内部_. **自动绑定模板只应当用在Polymer组件之_外_.**

## dom-change事件 {#dom-change}

当一个模板助手组件更新DOM树时就触发一个`dom-change`事件.

大多数情况下只需要通过修改_model data_来与创建好的DOM进行交互,而不需要直接创建结点来交互.如果需要直接访问结点时应使用`dom-change`事件.
