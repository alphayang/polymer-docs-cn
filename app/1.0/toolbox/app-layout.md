---
title: 响应式应用布局
---

<!-- toc -->

**应用布局组件处于prerelease阶段.** API可能会改变.
{.alert .alert-info}

**重大改变:** 应用布局包在
[version 0.10](https://github.com/PolymerElements/app-layout/releases/tag/v0.10.0)
中,`<app-toolbar>`组件的`title`标记变为`main-title`, `<app-header>`组件的`primary`标记变为 `sticky`.
{.alert .alert-warning}

每个应用都需要一些布局,应用布局组件提供的工具可以很轻松的创建响应式布局.

如果已经使用了上一代的Material Design布局组件, 如
`paper-header-panel` 和 `paper-drawer-panel`, 那么对于应用布局组件就会感觉很熟悉.
无论以什么形式, 这些组件都被设计为:

- 更好的灵活性和组合性 -- 支持更广泛的布局模式.
- 更少的预设 -- 组件不要求特定的观感
(当你想要Material Design的效果和UI模式时还是会继续支持的).
- 可扩展 -- 滚动效果使用一个新的可插拔系统.

## 设计你的布局

在创建布局前需要进行设计.

-   是否想要一个简单的应用标题可以随着内容滚动,或是一个伴随花式滚动效果的折叠头部?
-   用户如何导航? 标签? 侧边菜单?
-   应用布局在小屏幕上有什么变化?

或许已经有想要实现的设计模型, 或者你想要自己做模型. 如果不确定想要的效果, 可以看看这些内置的应用布局模板来寻找启发:

-   [Simple landing page.](https://polymerelements.github.io/app-layout/templates/landing-page/)
    一个带头部的简单登陆页面.页面顶部的标签跳转到页面中的不同部分.
    这个页面的基本布局在任何尺寸的屏幕上都一样.
-   [Getting started](https://polymerelements.github.io/app-layout/templates/getting-started/).
    一个带简单头部和响应式抽屉的布局.
-   [ZUPERKÜLBLOG](https://polymerelements.github.io/app-layout/templates/publishing/).
    一个博客风格的布局, 类似于Getting Started, 但带有一个固定头部.
-   [Shrine](https://polymerelements.github.io/app-layout/templates/shrine/). An e-commerce-style
    一个电商风格的站点,实现了很多模式, 包括在窄屏上使用导航抽屉来代替标签导航, 在向下滚动时多个工具栏折叠成一个工具栏.

一旦敲定了基本设计, 就开始实现, 从屏幕的顶部开始: 工具栏和头部.


## 工具栏和头部

绝大多数应用在顶部都有头部或工具栏. 头部可以随着内容滚, 可以固定在屏幕顶部, 或在用户滚动时动态变化.
基于不同的需要可以会使用这些组件:

-   想要一个伴随内容滚动的简单头部, 可以使用一个 `<app-toolbar>` 组件. `<app-toolbar>`是一个简单的水平容器来管理控件和标签. 如果需要多行的控件只需使用多个
    工具栏.

-   想要滚动效果 (例如在用户滚动时改变头部的大小), 可以使用
    `<app-header>` 组件.  `<app-header>` 可以包含一个或多个工具栏并管理滚动效果.

### 简单工具栏

简单工具栏随着页面内容的滚动. 只需一点额外的CSS, 就可以将它固定在页面顶部. [sample](http://jsbin.com/haroru/edit?html,output) 使用了一个带有头部的简单工具栏.

`index.html` { .caption }

```
  <head>
    <!-- import latest release version of all components from polygit -->
    <base href="https://polygit.org/components/">
    <script src="webcomponentsjs/webcomponents-lite.js"></script>
    <link rel="import" href="app-layout/app-toolbar/app-toolbar.html">

    <!-- sample-content included for demo purposes only -->
    <link rel="import" href="app-layout/demo/sample-content.html">

    <style is="custom-style">
      body {
        /* No margin on body so toolbar can span the screen */
        margin: 0;
      }
      app-toolbar {
        /* Toolbar is the main header, so give it some color */
        background-color: #1E88E5;
        font-family: 'Roboto', Helvetica, sans-serif;
        color: white;
        --app-toolbar-font-size: 24px;
      }
    </style>
  </head>
  <body>
    <app-toolbar>
      <div main-title>Spork</div>
    </app-toolbar>
    <sample-content size="10"></sample-content>
  </body>
```

![screenshot of a siple app-toolbar](/images/1.0/toolbox/simple-toolbar.png)

工具栏是一个水平flexbox容器, 可以使用常用的flexbox 规则来调整子元素的布局. 带有 `main-title` 标记的子元素会自动适应flex, 所以它会填充容器内的所有额外空间. 如果添加按钮或图标到头部的任意一边, 都会自动添加到工具栏:

```
  <app-toolbar>
    <paper-icon-button icon="menu"></paper-icon-button>
    <div main-title>Spork</div>
    <paper-icon-button icon="search"></paper-icon-button>
  </app-toolbar>
```

![screenshot of a simple app-toolbar with a menu and search buttons](/images/1.0/toolbox/toolbar-with-buttons.png)


### 动态头部

 `<app-header>` 组件是珍上应用滚动效果的容器. 应用头部可以容纳任意类型的组件, 通常是工具栏和标签栏. 多行控件可以使用多个工具栏.

默认情况下, 向下滚动页面时头部滚动关闭, 如同简单的工具栏.
可以给头部添加标记来改变它的默认行为:

- `fixed`.  _fixed header_ 固定在屏幕的顶部.
- `reveals`.  _revealing header_ 在向上滚动时出现, 不论当前内部与头部有多远.
- `condenses`.  _condensing header_ 比通常头部更高, 在向下滚动时垂直收缩. 收缩头部通常有多个工具栏/标签并总是会显示其中的一个 (
    _sticky_ 组件) . 可以搭配使用fixed 或
    revealing 头部.


### 收缩头部

当使用收缩头部配合多个工具栏时可以选择2种技术:

-   所有的工具栏保持在屏幕上但"折叠"在另一个顶部.工具栏内容必须交错以达到不重叠. (在Material Design指南中, 这个模式称作
    flexible space, 并常配合一个或多个滚动效果.)

    ![screenshot of an expanded, tall, app-toolbar with a menu and shop button, and titled My App](/images/1.0/toolbox/collapsing-headers-open.png)
    ![screenshot of the same toolbar collapsed to a regular, smaller size, with the same title and buttons](/images/1.0/toolbox/collapsing-headers-closed.png)

-   顶层的工具栏移出屏幕从而让下部的工具栏保持在屏幕上. (在
    Material Design模式中, 底部的工具栏通常是标签栏或搜索栏t.)

    ![screenshot of an expanded, tall app-toolbar with a back and shop buttons, titled Spork. Below it are three tabs, labelled food, drink, life](/images/1.0/toolbox/spork-tabs-tall.png)
    ![screenshot of the same app-toolbar, but with the title and the buttons gone, and only with the 2 tabs visible](/images/1.0/toolbox/spork-tabs-condensed.png)


其中的一个工具栏被定义为`sticky`. 当页面滚动时,任意在固定工具栏 _之上_ 的工具栏都移出屏幕. 可以使用 `sticky` 标记来配置一个固定工具栏. 如果没有工具栏指定了 `sticky` 标记,  `<app-header>`的第一个子元素被固定.

```
  <app-header fixed condenses effects="waterfall">
    <app-toolbar>
      <paper-icon-button icon="menu"></paper-icon-button>
      <div main-title></div>
      <paper-icon-button icon="shopping-cart"></paper-icon-button>
    </app-toolbar>
    <app-toolbar></app-toolbar>
    <app-toolbar>
      <div spacer main-title>My App</div>
    </app-toolbar>
  </app-header>
```

在这里, 第一个工具栏 (带图标按钮) 被固定. 它一直在屏幕上,其它的工具栏则滑上来堆叠在它的顶部. 标题中的 `spacer` 标记添加了左填充让标题不会遮盖了工具栏左边的菜单按钮.

收缩头部使用它的自然高度 (也就是内容的高度除非在CSS中显式指定了高度). 它会向下收缩直到固定组件的高度.

想要保持一个标签栏就把标签栏放到最后并设置为固定.

```
  <app-header id="header" effects="waterfall" fixed condenses>
    <app-toolbar>
      <paper-icon-button icon="arrow-back"></paper-icon-button>
      <div main-title>Spork</div>
      <paper-icon-button icon="shopping-cart"></paper-icon-button>
    </app-toolbar>
    <paper-tabs selected="0" sticky>
      <paper-tab>Food</paper-tab>
      <paper-tab>Drink</paper-tab>
      <paper-tab>Life</paper-tab>
    </paper-tabs>
  </app-header>
```

### 滚动效果

大多数滚动效果和 _收缩头部_ 一起使用. 在头部收缩时改变外观.

只有一个例外是瀑布效果需要一个固定头部, 但是不论是否有 `condenses` 标记都可以使用. 当用户开始滚动时,它使标题看起来提升到内容的上方
，因此内容可以在其下滚动.

```
  <app-header fixed effects="waterfall">
    <app-toolbar>
      <div main-title>App name</div>
    </app-toolbar>
  </app-header>
```

要尝试各种标题选项，包括所有滚动效果，查看演示:

<a href="https://polymerelements.github.io/app-layout/templates/test-drive/" class="blue-button">
  开始演示
</a>

所有的滚动效果列表, 查看 [ `<app-header>`
参考](https://elements.polymer-project.org/elements/app-layout?active=app-header). 需要自定义滚动效果, 查看 [`AppScrollEffectsBehavior`
参考](https://elements.polymer-project.org/elements/app-layout?active=Polymer.AppScrollEffectsBehavior).

想要了解更多, 查看 [Scrolling
techniques](https://www.google.com/design/spec/patterns/scrolling-techniques.html#) 在material
design规范中各个滚动效果的概述.

### 文档滚动条和组件滚动条

`<app-header>` 组件默认使用文档滚动条. 在移动浏览器上向下滚动时会隐藏URL栏. 然而由于只有一个文档滚动条,当你在不同页面间切换时,应用需要管理各个页面的滚动位置.

如果使用类似 `<iron-pages>` 来切换视图, 就可以用
[`<app-scrollpos-control>`](https://elements.polymer-project.org/elements/app-layout?active=app-scrollpos-control)
来追踪各个视图的滚动位置. 查看API文档中的示例.

可以在`<app-header>`中指定一个`scrollTarget`属性来使用组件滚动条:

```
  <div id="scrollingRegion" style="overflow-y: auto;">
    <app-header scroll-target="scrollingRegion">
    </app-header>
  </div>
```

当你想在边栏中使用头部滚动效果时很有用,比如抽屉.


### 头部布局

[`<app-header-layout>`](https://elements.polymer-project.org/elements/app-layout?active=app-header-layout)
组件可以方便地用一个 `<app-header>`配合布局. 它围绕内容提供了必要的填充所以内容不会被头部隐藏.

使用时, 放一个 `<app-header>` 以及 `<app-header-layout>` 内部放一些内容.

```
  <app-header-layout>
    <app-header fixed condenses effects="waterfall">
      <app-toolbar>
        <div main-title>App name</div>
      </app-toolbar>
    </app-header>
    <div>
      <!-- content goes here -->
    </div>
  </app-header-layout>
```

默认布局使用文档滚动. 如果不想滚动整个页面, 布局可以定义自己的滚动区域, 具体可查看 [API
docs](https://elements.polymer-project.org/elements/app-layout?active=app-header-layout).

## 抽屉

[`<app-drawer>`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer)
组件是一个抽屉可置于屏幕的左侧或右侧.

```
<app-drawer>
  <paper-menu selected="0">
    <paper-item>Item One</paper-item>
    <paper-item>Item Two</paper-item>
    <paper-item>Item Three</paper-item>
  </paper-menu>
</app-drawer>
```

有几种方法可以打开和关闭抽屉:

-   滑动打开关闭的抽屉. 设置
    [`swipeOpen`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer#property-swipeOpen)
    为 `true` 用来探测屏幕边缘的滑动手势来打开抽屉.

-   将抽屉拖动一半以上来关闭 (或打开) 并且它会自己继续关闭. 快速滑动就算没有达到一半以上(轻拂或甩动手势)也拥有相同的效果 .

-   通过编程调用
    [`open`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer#method-open),
    [`close`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer#method-close),
    或 [`toggle`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer#method-toggle)来打开和关闭抽屉.
    或绑定到
    [`opened`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer#property-opened) 属性.

-   通过设置`persistent`属性可将抽屉固定在边栏, 同时禁止了滑动/甩动手势.

由于抽屉是一个单独的组件,可以用不同的方式组成它. 例如:

-   对于全高抽屉, 将抽屉放在头部布局的外边, 或将头部布局包装到一个抽屉布局的内部.
-   对于在头部 _以下_ 打开的抽屉, 将抽屉或抽屉布局放到头部布局中.

### 设计抽屉样式

可以使用 `--app-drawer-content-container` 来设置抽屉样式. 例如可以设置一个背景或添加一个边线,或者用阴影定义抽屉的边界.

当一个响应式抽屉打开时,剩余的屏幕由一个帷幕或 _scrim_ 所覆盖. 通过`--app-drawer-scrim-background`自定义属性来设置scrim背景. 默认的 scrim 是一个半透明灰色背景.

以下的CSS为抽屉添加了一个边界阴影并提供了一个彩色scrim.

```
  app-drawer {
    --app-drawer-content-container: {
      box-shadow: 1px 0 2px 1px rgba(0,0,0,0.18);
    }
    --app-drawer-scrim-background: rgba(179, 157, 219, 0.5);
  }
```

### 抽屉布局

[`<app-drawer-layout>`](https://elements.polymer-project.org/elements/app-layout?active=app-drawer-layout)
组件使用单个抽屉创建了一个响应式布局. 在宽屏幕上, 抽屉默认是一个固定的边栏; 总是显示并且禁止了滑动和甩动手势.

要添加一个抽屉切换按钮, 在`<app-drawer-layout>`的子元素里放一个带有 `drawer-toggle` 标记的组件. 通常抽屉切所按钮放在应用工具栏里.

`<app-header-layout>` 可以嵌套在一个 `<app-drawer-layout>` 内部来创建一个带有抽屉和头部的响应式布局.

```
  <app-drawer-layout>

    <app-drawer>
      <app-toolbar>Getting Started</app-toolbar>
    </app-drawer>

    <app-header-layout>

      <app-header reveals effects="waterfall">
        <app-toolbar>
          <paper-icon-button icon="menu" drawer-toggle></paper-icon-button>
          <div main-title>Title</div>
        </app-toolbar>
      </app-header>

      <div>Content goes here</div>

    </app-header-layout>

  </app-drawer-layout>
```

## 响应式导航模式

很多情况下, 想要基于屏幕尺寸来切换导航. 一个通用模式是在桌面环境下使用导航标签, 在移动环境下使用一个导航抽屉, 如同
[Shop app](https://shop.polymer-project.org/).

![screenshot of a nav menu with 5 tabs, displayed horizontally, labelled "item one" through "item four"](/images/1.0/toolbox/app-layout-responsive-nav-tabs.png)
![screenshot of the same menu displayed vertically, after being open from a mobile drawer button ](/images/1.0/toolbox/app-layout-responsive-nav-drawer.png)

一些应用布局组件可以达到这种效果,使用数据绑定来切换标签和抽屉导航.

[transform navigation demo](https://polymerelements.github.io/app-layout/patterns/transform-navigation/)
展示了一个这种转换的简单版本.

```
  <!-- force-narrow prevents the drawer from ever being displayed
       in persistent mode -->
  <app-drawer-layout force-narrow>

    <app-drawer id="drawer">

      <!-- an empty toolbar in the drawer looks like a
           continuation of the main toolbar. It's optional. -->
      <app-toolbar></app-toolbar>

      <!-- Nav on mobile: side nav menu -->
      <paper-menu selected="{{selected}}" attr-for-selected="name">
        <template is="dom-repeat" items="{{items}}">
          <paper-item name="{{item}}">{{item}}</paper-item>
        </template>
      </paper-menu>

    </app-drawer>

    <app-header-layout>
      <app-header class="main-header">

        <app-toolbar>
          <!-- drawer toggle button -->
          <paper-icon-button class="menu-button" icon="menu" drawer-toggle hidden$="{{wideLayout}}"></paper-icon-button>
        </app-toolbar>

        <app-toolbar class="tabs-bar" hidden$="{{!wideLayout}}">
          <!-- Nav on desktop: tabs -->
          <paper-tabs selected="{{selected}}" attr-for-selected="name" bottom-item>
            <template is="dom-repeat" items="{{items}}">
              <paper-tab name="{{item}}">{{item}}</paper-tab>
            </template>
          </paper-tabs>
        </app-toolbar>

      </app-header>
    </app-header-layout>

  </app-drawer-layout>

  <iron-media-query query="min-width: 600px" query-matches="{{wideLayout}}"></iron-media-query>
```

<a href="https://polymerelements.github.io/app-layout/patterns/transform-navigation/" class="blue-button">Launch Demo</a>

<a href="https://github.com/polymerelements/app-layout/blob/master/patterns/transform-navigation/x-app.html" class="blue-button">View full source</a>

[Shop app](https://shop.polymer-project.org/). 使用了这种模式的一种更复杂版本, 通过条件模板来避免在不需要的时候创建导航组件. 这意味着应用在移动环境中不需要创建标签.
