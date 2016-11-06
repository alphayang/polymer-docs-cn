---
title: Step 3. 添加组件
subtitle: "使用App Toolbox构建应用"
---

<!-- toc -->

已经给你的应用添加了一个新的视图，现在可以在视图中添加详细内容了。

在这个过程中，可以多多使用其它开发者提供的组件，比如
[Polymer Element Catalog][catalog] 或社区组件
[customelements.io][ceio].

## 确保已经安装了bower

[Bower][bower]是一个前端包管理器，用来获取和管理web组件。

确保bower使用以下方式安装:

    npm install -g bower

## 安装第三方组件

想要安装一个组件时，只需要确定组件的bower包名就可以了。

这一步，给应用添加Polymer的 `<paper-slider>` 组件
[Polymer Element Catalog here][paper-slider].  在左侧栏可以找到bower安装命令

在项目根目录运行命令:

    bower install --save PolymerElements/paper-slider

## 添加到应用中

1.  编辑 `src/my-new-view.html` .

1.  引用Import `paper-slider.html`

    添加到头部的link引用:

    ```
    <link rel="import" href="../bower_components/paper-slider/paper-slider.html">
    ```

1.  把`<paper-slider>`添加到模板.

    ```
    <paper-slider min="-100" max="100" value="50"></paper-slider>
    ```

    在`<h1>`下添加 .  新的模板内容：
    

    ```
    <!-- Defines the element's style and local DOM -->
    <template>
      <style>
        :host {
          display: block;
          padding: 16px;
        }
      </style>
      <h1>New view</h1>
      <paper-slider min="-100" max="100" value="50"></paper-slider>
    </template>
    ```

查看`paper-slider`在视图中的运行 :
[http://localhost:8080/new-view](http://localhost:8080/new-view).

![Example of page with slider](/images/1.0/toolbox/app-drawer-template-slider.png)

## 下一步

已经添加了一个第三方组件到页面中，学习如何发布
[deploy the app to the web](deploy).

[bower]: http://bower.io/
[catalog]: https://elements.polymer-project.org/
[paper-slider]: https://elements.polymer-project.org/elements/paper-slider
[ceio]: https://customelements.io/
