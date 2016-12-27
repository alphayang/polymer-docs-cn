---
title: 设置Local DOM的样式
---

<!-- toc -->

Polymer使用[Shadow DOM样式规则](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom-201/)为组件的local DOM提供内部样式支持.内部样式应以  `<style>`标记位于组件的local DOM `<template>`中.

```html
<dom-module id="my-element">

  <template>

    <style>
      :host {
        display: block;
        border: 1px solid red;
      }
      #child-element {
        background: yellow;
      }
      /* styling elements distributed to content (via ::content) requires */
      /* selecting the parent of the <content> element for compatibility with */
      /* shady DOM . This can be :host or a wrapper element. */
      .content-wrapper ::content > .special {
        background: orange;
      }
    </style>

    <div id="child-element">In local DOM!</div>
    <div class="content-wrapper"><content></content></div>

  </template>

  <script>

      Polymer({
          is: 'my-element'
      });

  </script>

</dom-module>
```

想把样式放到组件外或在组件之间共享样式就需要创建一个[样式模块](#style-modules).

**注意:**  在Polymer 1.1之前,推荐将`<style>`标记放在组件的`<dom-module>`内(且在`<template>`之_外_).这个还是支持的但已经不推荐了.
{.alert .alert-info}


### 为分散的子结点设置样式 (::content)

在shady DOM中, `<content>`标记不会出现在DOM tree里. 样式会被重写,去掉了
`::content`伪组件, **所有组合器立即属于左边`::content`.**

这意味着:

*   必须在`:: content`伪元素的左侧有一个选择器.

    ```css
    :host ::content div
    ```

    Becomes:

    ```css
    x-foo div
    ```

    (Where `x-foo` is the name of the custom element.)

*   为了将样式限制在::content标记内部,需要在`<content>`组件旁添加一个包装组件. 这在使用子组合器（`>`）来选择顶级子级时尤其重要.

    ```html
    <dom-module id="my-element">

      <template>

        <style>
          .content-wrapper ::content > .special {
            background: orange;
          }
        </style>

        <div class="content-wrapper"><content></content></div>

      </template>

    </dom-module>
    ```

    In this case, the rule:

    ```css
    .content-wrapper ::content > .special
    ```

    Becomes:

    ```css
    .content-wrapper > .special
    ```

**自定义属性不能设置分散子级的样式.** Polymer的
[自定义属性](#xscope-styling-details)隔离不支持为分散子级设置样式 doesn't support styling
distributed children.
{.alert .alert-info}


## 跨范围样式 {#xscope-styling}


### 背景

Shadow DOM (和Shady DOM)带来的一大好处将样式封装和范围带到了web开发中,使得应用中CSS部分变得更安全易用.  这样组件local DOM内部和外部以及组件之间的样式就不会泄露了.

这样可以有效*保护*范围不被不需要的样式泄露影响.  当希望组件的用户希望*自定义* 组件
local DOM的样式呢?就需要使用
"主题"了.  例如一个"custom-checkbox"组件内部使用了
`.checked`类来保护自己不被其它组件CSS中的`.checked`所影响.  然而其实做为使用checkbox的用户其实是想改变单选框的颜色来匹配自己的品牌的.  Shadow DOM中提供了同样的"保护"机制来解决这个"主题"问题.

**已弃用的shadow DOM选择器.** Shadow DOM规范的作者通过使用`/deep/`选择器 and `::shadow`
伪组件为这种主题问题提供了一种解决方案,这种方案允许编写跨越Shadow DOM封装边界的规则. 然而这种方案是有问题的所以被弃用了.
{.alert .alert-info}

<!-- retain legacy anchor -->
<a id="xscope-styling-details"></a>

### 自定义CSS属性 {#custom-css-properties}

Polymer为自定义CSS样式包含了一个隔离,这个是受到了(and compatible with)
未来W3C[CSS Custom Properties for Cascading Variables](http://dev.w3.org/csswg/css-variables/)
规范 (查看
[使用CSS变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables)
on the Mozilla Developer Network)的启发并与之兼容.

与其将组件内部的样式实现暴露出去,还不如由组件的作者在组件的API中定义一个或多个自定义CSS属性.

这些自定义属性可以像其它标准CSS属性属性一样将从所构造的DOM树的定义点继承,就像`color` 和 `font-family`的效果一样.

在下边的简单示例中,`my-toolbar`的作者认识到了工具栏的用户希望可以改变工具栏标题的颜色.作者暴露了一个名为`--my-toolbar-title-color`的自定义属性通过主题组件的选择器来设置`color`属性.  工具栏的用户可以在任意的CSS规则中定义这个变量,变量值会继承给工具栏使用就像其它标准的CSS属性继续一样.

示例: { .caption }

```html
<dom-module id="my-toolbar">

  <template>

    <style>
      :host {
        padding: 4px;
        background-color: gray;
      }
      .title {
        color: var(--my-toolbar-title-color);
      }
    </style>

    <span class="title">{{title}}</span>

  </template>

  <script>
    Polymer({
      is: 'my-toolbar',
      properties: {
        title: String
      }
    });
  </script>

</dom-module>
```

`my-toolbar`用法示例: { .caption }

```html
<dom-module id="my-element">

  <template>

    <style>

      /* Make all toolbar titles in this host green by default */
      :host {
        --my-toolbar-title-color: green;
      }

      /* Make only toolbars with the .warning class red */
      .warning {
        --my-toolbar-title-color: red;
      }

    </style>

    <my-toolbar title="This one is green."></my-toolbar>
    <my-toolbar title="This one is green too."></my-toolbar>

    <my-toolbar class="warning" title="This one is red."></my-toolbar>

  </template>

  <script>
    Polymer({ is: 'my-element'});
  </script>

</dom-module>
```

`--my-toolbar-title-color`属性只作用于`my-toolbar`封装的标题组件颜色的内部实现.将来`my-toolbar`的作者可以重命名`title`类或者重构`my-toolbar`的内部细节而不用改变暴露给用户的自定义属性.

同样也可以为`var()`函数赋默认值, 用于当用户没有设置自定义属性时:

```css
color: var(--my-toolbar-title-color, blue);
```

由此,自定义CSS属性提供了一种强大的能力可以让组件作者为用户提供像正常的CSS样式一样的主题API. 它已经做为一个标准(目前处理候选推荐阶段)被Firefox, Safari, 和 [Chrome 49](https://www.chromestatus.com/features/6401356696911872)所支持.

### 自定义CSS混合

这可能是乏味的（或不可能的）来让组件作者预测所有的重要CSS属性,更不用说单独提供每一个属性.

自定义属性隔离包含一个扩展可以让组件的作者定义一组CSS属性作为一个单独的自定义属性,并允许组中的属性作用于组件local DOM的特定CSS规则.扩展使用混合功能来提供这种类似于`var`的能力, 但是它允许全部属性可以被混合. 此扩展拥护
[CSS @apply rule](http://tabatkins.github.io/specs/css-apply-rule/)
提案.

使用`@apply`来应用一个混合:

```
@apply(--<var>mixin-name</var>);
```

定义一个混合就好象定义一个自定义属性, 只是它的值是一个定义了一个或多个规则的对象:

```
selector {
  --mixin-name: {
    /* rules */
  };
}
```

Example: { .caption }

```html
<dom-module id="my-toolbar">

  <template>

    <style>
      :host {
        padding: 4px;
        background-color: gray;
        /* apply a mixin */
        @apply(--my-toolbar-theme);
      }
      .title {
        @apply(--my-toolbar-title-theme);
      }
    </style>

    <span class="title">{{title}}</span>

  </template>

  ...

</dom-module>
```

`my-toolbar`使用示例: { .caption }

```html
<dom-module id="my-element">

  <template>

    <style>
      /* Apply custom theme to toolbars */
      :host {
        --my-toolbar-theme: {
          background-color: green;
          border-radius: 4px;
          border: 1px solid gray;
        };
        --my-toolbar-title-theme: {
          color: green;
        };
      }

      /* Make only toolbars with the .warning class red and bold */
      .warning {
        --my-toolbar-title-theme: {
          color: red;
          font-weight: bold;
        };
      }
    </style>

    <my-toolbar title="This one is green."></my-toolbar>
    <my-toolbar title="This one is green too."></my-toolbar>

    <my-toolbar class="warning" title="This one is red."></my-toolbar>

  </template>

  <script>
    Polymer({ is: 'my-element'});
  </script>

</dom-module>
```

### Polymer组件的自定义属性API {#style-api}

Polymer自定义属性隔离在创建组件时计算和应用自定义属性.为了让组件(或子树)在诸如应用CSS类生更改时重新计算自定义属性值,需要调用组件的[`updateStyles`](/1.0/docs/api/Polymer.Base#method-updateStyles)
. 需要更新页面上的所有组件时可以调用
`Polymer.updateStyles`.

还可以通过设置键值对[`customStyle`](/1.0/docs/api/Polymer.Base#property-customStyle)的形式来修改一个Polymer组件(如同设置`style`)的自定义属性,然后调用`updateStyles`.
或者传递一个属性名和值的字典作为参数给
`updateStyles`.

为了获取组件上的一个自定义属性值,可以使用
[`getComputedStyleValue`](/1.0/docs/api/Polymer.Base#method-getComputedStyleValue).


例如: { .caption }

```html
<dom-module id="x-custom">

  <template>

    <style>
      :host {
        --my-toolbar-color: red;
      }
    </style>

    <my-toolbar>My awesome app</my-toolbar>
    <button on-tap="changeTheme">Change theme</button>

  </template>

  <script>
    Polymer({
      is: 'x-custom',
      changeTheme: function() {
        this.customStyle['--my-toolbar-color'] = 'blue';
        this.updateStyles();
      }
    });
  </script>

</dom-module>
```

### 自定义属性隔离的限制

在Polymer中跨平台的自定义属性支持由一个**近似**CSS变量规范的JavaScript库提供，规范不仅定*义了自定义组件样式的使用*同时还扩展到了使用混合属性来设置规则
. 由于属性原因, Polymer **不试图实现所有原生自定义属性.**
隔离在在CSS全面动态的可行性和实用性上做了权衡.

以下是当前隔离的限制. 动态和性能会得到持续的改进.

#### 动态限制

匹配组件的属性定义只在*创建时*被应用.
任何动态更改属性值都不会自动应用.可以在Polymer组件上调用
[`updateStyles`](/1.0/docs/api/Polymer.Base#method-updateStyles)方法来强制重新计算和更新, 或者使用`Polymer.updateStyles`来更新所有组件的样式.

例如在组件中添加如下标记:

HTML: { .caption }

```html
<div class="container">
  <x-foo class="a"></x-foo>
</div>
```

CSS: { .caption }

```css
/* applies */
x-foo.a {
  --foo: brown;
}
/* does not apply */
x-foo.b {
  --foo: orange;
}
/* does not apply to x-foo */
.container {
  --nog: blue;
}
```

在`x-foo`上添加了`b`类后,宿主组件必须调用 `this.updateStyles`
来应用这个新样式. 这个重新计算和应用将从这个入口处向DOM树传播.

动态效果**将**影响这个属性中的全部内容.

在下例中, 在`#title`组件上添加/删除`highlighted`类会得到预想的效果, 由于动态作用于自定义属性的全部内容.

```css
#title {
  background-color: var(--title-background-normal);
}

#title.highlighted {
  background-color: var(--title-background-highlighted);
}
```

#### 继承限制

不同于正常的CSS从上往下的继承顺序, Polymer隔离中的自定义属性只在所继承的自定义组件设置了所属范围或`:host`范围的规则时可以被改变.  **在组件的local DOM范围内,自定义属性只能有一个值.**  在隔离范围内计算属性的更改是一个耗资源的工作,所以隔离不需要实现跨范围自定义元素的样式,尽管这是隔离的主要功能.

```html
<dom-module id="my-element">

  <template>

    <style>
     :host {
       --custom-color: red;
     }
     .container {
       /* Setting the custom property here will not change */
       /* the value of the property for other elements in  */
       /* this scope.                                      */
       --custom-color: blue;
     }
     .child {
       /* This will be always be red. */
       color: var(--custom-color);
     }
    </style>

    <div class="container">
      <div class="child">I will be red</div>
    </div>

  </template>

  <script>
    Polymer({ is: 'my-element'});
  </script>

</dom-module>
```

#### 不支持分离组件的样式

自定义属性隔离不支持对分离组件的样式.

```css
/* Not supported */
:host ::content div {
  --custom-color: red;
}
```

## 文档样式的自定义组件 (custom-style) {#custom-style}


Polymer提供了一个 `<style is="custom-style">`自定义组件用来定义**主文档的**的样式,它利用了Polymer样式系统的几个特性:

*   在一个`custom-style`中定义的文档样式是隔离的,确保它们不会泄漏到local DOM当浏览器不支持Shadow DOM时.

*   Polymer的自定义属性
    [跨范围样式隔离](#xscope-styling-details)可以用一个
    `custom-style`来定义.使用`:root`选择器来定义作用于所有自定义组件的自定义属性.

*   为了向后兼容, 已弃用的`/deep/`组合和`::shadow`伪组件在不支持原生Shadow DOM的浏览器中做了隔离.在新的代码中应避免使用它们.


示例: { .caption }

```html
<!doctype html>
<html>
<head>
  <script src="components/webcomponentsjs/webcomponents-lite.js"></script>
  <link rel="import" href="components/polymer/polymer.html">

  <style is="custom-style">

    /* Will be prevented from affecting local DOM of Polymer elements */
    * {
      box-sizing: border-box;
    }

    /* Use the :root selector to define custom properties and mixins */
    /* at the document level  */
    :root {
      --my-toolbar-title-color: green;
    }

  </style>

</head>
<body>

    ...

</body>
</html>
```

在定义Polymer组件的的样式时所有的`custom-style`特性都可用(例如 `<style>`组件中有一个
`<dom-module>`自定义组件). 除了`:root`选择器, 只在文档级别有用. **`custom-style`扩展应当只被用于在一个自定义组件local DOM外定义文档样式.**

## 共享的样式和外部样式表 {#style-modules}

为了在组件之间共享样式, 可以在一个`<dom-module>`组件内打包一个样式集合.在这一节里,
用了一个名为_style module_的`<dom-module>`来控制样式.

一个样式模块声明了一组样式规则可以被引入到一个组件的定义中, 或者引入到一个`custom-style`组件中.

**注意:** 样式模块在Polymer 1.1中被引入;
它替换了对[外部样式表](#external-stylesheets)的实验性支持.
{.alert .alert-info}

使用`<dom-module>`组件在一个HTML引用中定义一个样式模块.

```html
<!-- shared-styles.html -->
<dom-module id="shared-styles">
  <template>
    <style>
      .red { color: red; }
    </style>
  </template>
</dom-module>
```

`id`属性可在共享样式中作为引用. 样式模块名称秘组件的命令空间相同,所以样式模块必须用唯一名称.

使用共享样式为分两步: 需要用一个`<link>`标记来
_引入_ 模拟,再用一个`<style>`标记在合适的地方来_包含_ 样式.

在组件中使用样式模块:

```html
<!-- import the module  -->
<link rel="import" href="../shared-styles/shared-styles.html">
<dom-module id="x-foo">
  <template>
    <!-- include the style module by name -->
    <style include="shared-styles"></style>
    <style>:host { display: block; }</style>
    Hi
  </template>
  <script>Polymer({is: 'x-foo'});</script>
</dom-module>
```

还可以在`custom-style`组件中使用共享样式模块.

```html
<!-- import the shared styles  -->
<link rel="import" href="../shared-styles/shared-styles.html">
<!-- include the shared styles -->
<style is="custom-style" include="shared-styles"></style>
```

一个样式标记既可以`include`共享样式也可以定义本地规则:

```html
<style include="shared-styles">
  :host { display: block; }
</style>
```

(对于`custom-style`组件和自定义组件内的`<style>`标记都有效.) 共享样式在内部定义了`<style>`标记的组件之前被应用,所以共享样式可以被内部样式覆盖.

### 外部样式表(已弃用) {#external-stylesheets}

**弃用功能.** 此实验性功能已被弃用请使用
[样式模块](#style-modules). 虽然依然被支持但未来就不支持了.
{.alert .alert-info}

Polymer包含了一个实验性功能来支持加载外部样式表并应用于组件的local DOM.开发者可以用来分离样式,组件间共享通用样式或使用样式预处理工具.由于功能使用了HTML Imports(或适当时使用HTML Import polyfill)来加载样式表内容所以语法有点不同于经典的样式加载,作为一个内联样式有可能被隔离或拒绝.

为了包含一个远程样式表并应用到Polymer组件的local DOM,添加一个特殊的带有`type="css"`的HTML引入`<link>`标记到要加载外部样式的`<dom-
module>`中.

示例: { .caption }

```html
<dom-module id="my-awesome-button">

  <!-- special import with type=css used to load remote CSS
       Note: this style of importing CSS is deprecated -->
  <link rel="import" type="css" href="my-awesome-button.css">

  <template>
    ...
  </template>

  <script>
    Polymer({
      is: 'my-awesome-button',
      ...
    });
  </script>

</dom-module>
```

## 第三方库修改local DOM {#scope-subtree}

如果使用第三方库为Polymer组件添加local DOM结点, 需要注册组件的样式会更新不正确.

正确的做法是通过Polymer的DOM API](local-dom#dom-api)为Polymer组件的local DOM添加结点. 此API可以像local DOM一样操作结点并确保样式被正确更新.

使用第三方库时**不要用**Polymer DOM
API, 使用[`scopeSubtree`](/1.0/docs/api/Polymer.Base#method-scopeSubtree)
方法来应用CSS范围到结点和它的所有子结点上.

1.  在组件的local DOM内创建一个容器结点, 让第三方库在容器结点内创建DOM.

    ```html
    <dom-module is="x-example">
      <template>
        <div id="container">
        </div>
      </template>
    </dom-module>
    ```

2.  在容器结点上调用`scopeSubtree`.

    ```js
    ready: function() {
      this.scopeSubtree(this.$.container, true);
    }
    ```

    `containerNode`是要覆盖DOM树的根结点. 第二个参数设置为`false`来覆盖特定的结点和它的子结点**一次**.
     设置为`true`表示当范围内的任何结点更改时都被持续应用.

**不适用于Polymer组件.** 如果子树包含有带local DOM的Polymer组件, `scopeSubtree`将导致所结点的local DOM样式错误.
{.alert .alert-error}
