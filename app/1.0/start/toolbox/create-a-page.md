---
title: Step 2. 创建一个页面
subtitle: "使用App Toolbox构建应用"
---

<!-- toc -->

`app-drawer-template`包含3个占位页面，可以用它们来开始打造应用界面。当然，你可以添加更多的页面。

此步骤将指导您完成添加新页面或顶级视图到你的应用程序。

## 为新页面创建一个组件

首先，创建一个新的自定义组件用来封装新视图的内容。

1.  新建文件`src/my-new-view.html`并在编辑器打开.

2.  使用Polymer添加新组件的定义框架:

    ```
    <link rel="import" href="../bower_components/polymer/polymer.html">

    <dom-module id="my-new-view">

      <!-- Defines the element's style and local DOM -->
      <template>
        <style>
          :host {
            display: block;
            padding: 16px;
          }
        </style>
        <h1>New view</h1>
      </template>

      <!-- Creates the element's prototype and registers it -->
      <script>
        Polymer({
          is: 'my-new-view',
          properties: {
            route: Object
          }
        });
      </script>

    </dom-module>

    ```

现在新组件是最基本的，只是有一个`<h1>`来显示"New View"，但是后面我们可以让它变得更有趣。


## 添加组件到应用中

1.  编辑器中打开 `src/my-app.html`.

1.  查找在`<iron-pages>`中包含的页面:

    ```
      <iron-pages role="main" selected="[[page]]" attr-for-selected="name">
        <my-view1 name="view1"></my-view1>
        <my-view2 name="view2"></my-view2>
        <my-view3 name="view3"></my-view3>
      </iron-pages>
    ```

    `<iron-pages>`绑定在 `page`变量中用来修改路由，选择活动页面并隐藏其它页面。

1.  把新页面添加到iron-pages中:

    ```
        <my-new-view name="new-view"></my-new-view>
    ```

    Your `<iron-pages>` should now look like this:

    ```
      <iron-pages role="main" selected="[[page]]" attr-for-selected="name">
        <my-view1 name="view1"></my-view1>
        <my-view2 name="view2"></my-view2>
        <my-view3 name="view3"></my-view3>
        <my-new-view name="new-view"></my-new-view>
      </iron-pages>
    ```

    注意: 正常情况下每一次添加一个新的自定义组件需要添加一个HTML Import用来确保组件定义被加载。但是这个应用模板已经配置好了基于路由的顶层视图按需被动加载，所以不需要在新`<my-new-view>`组件中添加引用了。 

    应用模板中下面的代码用来确保加载每一个页面的定义以便在路由更改时使用。可以看到，我们遵循了简单的转换(`'my-' + page + '.html'`)
    来引入页面中的定义，你也可以修改这些代码来处理更复杂的路由和被动加载。

    在现有的模板代码中你不需要添加这些{ .caption }

    ```
      _pageChanged: function(page) {
        // load page import on demand.
        this.importHref(
          this.resolveUrl('my-' + page + '.html'), null, null, true);
      }
    ```

## 创建一个导航菜单

最后，我们在左侧抽屉栏中添加一个菜单项用来导航到新页面。

1.  在编辑器中打开 `src/my-app.html` 。

1.  找到`<app-drawer>`组件。

    ```
    <!-- Drawer content -->
    <app-drawer>
      <app-toolbar>Menu</app-toolbar>
      <iron-selector selected="[[page]]" attr-for-selected="name" class="drawer-list" role="navigation">
        <a name="view1" href="/view1">View One</a>
        <a name="view2" href="/view2">View Two</a>
        <a name="view3" href="/view3">View Three</a>
      </iron-selector>
    </app-drawer>
    ```

    每一个导航菜单项包含一个锚组件 (`<a>`) 并使用CSS来美化。

1.  在菜单底部添加新的菜单项。

    ```
        <a name="new-view" href="/new-view">New View</a>
    ```

    添加后的菜单:

    ```
    ...
    <!-- Drawer content -->
    <app-drawer>
      <app-toolbar>Menu</app-toolbar>
      <iron-selector selected="[[page]]" attr-for-selected="name" class="drawer-list" role="navigation">
        <a name="view1" href="/view1">View One</a>
        <a name="view2" href="/view2">View Two</a>
        <a name="view3" href="/view3">View Three</a>
        <a name="new-view" href="/new-view">New View</a>
      </iron-selector>
    </app-drawer>
    ...
    ```

新页面已经就绪! 在浏览器中访问
[http://localhost:8080/new-view](http://localhost:8080/new-view).

![Example of new page](/images/1.0/toolbox/app-drawer-template-newview.png)

## 页面注册

发布应用时，需要使用WPolymer CLI来处理文件。Polymer CLI 需要知道所有按需加载的片断比如刚才添加的被动加载视图。

1.  编辑器中打开 `polymer.json` 。

1.  把 `src/my-new-view.html` 添加到`fragments`列表中。

    添加后的片断列表:

    ```
    "fragments": [
      "src/my-view1.html",
      "src/my-view2.html",
      "src/my-view3.html",
      "src/my-new-view.html"
    ]
    ```

注意: 只需要添加被动加载或使用`async`
标记来导入的片断到`fragments`列表。使用同步标记的导入
`<link rel="import">` *不能* 添加到`fragments`。

## 下一步

已经添加到了一新页面到应用中，继续学习如何[添加第三方组件到应用中](add-elements), 或如何
[发布应用](deploy).
