---
title: "Step 3: 使用数据绑定和属性"
subtitle: "创建第一个Polymer组件"
---

现在，组件还没有太多功能.下面我们给它添加一个基本的
API, 可以使用它来配置图标,通过标签属性, 或使用JavaScript, 或使用DOM属性.

首先, 了解下数据绑定. 找到`<iron-icon>`组件后修改`icon`attribute，把`"polymer"`改为"`[[toggleIcon]]`".

icon-toggle.html { .caption }

```html
<!-- local DOM goes here -->
<iron-icon icon="[[toggleIcon]]">
</iron-icon>
```

主要内容:

  * `toggleIcon` 是一个<em>属性</em> 用来定义切换按钮组件. 没有默认值.
  * `icon="[[toggleIcon]]" `是一个 <em>数据绑定</em>. 链接了组件的I`toggleIcon`<em>属性</em>和`<iron-icon>`的`icon`属性.

现在可以使用`toggleIcon`属性或JavaScript来定义图标了. 如果在Plunker中,可以在添加了`toggleIcon`绑定后看到效果.想看新图标从哪里来,查看`icon-toggle-demo.html`. (如果是下载了the starting code, 文件位于`demo`目录中. 如果使用
Plunker, 所有文件在同一级中.)

修改后的代码:

icon-toggle-demo.html—existing demo code { .caption }

```html
<icon-toggle toggle-icon="star" pressed></icon-toggle>
```

这些_代码_都不需要修改, 是Demo的一部分. 可以试着添加新的`<icon-toggle>`组件到`icon-toggle-demo.html`文件. 试试
`add`, `menu`, 和 `settings`的图标.

**更多: attribute and property 名称.** 标记使用了toggle-icon`, 而不是 `toggleIcon`. Polymer使用camelCase property名称 
，用中划线分割. 更多信息 <a href="/1.0/docs/devguide/properties#property-name-mapping">Property
name 与 attribute name mapping</a> 在Polymer文档中.
{ .alert .alert-info }

下一步, 为`toggleIcon`属性添加一个声明.

找到script标记并添加如下`properties`对象到组件原型中:

icon-toggle.html { .caption }

```html
<script>
  Polymer({
    /* this is the element's prototype */
    is: 'icon-toggle',
    properties: {
      toggleIcon: String
    }
  });
</script>
</dom-module>
```

主要内容:

  * 如果属性在组件的公开API中，那么在`properties`对象中声明是一个好主意.
  * 简单的属性声明只包含了类型(此处,`String`).



**更多: 反序列化attributes.** 声明的property类型影响Polymer转换或<em>反序列化</em>
attribute值 (字符串值) 为一个JavaScript property值.
默认是`String`,`toggleIcon`中的声明是一个形式.
查看更多 <a href="/1.0/docs/devguide/properties#attribute-deserialization">Attribute
deserialization</a>位于Polymer文档中.
{ .alert .alert-info }

`properties`对象同时支持多个特性. 添加以下代码用来支持`pressed`属性:

icon-toggle.html { .caption }

```js
  Polymer({
    /* this is the element's prototype */
    is: 'icon-toggle',
    properties: {
      toggleIcon: String,
      pressed: {
        type: Boolean,
        value: false,
        notify: true,
        reflectToAttribute: true
      }
    }
  });
```

主要内容:

 *   对复杂的属性, 提供一个包含多个字段的配置对象.
*   `value`定义了property的 [默认值](/1.0/docs/devguide/properties#configure-values).
*   `notify`属性告诉Polymer<em>分发属性值改变的事件
    </em>当属性值改变时. 可以让其它的结点观察到更改.
*   `reflectToAttribute` property告诉Polymer
    [在值更改时更新相应的attribute](/1.0/docs/devguide/properties#attribute-reflection).
    用来使用选择器进行组件样式的更改,比如
    `icon-toggle[pressed]`.


**更多: `notify`和`reflectToAttribute`.**  `notify`和
`reflectToAttribute`属性可能_听起来_ 差不多: 它们都是让组件的状态对外部世界可见. `reflectToAttribute`让状态在**DOM树**中可见,以便在CSS和
`querySelector`方法中使用. `notify` **让状态送货在组件外可见**, 不论是使用JavaScript event handlers还是Polymer
<a href="/1.0/docs/devguide/data-binding#property-notification">双向数据绑定</a>.
{ .alert .alert-info }

现在组件的`pressed`和`toggleIcon`的属性开始工作了.

刷新demo, 可以看到星星和爱心图标替换了之前的硬编码图标:

<img src="/images/1.0/first-element/static-toggles.png" alt="Demo showing icon toggles with star and heart icons">

<a class="blue-button" href="step-2">Previous step: 添加Local DOM</a>
<a class="blue-button" href="step-4">Next step: 输入响应</a>
