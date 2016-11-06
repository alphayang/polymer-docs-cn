---
title: Polymer快览
---

<!-- toc -->

Polymer可以轻松的创建web components.

自定义组件可以利用Polymer的独特优势来减少冗余代码并且可以更容易的创建复杂可交互的组件:

- 组件注册
- 回调生命周期
- 属性观察
- Local DOM模板
- 数据绑定

这一节是Polymer快速入门且不需要安装任何东西. 点击**Edit on Plunker**按钮可以在沙箱环境中与任意示例交互.

点击下面的链接来查看每一项特性.

### 注册一个组件 {#register}

为了注册一个新的叫做`Polymer`的组件,可以在浏览器中使用
_registers_来注册新组件. 注册一个组件关联了一个标记名称和一个原型, 可以添加属性和方法来定制组件. 定制组件的名称T**必须包含一个中划线(-)**.

Polymer函数接受一个定义了组件原型的对象作为参数.

<demo-tabs selected="0" src="http://plnkr.co/edit/NQ4qGEoS24hia4numeLP?p=preview">
  <demo-tab heading="proto-element.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/proto-element/proto-element.html')}}}</code></pre>
  </demo-tab>
  <demo-tab heading="index.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/proto-element/index.html')}}}</code></pre>
  </demo-tab>

  <iframe frameborder="0" src="samples/proto-element/index.html" width="100%" height="40"></iframe>
</demo-tabs>

这个示例使用了回调生命周期为`<proto-element>`初始化时添加内容.
自定义组件完成初始化后就调用`ready`回调函数.
`ready`回调可以用来进行类似构造函数之类的工作.

<p><a href="/1.0/docs/devguide/registering-elements" class="blue-button">
  更多关于: 组件注册
</a></p>

<p><a href="/1.0/docs/devguide/registering-elements#lifecycle-callbacks" class="blue-button">
  更多关于: 回调生命周期
</a></p>

### 添加local DOM

很多组件包含一些内部DOM结点来实现组件UI和功能.
Polymer称之为 _local DOM_,并提供了一种简单的方式来指定它:

<demo-tabs selected="0" src="http://plnkr.co/edit/q3o7yWQq9cevrEy4fA9I?p=preview">
  <demo-tab heading="dom-element.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/dom-element/dom-element.html')}}}</code></pre>
  </demo-tab>
  <demo-tab heading="index.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/dom-element/index.html')}}}</code></pre>
  </demo-tab>

  <iframe frameborder="0" src="samples/dom-element/index.html" width="100%" height="40"></iframe>
</demo-tabs>

Local DOM被封装在组件的内部.

<p><a href="/1.0/docs/devguide/local-dom" class="blue-button">Learn more: local DOM</a></p>

### 使用local DOM

Local DOM让你能够控制_合成_. 组件的子结点可以是_分散的_
因此它们渲染为好像它们被插入到local DOM树中.

本便中创建一个简单的标记,它封装了一个图片在一个格式化过的`<div>`标记中.

<demo-tabs selected="0" src="http://plnkr.co/edit/LClm7BxEaq56395cgn64?p=preview">
  <demo-tab heading="picture-frame.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/picture-frame/picture-frame.html')}}}</code></pre>
  </demo-tab>
  <demo-tab heading="index.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/picture-frame/index.html')}}}</code></pre>
  </demo-tab>

  <iframe frameborder="0" src="samples/picture-frame/index.html" width="100%" height="60"></iframe>
</demo-tabs>

**注意:**CSS样式定义在`<dom-module>`内部属于组件的local DOM_范围_.
所以`div`规则只对位于`<picture-frame>`内的`<div>`标记有效.
{: .alert .alert-info }

<p><a href="/1.0/docs/devguide/local-dom#dom-distribution" class="blue-button">
更多关于: 合成 & 分散</a></p>

### 使用数据绑定

当然只有静态local DOM是远远不够的. 可以让组件来动态更新它的local DOM.

数据绑定D数据绑定是快速传播元素中的更改并减少样板代码的好方法.
可以使用"double-mustache"语法(`{%raw%}{{}}{%endraw%}`)来绑定组件中的属性.
`{%raw%}{{}}{%endraw%}`被替换为在括号之间引用的属性值.

<demo-tabs selected="0" src="http://plnkr.co/edit/IdMTRu1boSjWIA6q7Kj8?p=preview">
  <demo-tab heading="name-tag.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/name-tag/name-tag.html')}}}</code></pre>
  </demo-tab>
  <demo-tab heading="index.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/name-tag/index.html')}}}</code></pre>
  </demo-tab>

  <iframe frameborder="0" src="samples/name-tag/index.html" width="100%" height="40"></iframe>
</demo-tabs>

<p><a href="/1.0/docs/devguide/data-binding" class="blue-button">
关于更多: 数据绑定</a></p>

### 声明一个属性

属性是组件公开API的重要部分. Polymer的
_声明属性_支持大量的通用模式来设置属性默认值,通过标记来配置属性以及观察属性的更改等等.

下面的示例中,定义了一个具有默认值的`owner`属性,
并在`index.html`进行配置.

<demo-tabs selected="0" src="http://plnkr.co/edit/DhDSeqNrmLflcQ8UZI1R?p=preview">
  <demo-tab heading="configurable-name-tag.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/configurable-name-tag/configurable-name-tag.html')}}}</code></pre>
  </demo-tab>
  <demo-tab heading="index.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/configurable-name-tag/index.html')}}}</code></pre>
  </demo-tab>

  <iframe frameborder="0" src="samples/configurable-name-tag/index.html" width="100%" height="40"></iframe>
</demo-tabs>

<p><a href="/1.0/docs/devguide/properties" class="blue-button">
更多关于: 属性声明</a></p>

### 绑定一个属性

除了文本内容还可以绑定一个组件_属性_ (通过
`property-name="{%raw%}{{binding}}{%endraw%}"`). Polymer属性可选支持双向绑定.

本例中使用双向绑定:把自定义输入组件(`iron-input`)的值绑定组件的`owner`属性,以便用户输入时自动更新.

<demo-tabs selected="0" src="http://plnkr.co/edit/OXaNcCl7qkeMwXEtiTxX?p=preview">
  <demo-tab heading="editable-name-tag.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/editable-name-tag/editable-name-tag.html')}}}</code></pre>
  </demo-tab>
  <demo-tab heading="index.html">
<pre><code>{{{include_file('1.0/docs/devguide/samples/editable-name-tag/index.html')}}}</code></pre>
  </demo-tab>

  <iframe frameborder="0" src="samples/editable-name-tag/index.html" width="100%" height="100"></iframe>
</demo-tabs>

**注意:** `is="iron-input"`属性说明这个输入是一个_扩展类型_的自定义组件;组件名为`iron-input`, _扩展_自原生`<input>`组件.
{: .alert .alert-info }

## 下一步

已经理解了Polymer的基本原理, 可以
[创建第一个组件项目](/1.0/start/first-element/intro)或查看更多的开发指南.
