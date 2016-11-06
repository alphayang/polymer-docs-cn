---
title: 什么是Polymer?
---

Polymer库用来帮助开发者可以轻松快捷的为Web创建优秀的可重用组件。

## 自定义组件扩展web

HTML提供了一组内建的组件如`<button>`, `<form>` 和
`<table>`。每一个组件有自己的API，包括 attributes, properties, methods, 和
events. 每个组件也有内建的样式并可以使用CSS来覆盖这个样式属性。

每个人都可以使用这些组件来创建一个简单的web页面。但是这些组件是有限的。为了创建一个简单的tab集合，需要使用HTML和CSS以及script。
利用自定义组件，你就可以使用自己的组件来扩展HTML的语汇。组件可以提供复杂的界面。组件可以这被简单的使用，比如`<select>`:

```html
<my-tabstrip>
  <my-tab>
    Home
  </my-tab>
  <my-tab>
    Services
  </my-tab>
  <my-tab>
    Contact Us
  </my-tab>
<my-tabstrip>
```

## Polymer是web components? 或是组件?

Polymer两者都不是. Polymer是基于web components的标准并用来帮助更好的创建你的自定义组件:

![diagram of web components stack](/images/1.0/webcomponents_stack.svg)

*   **Web components**. 这些标准提供了创建新组件的原始资源。你可以用标准来创建自己的组件，但需要大量的工作。

    现在还不是所有的浏览器都支持这些标准，所以[web components polyfill
    library](http://webcomponents.org/polyfills/)用来填补这个缺陷，利用JavaScript来实现这个APIs.

*   **Polymer库**. 提供了声明式的语法来简化自定义组件的定义。同时添加诸如模板，双向数据绑定和属性观察等特性来帮助用更少的代码构建更强大的可重用组件。

*   **自定义组件**. 如果你想编写自己的组件，Polymer中内建了大量的组件可以直接在你的页面中使用。这些组件都基于Polyer库，不过你也可以直接使用组件，不需要利用Polymer。
你可以混合使用Polymer和其它的自定义组件，当然也包含内建的组件。


## 获取组件

Polymer团队提供了一组组件可以在你的应用中使用。请查看[Element catalog](https://elements.polymer-project.org/).


## 编写自己的

利用Polymer库来编写属于自己的组件？

快速查看特性:

<a href="/1.0/docs/devguide/quick-tour" class="blue-button">Quick Tour</a>

或直接查看:

<a href="/1.0/docs/devguide/feature-overview" class="blue-button">
  Developer Guide
</a>
