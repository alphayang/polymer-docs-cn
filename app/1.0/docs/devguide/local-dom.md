---
title: Local DOM基础和API
---

<!-- toc -->

由组件创建和管理的DOM称为组件的_local
DOM_. 这个区别于组件的有时称为 _light DOM_ 的子结点.

Polymer支持多种local DOM的实现 .在支持shadow DOM的浏览器上可能使用shadow DOM来创建local DOM.在其它浏览器上Polymer基于由自己实现的称为 _shady DOM_ 来提供local DOM. Shady DOM受到了shadow DOM的启发.

Shady DOM要求在JavaScript中操作DOM时使用[Polymer DOM API](#dom-api)
. 此接口覆盖了大多数DOM方法和属性并与shady DOM和原生shadow DOM兼容


**注意:** 当前Polymer在所有浏览器中默认使用shady DOM. 为了在shadow DOM可用时使用它,可以查看[全局设置](settings).
{.alert .alert-info}


## Local DOM模板 {#template-stamping}

使用`<dom-module>`组件来指定组件的local DOM所使用的DOM. 给`<dom-module>`赋予一个匹配组件的`is`属性的`id`标记,并在`<dom-module>`中添加一个`<template>` . Polymer就会自动克隆模板内容到组件的local DOM.

示例: { .caption }

```html
<dom-module id="x-foo">

  <template>I am x-foo!</template>

  <script>
    Polymer({
      is: 'x-foo'
    });
  </script>

</dom-module>
```

组件定义具有命令和声明部分.命令部分称为`Polymer({...})`,声明部分是`<dom-module>`元素. 组件的命令和声明部分可以位于相同的html文件也可以在不同文件中.

`<script>`标签放在`<dom-module>`元素的内部或外部都可以.

组件模板必须在调用Polymer之前被解析.


**注意:** 组件应当在主文档之外进行定义,除非是为了测试. 关于在主文档内定义组件的警告,参看[主文档定义][mdd].
{.alert .alert-info}

[mdd]: registering-elements#main-document-definitions


## 自动节点查找 {#node-finding}

Polymer自动为它的local DOM中创建的静态实例结点构建一个映射,用来提供常用结点的快速访问从而不需手动查找. 任意在组件模板中带有`id`的结点都在`this.$`以`id`哈希保存.


**Note:** 使用数据绑定动态创建的结点(包括`dom-repeat`和`dom-if`模板) _没有_ 添加到`this.$`哈希中. 哈希只包含 _静态_ 创建的local DOM结点
(也就是定义在组件最外层模板中的结点).
{.alert .alert-info}

示例: { .caption }

```html
<dom-module id="x-custom">

  <template>
    Hello World from <span id="name"></span>!
  </template>

  <script>

    Polymer({

      is: 'x-custom',

      ready: function() {
        this.$.name.textContent = this.tagName;
      }

    });

  </script>

</dom-module>
```

要定位组件的local DOM中动态生成的结点,使用`$$`方法:

<code>this.$$(<var>selector</var>)</code>

`$$` 返回local DOM中匹配<code><var>selector</var></code>的第一个结点.


## DOM分布 {#dom-distribution}

为了支持使用组件的local DOM来合成它的light DOM,Polymer支持`<content>`组件.`<content>`毋伯提供了一个插入点可以让组件的light DOM与它的local DOM合并在一起.
`<content>`组件支持一个`select`标记用来使用一个简单选择器过滤结点.

示例: { .caption }

```html
<template>
  <header>Local dom header followed by distributed dom.</header>
  <content select=".content"></content>
  <footer>Footer after distributed dom.</footer>
</template>
```

在shadow DOM中,浏览器维护互相分离的light DOM树和shadow DOM树,并为了渲染而合建一个合并视图(合成树).

在shady DOM中, Polymer维护自己的light DOM树和shady DOM树.文档的DOM树等效于合成树.


## DOM API {#dom-api}

Polymer为其维护的light DOM树和local DOM树中的DOM操作提供了自定义API.


**注意:** 所有的DOM操作必须使用这个API,不能直接使用结点上的DOM API.
{.alert .alert-error}

这些方法和属性与它们在标准DOM中具有相同的签名,但有以下例外:

*   **`Array`不是`NodeList`**. 属性和方法返回的结点列表是一个`Array`而不是一个`NodeList`.

*   **Local DOM根结点**. 使用`root`属性来访问Polymer组件的local DOM根结点,等效于在原生shadow DOM中的shadow根结点.

*   **Async操作.** 插件、附加和删除操作在某些情况下出于性能考虑会被懒洋洋的执行. 为了在这些操作之后立即访问DOM(如`offsetHeight`, `getComputedStyle`等等),需要先调用`Polymer.dom.flush()`.

提供了以下方法和属性.

添加和删除子元素:

*   `Polymer.dom(parent).appendChild(node)`
*   `Polymer.dom(parent).insertBefore(node, beforeNode)`
*   `Polymer.dom(parent).removeChild(node)`
*   `Polymer.dom.flush()`

调用`append`/`insertBefore`添加结点到<var>parent</var>有
_light DOM_.  为了添加/附加到自定义组件的local DOM,使用local DOM中的结点做为父结点(或`this.root`这个local DOM中的根结点).

父元素和子元素API:

  * `Polymer.dom(parent).childNodes`
  * `Polymer.dom(parent).children`
  * `Polymer.dom(node).parentNode`
  * `Polymer.dom(node).firstChild`
  * `Polymer.dom(node).lastChild`
  * `Polymer.dom(node).firstElementChild`
  * `Polymer.dom(node).lastElementChild`
  * `Polymer.dom(node).previousSibling`
  * `Polymer.dom(node).nextSibling`
  * `Polymer.dom(node).textContent`
  * `Polymer.dom(node).innerHTML`


**Note:** 在使用light DOM子元素时可以考虑使用分布子元素或有效子元素API.
查看[操作light DOM子元素](#light-dom-children)获取详情.
{.alert .alert-info}

查询选择器:

  * `Polymer.dom(parent).querySelector(selector)`
  * `Polymer.dom(parent).querySelectorAll(selector)`

内容API:

  * `Polymer.dom(contentElement).getDistributedNodes()`
  * `Polymer.dom(node).getDestinationInsertionPoints()`

结点变换API:

  * `Polymer.dom(node).setAttribute(attribute, value)`
  * `Polymer.dom(node).removeAttribute(attribute)`
  * `Polymer.dom(node).classList`

操作子结点时使用这些结点变换API可以确保可将内部节点动态分布.如果 **没有** 使用`Polymer.dom`API而修改了影响分布的标记或类,在宿主组件上调用`distributeContent`来强制更新它的分布.

### 使用local DOM

每个Polymer组件都有一个`this.root`属性作为local DOM树的根结点.可以用`Polymer.dom`方法来操作树:

```js
// Append to local DOM
var toLocal = document.createElement('div');
Polymer.dom(this.root).appendChild(toLocal);

// Insert to the local DOM
var toInsert = document.createElement('div');
var beforeNode = Polymer.dom(this.root).childNodes[0];
Polymer.dom(this.root).insertBefore(toLocal, beforeNode);
```

You can use the [automatic node finding](#node-finding) feature to locate
local DOM nodes:

```js
var item = document.createElement('li');
Polymer.dom(this.$.list).appendChild(item);
```

You can also locate nodes in the local DOM using `querySelector`,
`querySelectorAll`, or the `$$` utility method:

```js
var cancelButton = Polymer.dom(this.root).querySelector('#cancelButton');

// Shorthand for finding a local DOM child by selector
// (equivalent to the above):
this.$$('#cancelButton');
```

### 使用light DOM子结点 {#light-dom-children}

当创建有light DOM子结点的自定义组件时,经常需要命令式的与子结点进行交互.

同属性和方法类似,组件可以使`Polymer.dom(this).children`来访问它的light DOM子结点. 然后大多数时间只关心light DOM元素在插入点是如何分布的.

如果组件有local DOM且包含一个或多个插入点
(`<content>`标签),就可以查询在给定插入点上分布的[_分布子元素结点_]集合(#distributed-children).

某些情况下分布节点可能不是想要的.例如:

*   组件没有shadow DOM.
*   需要的组件 **没有** 被分布到任意插入点上.
*   想要查看所有的子元素,无率被分布到了哪个插入点上.

这时候,需要组件子结点的一个列表.
[_有效子结点_ API](#effective-children)是一种有用的方式来访问light DOM子结点,无论它们被分布了组件哪个插入点上.

```html
<dom-module id="simple-content">
  <template>
    <content id="myContent"></content>
  </template>
  <script>
    Polymer({
      is: 'simple-content',
      ready: function() {
        var distributed = this.getContentChildren('#myContent');
        console.log(distributed.length);
      }
    });
  </script>
</dom-module>
```

#### 有效子结点 {#effective-children}

有效子结点是组件的local DOM子结点集合, _在任意插入点上以各自分布式的子结点代替._

考虑一个没有local DOM的简单图片轮播组件. 可使用如下:

```html
<simple-carousel>
  <img src="one.jpg">
  <img src="two.jpg">
  <img src="three.jpg">
<simple-carousel>
```

轮播在当前图片添加了选择点让用户选取不同的图片,所以轮播需要知道它有多少个子元素.
非常简单:轮播在它的`attached`回调中检查自己的子元素:

```js
attached: function() {
  this.childCount = Polymer.dom(this).children.length;
  // do something with childCount ...
}
```

但是还有一些问题.如果创建一个新组件`<popup-carousel>`,新组件在local DOM中包含了一个简单的轮播呢?使用新组件如下:

```html
<popup-carousel>
  <img src="one.jpg">
  <img src="two.jpg">
</popup-carousel>
```

popup-carousel在内部做了以下工作:

```html
<dom-module id="popup-carousel">
  <template>
    <simple-carousel>
      <content></content>
    </simple-carousel>
  </template>
  ...
</dom-module>
```

popup carousel简单传递它的子元素给包含一个`<content>`标签的simple carousel. 但是现在simple carousel的`attached`方法不管用: `Polymer.dom(this).children.length`总是返回1,
由于carousel只有一个`<content>`标签作为它的子元素.

很显然`children`不是我们想要的.想要一个元素中的`<content>`标签被它的分布式子元素所替换的元素列表.
不幸的是平台对基本类型不支持这样做,所以Polymer必须在它的DOM API中添加"有效子元素".

访问组件的有效子元素节点使用如一代码:

```js
var effectiveChildren = Polymer.dom(element).getEffectiveChildNodes();
```

为了方便起见,在Polymer组件原型中还提供了一些实用方法:

*   `getEffectiveChildNodes()`. 返回组件中的有效子元素结点列表.
*   `getEffectiveChildren()`. 返回组件上的有效子元素列表.
*   `queryEffectiveChildren(selector)`. 返回匹配<var>selector</var>的第一个有效子元素.
*   `queryAllEffectiveChildren(selector)`. 返回匹配<var>selector</var>的有效子元素列表.

将`children`替换为`getEffectiveChildren`方法可以得到需要的结果:

```js
this.childCount = this.getEffectiveChildren().length;
```

可以把`getEffectiveChildren`看作一个精心合成的`children`版本.

### 观察已添加或已删除的子元素 {#observe-nodes}

使用DOM API的`observeNodes`方法来跟踪从组件上已添加和已删除的子元素:

```js
this._observer =
    Polymer.dom(this.$.contentNode).observeNodes(function(info) {
  this.processNewNodes(info.addedNodes);
  this.processRemovedNodes(info.removedNodes);
});
```

为`observeNodes`传递一个回调以便在节点添加后或删除后被调用. 回调接受一个简单的带有`addedNodes`和`removedNodes`数组的对象参数.

方法返回一个句柄可用来停止观察:

```js
Polymer.dom(node).unobserveNodes(this._observer);
```

`observeNodes`方法根据所观察的节点不同其行为有一些差别:

*   如果被观察节点是一个 _内容节点_, 回调的调用发生在内容节点的 _分布式子元素_ 改变后.
*   对于其它节点,回调在节点的[_有效子元素_](#effective-children)改变后被调用.

对于`observeNodes`需要注意的一些地方:

*   由于方法被附加到了DOM API, 回调调用中使用`this`来表示所观察的节点.如果使用:

    ```js
    this._observer = Polymer.dom(this.$.content).observeNodes(_childrenChanged);
    ```

    回调调用中以`this.$.content`作为`this`值.如果需要使用自定义组件作为`this`值,就需要绑定回调:

    ```js
    var boundHandler = this._childNodesChanged.bind(this);
    this._observer = Polymer.dom(this.$.content).observeNodes(boundHandler);
    ```

*   回调参数列出了已添加和已删除的节点, 不只是组件.
    如果只关心组件就可以使用如下代码来过滤节点列表:

    ```js
    info.addedNodes.filter(function(node) {
      return (node.nodeType === Node.ELEMENT_NODE)
    });
    ```

*   在`observeNodes`中的第一个回调T包含了 **所有** 添加到组件的节点, _不只是_ 在`observeNodes`调用之后添加的元素. 如果单独使用`observeNodes`也是同样的.

    如果要同步处理组件的子元素 --例如,在`attached`中使用`observeNodes`来监视子元素列表的更改,就要注意了.


#### 为什么不只是一个变换观察器?

如果对变换观察器熟的话,为什么不使用一个变换观察器来处理DOM更改.

简单情况下可以使用一个变换观察器来探测组件上的子元素被添加或删除.然后,变换观察器同`children`列表一样的限制:它们没有映射到local DOM分布中.
在`<popup-carousel>`示例中, 添加一个子元素到`<popup-carousel>`并不会在`<simple-carousel>`上触发一个变换观察器.

为了检测这些更改, `<simple-carousel>`就要检测它的`<content>`结点列表.如果在`children`中找到一个`<content>`结点,
就需要添加 _另一个_ 更改观察器到shadow宿主上(此处为`popup-carousel`)等等.这样一来`<simple-carousel>`就不再那么简单了.

`observeNodes`方法处理了复杂性.它内部使用变换观察器来跟踪DOM更改并处理为了跟踪分布式local DOM所需的额外记录.不同于变换观察器,
`observeNodes`回调只在节点添加或删除后调用—它并不处理标记更改或数据更改.

### DOM API示例

一些使用`Polymer.dom`的例子.

添加一个子元素到light DOM:

```js
var toLight = document.createElement('div');
Polymer.dom(this).appendChild(toLight);
```

获取light DOM上的所有`<span>`组件.

```js
var allSpans = this.queryAllEffectiveChildren('span');
```

可以在任意节点上使用`Polymer.dom`, 不论节点有没有一个local DOM树:

```js
<template>
  <div id="container">
     <div id="first"></div>
     <content></content>
  </div>
</template>

...

var insert = document.createElement('div');
Polymer.dom(this.$.container).insertBefore(insert, this.$.first);
```

## 删除空文本的节点 {#strip-whitespace}

添加`strip-whitespace`布尔标记到模板来删除模板内容中任意的空文本结点. 这样可以获得一些性能的提升.

有空文本节点:

```html
<dom-module id="has-whitespace">
  <template> <div>A</div> <div>B</div> </template>
  <script>
    Polymer({
      is: 'has-whitespace',
      ready: function() {
        console.log(Polymer.dom(this.root).childNodes.length); // 5
      }
    });
  </script>
</dom-module>
```

无空文本节点:

```html
<dom-module id="no-whitespace">
  <template strip-whitespace>
    <div>A</div>
    <div>B</div>
  </template>
  <script>
    Polymer({
      is: 'no-whitespace',
      ready: function() {
        console.log(Polymer.dom(this.root).childNodes.length); // 2
      }
    });
  </script>
</dom-module>
```
