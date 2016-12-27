---
title: "案例学习: Shop应用"
---

<!-- toc -->


Shop 是一个基于Toolbox打造的全功能渐进式电商web应用示例. 在线体验:

<a href="https://shop.polymer-project.org/" class="blue-button">打开 Shop 示例
</a>

案例学习展示了Shop是如何使用App Toolbox来打打造最好的用户体验.

## 应用结构

 Shop 应用由几个主视图组成: 首页视图, 列表视图,
详情视图和购物车视图:

<div class="image-container layout horizontal">
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-browse.png" alt="screenshot of the list view">
  </div>
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-detail.png" alt="screenshot of the detail view">
  </div>
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-cart.png" alt="screenshot of the shopping cart view">
  </div>
</div>

应用使用自定义组件作为组织原则: 最顶层的应用组件作为应用的主控制器. 特定应用组件作为视图,例如浏览和详情视图.
用户和产品数据同样使用组件实现. 这些组件都是由按钮和标签这些可重用组件构成. 可重用组件同样适用于诸如布局和路由这些基本功能.
由[`<iron-pages>`](https://elements.polymer-project.org/elements/iron-pages)
组件来控制哪个视图处于可见状态.

![the high level architecture of the application, as described above](/images/1.0/toolbox/high-level-arch.png)

## 路由

Shop的客户端URL路由基于
[`<app-route>`](https://elements.polymer-project.org/elements/app-route)
组件, 是一个模块化的路由组件. 应用组件由一个顶层
`<app-route>` 组件来绑定到页面的URL, 并使用应用组件的`page`属性来选择顶层视图.

顶层组件委剩余的路由给其它表示子路由的`<app-route>`实例. 例如当浏览器一个分类时, 顶层 `<app-route>` 选择浏览视图, 第二层的 `<app-route>` 选择要显示的分类.

所有导航都通过链接 (`<a>` 标记) 来实现(这样可以确保应用可被网络爬虫正常索引). 路由组件处理URL变化并转递路由数据给活动视图组件.

例如`/list/Shoes` 路径显示了浏览(列表)视图, 并传递类别 "Shoes" 给浏览视图.

查看更多:

-   [组件路由封装Encapsulated routing with elements](/1.0/blog/routing)
-   [`<app-route>` API 参考](https://elements.polymer-project.org/elements/app-route)

## 视图

组件的主视图由一个 `<iron-pages>` 组件来控制, 它会每次显示单个视图. 当一个视图处于活动时, 它将覆盖应用标题以下的整个内容区域.

 [`<iron-pages>`](https://elements.polymer-project.org/elements/iron-pages)
组件绑定到了应用组件的 `page` 属性, 可基于当前路由进行设置. 视图切换代码如下所示:

`shop-app.html` { .caption }
```
<iron-pages role="main" selected="[[page]]" attr-for-selected="name" selected-attribute="visible">
  <!-- home view -->
  <shop-home name="home" categories="[[categories]]"></shop-home>
  <!-- list view of items in a category -->
  <shop-list name="list" route="[[subroute]]" offline="[[offline]]"></shop-list>
  <!-- detail view of one item -->
  <shop-detail name="detail" route="[[subroute]]" offline="[[offline]]"></shop-detail>
  <!-- cart view -->
  <shop-cart name="cart" cart="[[cart]]" total="[[total]]"></shop-cart>
  <!-- checkout view -->
  <shop-checkout name="checkout" cart="[[cart]]" total="[[total]]" route="{{subroute}}"></shop-checkout>
</iron-pages>
```

`page` 属性为 `list` 时, 列表或浏览视图处于活动状态.

视图将被按需进行延迟加载,这利用了自定义组件的 _upgrade_ 功能. 非活动的视图组件 (如 `shop-list`
) 将以`HTMLElement`的实例存在于DOM中.

当页面发生变化时, 应用加载活动视图的定义.
当加载定义时, 浏览器 _upgrades_ 组件到一个全功能的自定义组件.

```
_pageChanged: function(page, oldPage) {
  if (page != null) {
    // home route is eagerly loaded
    if (page == 'home') {
      this._pageLoaded(Boolean(oldPage));
    // other routes are lazy loaded
    } else {
      this.importHref(
        this.resolveUrl('shop-' + page + '.html'),
        function() {
          this._pageLoaded(Boolean(oldPage));
        }, null, true);
    }
  }
},

```

以上的逻辑中, 主面视图内嵌于应用shell中, 其它的视图则按需加载片段.

Shop 还使用了 [`dom-if`](/1.0/docs/api/dom-if) 模板来延迟创建视图:

```
<div id="tabContainer" primary$="[[_shouldShowTabs]]" hidden$="[[!_shouldShowTabs]]">
  <template is="dom-if" if="[[_shouldRenderTabs]]">
    <shop-tabs
        selected="[[categoryName]]"
        attr-for-selected="name">
      <template is="dom-repeat" items="[[categories]]" as="category" initial-count="4">
        <shop-tab name="[[category.name]]">
          <a href="/list/[[category.name]]">[[category.title]]</a>
        </shop-tab>
      </template>
    </shop-tabs>
  </template>
```

模板内容在解析后被插入主文档而不是包含在主文档中. 如果 `_shouldRenderTabs` 属性为 `true`, 模板的内容被插入到DOM,
组件被初始化并且创建组件的本地DOM树. 由于标签只在桌面环境中显示, 移动用户不需要为他们不会用到的组件浪费资源.

## 主题

Shop 使用
[CSS 自定义属性](/1.0/docs/devguide/styling#custom-css-properties) 和
[mixins](/1.0/docs/devguide/styling#custom-css-mixins) 为应用特定的组件和其包含的可重用组件提供主题.

Shop 定义了一些顶层自定义属性来设置基本的主题颜色, 这些都会传递给其它组件.

```
:host {
  --app-primary-color: #0b374b;
  --app-secondary-color: #fee0e0;
  --app-accent-color: #202020;
    ...
}
```

这些自定义属性是一些由组件作者定义的特殊CSS属性.  Shop 应用使用了其中的三个属性来定义主题颜色.
这些值可以其它CSS规则内部由`var()`函数使用:

```
  color: var(--app-accent-color);
```

自定义属性可被应用于设置 _其它_ 自定义属性.

```
  --paper-button-ink-color: var(--app-primary-color);
  --paper-icon-button-ink-color: var(--app-primary-color);
  --paper-spinner-color: var(--app-primary-color);
```

这里,应用主题颜色由paper组件集合向下传递给了其它可重用组件.

如果添加了多个组件到应用中, 可以在组件API文档中查看这些组件的自定义属性. (例如, 在上例中
`--app-primary-color` 被用来设置 `<paper-button>` 墨水颜色, 在文档中查看 `<paper-button>`
[API 文档](https://elements.polymer-project.org/elements/paper-button#styling).

关于更多的自定义属性和mixins, 查看
[Polymer 文档](/1.0/docs/devguide/styling#xscope-styling-details). Polymer
为自定义属性提供了一个  _隔离_ , 但是隔离有一些限制, 特别是关于动态改变属性值. 如果你想要更好的使用自定义属性, 查看 [隔离限制
](/1.0/docs/devguide/styling#custom-properties-shim-limitations)
和  [自定义样式 API](/1.0/docs/devguide/styling.html#style-api).

## 离线缓存

为了在不稳定的网络和离线情况下提供更好的体验, Shop
使用了 service worker 来提供应用shell的离线缓存—主要是应用UI和业务逻辑.
Service worker 是一个关联于特定网站的脚本可用来做为客户端网络请求的代理.
Service worker 可以截获网络请求, 访问浏览器的缓存.

用户首次访问网站时浏览器将安装网站的 service
worker, service worker 确保网站的应用shell被缓存以便离线使用. 后续访问时, service worker 可以直接从缓存中加载应用shell.
如果用户完全没有网络, service worker
仍然可以加载应用shell, 并显示合适的离线数据或一个离线消息.

Shop 使用 `sw-precache` [库](https://github.com/GoogleChrome/sw-precache)
来提供离线支持. 该库可以接收一个需要被缓存的文件列表并在构建时生成一个service worker,
所以不需要自己动手编写service worker代码. 只需要创建一个所需资源列表并将precache脚本添加到构建流程中.
[Polymer CLI](https://github.com/polymer/polymer-cli)
支持 [使用 sw-precache 来生成一个service worker的案例](https://github.com/polymer/polymer-cli#app-shell-structure) 用来缓存应用shell的依赖资源.

## 使用app-layout进行应用布局

应用布局组件为Shop提供了响应式布局. 这些组件都是模块化的设计可被用来组件不同的布局.
应用的主UI组件是一个包含了标题和主导航控制的 `<app-header>`.  `<app-header>` 包含
`<app-toolbars>`, 是一个水平容器来放置工具. 一个工具栏包含了标题和一些按钮.

在桌面环境中, 浏览视图使用第二个工具栏来显示导航标签.
当向下拉动页面时, 标题会缩小并滚动离开.
从任意点向上拉动页面时显示标签.

<div class="image-container layout horizontal">
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-toolbar-expanded.png" alt="screenshot of the expanded toolbar">
  </div>
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-toolbar-condensed.png" alt="screenshot of the condensed toolbar">
  </div>
</div>

标签在移动设备上不能正常使用, 所以 Shop 使用了一个 `<app-drawer>` 组件来作为导航抽屉, 配合一个垂直菜单.

<div class="image-container layout horizontal">
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-home.png" alt="screenshot of the drawer closed on mobile">
  </div>
  <div class="image-wrapper">
    <img src="/images/1.0/toolbox/shop-drawer.png" alt="screenshot of the drawer open on mobile">
  </div>
</div>

应用布局组件集合同样包含了简单容器组件来放置标题和抽屉:  `<app-header-layout>` 和
`<app-drawer-layout>` 组件.

## 更多内容

想要查看 Shop 应用的更多信息, 可以在Github上找到全部代码:

[https://github.com/Polymer/shop](https://github.com/Polymer/shop)
