---
title: 为组件编写文档
---

<!-- toc -->

可以通过在源码中添加文档注释的方法来编写Polymer组件的API文档.使用`iron-component-pages`组件来创建一个简单的通过解析注释来渲染的API文档页面.

## 为组件库添加一个文档页面

Polymer CLI'组件项目模板自带使用
[`iron-component-page`](https://github.com/PolymerElements/iron-component-page)生成的文档.
使用这个模板来开发你的组件是一个很好的开始.
查看:

*   [创建一个组件项目](polymer-cli#element)

下面为_不是_通过Polymer CLI来创建的已有项目添加一个文档页面:

1.  添加`iron-component-page`，作为项目的开发依赖:

    ```
    bower install --save-dev PolymerElements/iron-component-page
    ```

2.  项目根目录新建一个`index.html`文件.

3.  从以下位置的`index.html`文件中复制文件内容到新建的`index.html`文件中.

    [组件文档页面模板](https://raw.githubusercontent.com/yeoman/generator-polymer/master/el/templates/index.html)

默认情况下, `iron-component-page`假设只有一个和文件夹同名的引用(例如, `my-element/my-element.html`).

添加多个文档文件:

1.  新建一个文件来引用所有的文档:

    ```
    <!-- all-imports.html -->
    <link rel="import" href="my-element-one.html">
    <link rel="import" href="my-element-two.html">
    ```

2.  编辑`index.html`.

3.  添加`src`属性到`iron-component-page`实例, 并指定上一步新建的文档名为值:

    ```
    <iron-component-page src="all-imports.html"></iron-component-page>
    ```

## 查看组件文档

使用Polymer CLI[`polymer serve`](polymer-cli#serve)命令来预览组件文档.

查看组件文档:

1.  运行`polymer serve`.

2.  在浏览器中访问根目录下的`index.html`:

    <pre><code>localhost:8080/components/<var>my-el</var>/</code></pre>

    <code><var>my-el</var></code>是组件的名称.

全部配置正确的话, 就可以看到组件的文档页面了，就算是没有添加任意文档注释也可以看到页面.

如果有多个组件或行为,使用左上角的下拉菜单来选择文档页面.

**发布API文档.** 查看[创建一个可重用的组件Create a reusable element](/1.0/docs/tools/reusable-elements#publish)
可获取如布组件到Github以及使用GitHub pages来发布API文档的更多信息.
{ .alert .alert-info }

## 编写组件文档

使用内联的HTML或JavaScript来添加组件的API文档内容.

### 组件简介
<p class="tldr">
  提供元素的功能和提供的全面概述
   常见示例。 将文档格式化为
   markdown.
</p>

如果组件使用`<dom-module>`来声明,使用HTML注释编写文件会**立即**处理`<dom-module>`

```
<link rel="import" href="../polymer/polymer.html">

<!--
`<awesome-sauce>` injects a healthy dose of awesome into your page.

In typical use, just slap some `<awesome-sauce>` at the top of your body:

    <body>
      <awesome-sauce></awesome-sauce>

Wham! It's all awesome now!
-->
<dom-module id="awesome-sauce">
```

注意文档注释应在所有依赖的**后边*.

如果组件没有`<dom-module>`, 使用**JavaScript注释**编写文档会立即触发`Polymer()`调用:

```
/**
 * `<awesome-sauce>` injects a healthy dose of awesome into your page.
 *
 * In typical use, just slap some `<awesome-sauce>` at the top of your body:
 *
 *     <body>
 *       <awesome-sauce></awesome-sauce>
 *
 * Wham! It's all awesome now!
 */
Polymer({
  is: 'awesome-sauce',
```

可以使用Markdown headings来分割组件的过长说明内容:

    ### 辅助功能

在组件说明的**底部**添加组件级别的标记来做为注释块的一部分. 现在支持两个标签:

*   `@hero`. 指定一个人物图片.

        @hero path/to/image

*   `@demo`. 使用可选的路径和说明参数来指定一个demo. 没有路径和说明的话，使用标准的demo路径(`./demo/`).

        @demo

        @demo path/to/demo1.html  Super cool demo, with sharks!
        @demo path/to/demo2.html  Even cooler demo. The sharks have lasers!

其它的标记都会被忽略.

注释块中的第一个标记作为组件概要的结尾. **所有以@开始的行都被解析为一个标记.** 其它剩余没有标记的注释块都被忽略.

### 属性

<p class="tldr">
  记录所有的公开属性. 文档最好以一个简短的说明开始.确保属性的类型被批注.
</p>

例如, 一个最简单的属性文档可以是一行:

```js
/** Whether this element is currently awesome. */
isAwesome: Boolean,
```

如果属性没有指定类型或类型不是基本类型,
确保类型[批注](#type-annotation)是正确的:

```js
/**
 * Metadata describing what has been made awesome on the page.
 *
 * @type {{elements: Array<HTMLElement>, level: number}}
 */
sauce: Object,
```

私有属性应当以下划线(`_`)开头:

```js
/** An awesome message */
_message: String,
```

### 方法

遵循[属性指南](#properties). 另外要确保所有参数和返回值的类型都有说明Additionally, make sure
the types for all params and return values are documented.
{ .tldr }

例如:

```js
/**
 * Applies awesomeness to `element`.
 *
 * @param {HTMLElement} element The element to be made awesome.
 * @param {number} level The numeric level of awesomeness. A value
 *     between `1` and `11`.
 * @param {Array<HTMLElements>=} refs Optional referenced elements
 *     that become awesome by proxy.
 * @return {number} The cumulative level of awesomeness.
 */
makeAwesome: function makeAwesome(element, level, refs) {
```

### 事件

<p class="tldr">
  事件必须以一个<code>@event</code>标记做显式批注.
</p>

事件属性用`@param`标记，和方法参数一样.

例如:

```js
/**
 * Fired when `element` changes its awesomeness level.
 *
 * @event awesome-change
 * @param {number} newAwesome New level of awesomeness.
 */
```

### 行为

<p class="tldr">同组件一样但是要添加<code>@polymerBehavior</code>.</p>

包含一个行为说明, 同组件说明一样, 但是要以一个
`@polymerBehavior`标记结尾. 行为名称需要显式指定如果文档解析器不能正确推断.

    @polymerBehavior MyOddBehavior

文档方法、属性等.

例如:

```js
/**
 * Behavior that highlights stuff.
 *
 * @polymerBehavior
 */

MyBehaviors.HighlightStuff = { ... }
```

当扩展一个行为时, 在类中实现一个_新_ 功能
参照[行为扩展](/1.0/docs/devguide/behaviors#extending).

实现类**必须**把行为名跟在`Impl`的后边来命名, 还要用`@polymerBehavior`做批注 _跟在真正的行为名称之后_:

```js
/**
 * Extended behavior.
 *
 * @polymerBehavior SuperBehavior
 */
MyBehaviors.SuperBehaviorImpl = { ... }
```

实际的行为是一组行为的数组并以实现类结尾. 必须用`@polymerBehavior`做批注:

```js
/**
 * @polymerBehavior
 */
MyBehaviors.SuperBehavior =
    [MyBehaviors.BaseBehavior, MyBehaviors.SuperBehaviorImpl]
```

文档系统会把这些说明都合并到一个行为中
(此处为`MyBehaviors.SuperBehavior`).

### 自定义CSS属性和mixins

现在还没有自定义CSS属性和mixins的标记. 文档属性和mixins在主组件说明中的表格:

    ### Styling

    `<paper-button>` provides the following custom properties and mixins
    for styling:

    Custom property | Description | Default
    ----------------|-------------|----------
    `--paper-button-ink-color` | Background color of the ripple | Based on the button's color
    `--paper-button` | Mixin applied to the button | `{}`

确保把表格放组件说明中任何组件级别标记的前面.

### 类型批注

查看[Closure兼容的类型表达式](https://developers.google.com/closure/compiler/docs/js-for-compiler#types).
{ .tldr }

### 语言

<p class="tldr">
  使用第三人称表达并尽量保持简单.
</p>

一些保持一致性的指南:

* 使用第三人称表达.

  * 推荐. "Creates a foo."
  * 避免. "Create a foo."

  Use 2nd person ("Do this...") when you're _trying_ to be prescriptive,
  such as, "**Add** the `toolbar` attribute to the element you want
  to use as a toolbar."

* 尽可能使用现在时.

  * 推荐. "Clicking the element starts an animation."
  * 避免. "Clicking the element will start an animation."

* 使用主动动词进行方法说明.

  * 推荐. "Starts the animation."
  * 避免. "This method to starts the animation."

* 使用短语特别是短文字中.

  * 推荐. "Item height, in pixels."
  * 避免. "This property specifies the item height, in pixels."

  (短语应大写并以标点结束.)

[JavaDoc Style Guide](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html#styleguide)
是一个很少的API文档风格资源. 其中的大多数风格规则在这里同样适用.


