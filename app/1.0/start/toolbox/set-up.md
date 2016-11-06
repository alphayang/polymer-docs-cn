---
title: Step 1. 配置
subtitle: "使用App Toolbox构建应用"
---

<!-- toc -->

[Polymer App Toolbox][toolbox]提供了一系列组件、工作和模板用来构建基于Polymer的Progressive Web Apps。

在15分钟内，按照如下步骤可以安装、创建和发布一个基于App Toolbox模板的项目。

## 安装Polymer CLI

1.  安装LTS版本的(4.x)Node.js. 最新版本(6.x)也可以工作，但是还没有得到官方支持。不支持低于LTS的版本。

1.  安装Polymer CLI

        npm install -g polymer-cli

## 使用模板来初始化项目

1. 新建一个项目文件夹

        mkdir my-app
        cd my-app

1. 使用一个应用模板来初始化项目

        polymer init app-drawer-template

## 运行项目

App Toolbox模板不需要Build。可以直接使用Polymer CLI来运行项目，文件的改变会自动刷新浏览器。

    polymer serve --open

上面的任务会自动打开你的默认浏览器并导航到本地应用(位置 `http://localhost:8080`).

![App Toolbox Drawer Template](/images/1.0/toolbox/app-drawer-template.png)

## 初始化Git库（可选）

应用模板不包含任何版本控制系统。使用下面的步骤可以利用Git来管理你的源码。Y

1.  `cd` 进入到项目文件夹。

1.  初始化Git库。

        git init

1.  添加并提交所有文件。

        git add . && git commit -m "Initial commit."

## 文件夹结构

下图是一个模板的基本结构。

    /
    |---index.html
    |---src/
    |---bower_components/
    |---images/
    |---test/


*   `index.html`是应用的主入口 
*   `src/` 存放应用相关的自定义组件
*   `bower_components/` 存储使用bower获取的其它可重用自定义组件
*   `images/` 存放静态图片
*   `test/` 存放[web
    components的测试](https://github.com/Polymer/web-component-tester).

## 下一步

App Toolbox模板已经建立和运行，开始学习如何Now that your App Toolbox template is up and [添加一个新的内容页](create-a-page), 或如何[把应用发布到web](deploy).

[toolbox]: /1.0/toolbox/
[shared styles]: /1.0/docs/devguide/styling.html#style-modules
[md]: http://www.google.com/design/spec/material-design/introduction.html
