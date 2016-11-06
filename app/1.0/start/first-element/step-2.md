---
title: "Step 2: 添加Local DOM"
subtitle: "创建第一个Polymer组件"
---

下面，我们将创建一个简单组件来显示一个图标.

在这一部分，将会学习到:

*   使用Polymer创建一个自定义组件.
*   操作Local DOM.

_Local DOM_ 是组件中的DOM元素. 在这一部分，将学习更多它的知识.

**Local DOM? Shadow DOM?** 如果你熟悉_shadow DOM_,那么local DOM就是一个与shadow DOM相同原理的更通用的一个web标准. 无论浏览器是否支持native shadow DOM,Polymer的local DOM都能正常工作.
{ .alert .alert-info }

## 编辑icon-toggle.html

打开`icon-toggle.html `.这个文件包含自定义组件的框架.

不同于大多数HTML文件, 此文件<em>在浏览器里加载后不会显示任何内容</em>—它只是 <em>定义</em> 了一个新组件. Demo导入
了`icon-toggle.html`所以你可以使用 `<icon-toggle>`
组件. 随着你给组件添加相应的功能，它们都会显示在demo中.

查看现有的代码:


HTML imports代码 { .caption }

```html
<link rel="import" href="../polymer/polymer.html">
<link rel="import" href="../iron-icon/iron-icon.html">
```

主要内容:

*   `link rel="import"`元素是一个<em>HTML import</em>,在HTML文件中引用资源的一种新方式 .
*   引用Polymer library和其它自定义组件
    `iron-icon`,之后可以使用.

**更多: HTML Imports.** [HTML Imports: #include for the web](http://www.html5rocks.com/en/tutorials/webcomponents/imports/)
在HTML5Rocks.com在一个关于HTML Imports更深入的讨论.
{ .alert .alert-info }

下一步是定义组件自身:

—local DOM 模板代码 { .caption }

```html
<dom-module id="icon-toggle">
  <template>
    <style>
      /* local styles go here */
      :host {
        display: inline-block;
      }
    </style>
    <!-- local DOM goes here -->
    <span>Not much here yet.</span>
  </template>
```

主要内容:

*   `<dom-module>`标签包含了组件的local DOM定义.在这里`id`属性表示这个模块中有一个叫`icon-toggle`的组件.
*   `<template>`实际上定义了组件的local DOM结构和样式. 这里可以为自定义组件添加标记.
*   `<style>`组件位于`<template>`中定义了只作用于local DOM<em>范围</em>的样式，不会影响文档其余的部分.
*   `:host` 伪类匹配自定义组件的(在这里是`<icon-toggle>`). 这里包含了local DOM树中的<em>hosts </em>.

**了解更多: local DOM.** Local DOM
让你在组件内添加<em>一个</em>DOM树, 可以使用组件内的样式和标记来与网页的其它部分解耦. Local DOM基于Shadow DOM规范, 支持native shadow DOM.
更多详情,在Polymer库文档中查看<a href="/1.0/docs/devguide/local-dom">Local
DOM</a>.
{ .alert .alert-info }

在组件定义是最后是一些JavaScript用来注册组件. 如果组件包含`<dom-module>`, 那么脚本通常放在`<dom-module>`
<em>内</em>以便统一管理.


element注册代码 { .caption }

```html
  <script>
    Polymer({
      /* this is the element's prototype */
      is: 'icon-toggle'
    });
  </script>
</dom-module>
```


主要内容:

  * `Polymer`调用<em>registers</em>以便被浏览器识别.
  * Polymer调用的参数是新组件的原型. 后面会看到更多相关内容.
  * 原型中`is`属性是新组件的名称. 必须与包含组件模板的`<dom-module>`的`id`相<em>匹配</em>.

### 创建local DOM结构

现在了解了组件的基本结构, 下面添加更多内容到local DOM模板.

在`local DOM goes here`评论下查找`<span>` :

icon-toggle.html—before { .caption }

```html
    <!-- local DOM goes here -->
    <span>Not much here yet.</span>
  </template>
```

 替换`<span>`和它的内容为`<iron-icon>`标签:

icon-toggle.html—after { .caption }

```html
    <!-- local DOM goes here -->
    <iron-icon icon="polymer">
    </iron-icon>
  </template>
```

主要内容:

  * `<iron-icon>`组件负责渲染一个图标. 这里硬编码使用一个叫"polymer"的图标.

### local DOM的样式

有很多新的CSS选择器用来操作local DOM. `icon-toggle.html `文件包含了一个`:host`选择器,正如之前提到的用来定义顶层`<icon-toggle>`组件.

为了自定义中`<iron-icon>`组件, 使用如下CSS在`<style>`标记中:

icon-toggle.html { .caption }

```html
    <style>
      /* local styles go here */
      :host {
        display: inline-block;
      }
      iron-icon {
        fill: rgba(0,0,0,0);
        stroke: currentcolor;
      }
      :host([pressed]) iron-icon {
        fill: currentcolor;
      }
    </style>
```

主要内容:

*   `<iron-icon>`标签使用了一个SVG核动力.`fill`
    和`stroke`属性是SVG特有的CSS属性. 它们设置填充色和轮廓颜色.

*   `:host()`方法匹配宿主组件<em>如果选择器匹配了宿主组件</em>. 此处, `[pressed]`是一个标准的CSS属性选择器,所以当`icon-toggle`有一个`pressed`属性时进行匹配.

更新后的自定义组件:

icon-toggle.html { .caption }

```html
<link rel="import" href="../polymer/polymer.html">
<link rel="import" href="../iron-icon/iron-icon.html">
<dom-module id="icon-toggle">
  <template>
    <style>
      /* local DOM styles go here */
      :host {
        display: inline-block;
      }
      iron-icon {
        fill: rgba(0,0,0,0);
        stroke: currentcolor;
      }
      :host([pressed]) iron-icon {
        fill: currentcolor;
      }
    </style>
    <!-- local DOM goes here -->
    <iron-icon icon="polymer">
    </iron-icon>
  </template>
  <script>
  Polymer({
    is: 'icon-toggle',
  });
  </script>
</dom-module>
```

重新加载demo.

-   如果下载了the starting code,确认`polymer serve`在运行中然后重新加载demo.

-   如果你使用Plunker, 修改会立即生效. 如果没有生效，你可能需要点击**Stop**，然后**Run**来刷新预览.

就可以看到有图标的切换按钮了.

<img src="/images/1.0/first-element/hardcoded-toggles.png" alt="Demo showing icon toggles displaying Polymer icon">

还会看到切换按钮按下时有更改因为`pressed`
属性的生效. 由于没有任何代码来修改`pressed`属性,所以怎么点击切换按钮都不会切换的.


**如果没有看到新的切换,** 再次对照上边的代码进行检查. 如果看到了一个空页面, 确认你在demo目录中或demo/index.html.
{ .alert .alert-info }

<a class="blue-button" href="intro">Previous step: Intro</a>
<a class="blue-button"
    href="step-3">Next step: 使用数据绑定和属性</a>
