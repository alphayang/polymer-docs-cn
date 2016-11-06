---
title: 配置
subtitle: "创建第一个Polymer应用"
---

<!-- toc -->

Polymer Starter Kit是一个完整的Polymer1.0应用。它包括:

*   [Material Design][md] 布局.
*   R使用Page.js进行路由.
*   使用[web-component-tester](https://github.com/Polymer/web-component-tester)进行单元测试.
*   完整的本地开发环境和构建工具链.

在15分钟内学习在本地安装、构建和运行Polymer Starter Kit (PSK).

## 安装Polymer Starter Kit以及依赖

1.  安装[Node.js](https://nodejs.org/) (`node`)版本大于等于0.12.
    Node.js默认包含了npm. PSK使用`npm`来安装和管理工具.

1.  验证node和npm版本.

        node -v
        v0.12.5

        npm -v
        2.12.2

1.  安装Gulp和Bower.

        npm install -g gulp bower

    注意: `-g`参数将Gulp和Bower安装到系统中, 可能需要`sudo` privileges权限.这样可以在任何目录中使用`gulp`和`bower`，PSK需要这样的功能.

1.  下载[最新的PSK](https://github.com/PolymerElements/polymer-starter-kit/releases/latest).

    有2个版本的PSK，简化版 (如`polymer-starter-kit-light-x.x.x.zip`)和完整版(如`polymer-starter-kit-x.x.x.zip`). 需要下载完整版.

1.  解压到相应目录。解压出的文件夹名`polymer-starter-kit-x.x.x`.可以根本项目需要修改文件夹名称。
    

1. `cd` 到PSK项目根目录.

1.  安装构建工具.

        npm install

1.  安装应用依赖.

        bower install

## 文件夹结构

下图是PSK的文件夹结构.

    /
    |---app/
    |   |---elements/
    |   |---images/
    |   |---scripts/
    |   |---styles/
    |   |---test/
    |---docs/
    |---dist/

*   `app/` 存放源代码进行开发的地方.
*   `elements/` 存放自定义组件.
*   `images/` 存放静态图片.
*   `scripts/` 存放JS脚本.
*   `styles/` 存放应用的[shared styles][shared styles]和CSS文件.
*   `test/` 存放[web组件测试](https://github.com/Polymer/web-component-tester).
*   `docs/` 存放"文档" (how-to guides) 来记录应用的特性.
*   `dist/` 用来存放发布的内容.使用build命令后，输出的所有应用文件（JS经过了压缩）都放在这里.

## 初始化Git库 (可选)

PSK安装不包括任何版本控制系统. 可以使用下列步骤来利用Git管理你的源代码.

1.  `cd` 到PSK项目根目录.

1.  初始化Git库.

        git init

1.  添加和提交所有文件.

        git add . && git commit -m "Add Polymer Starter Kit."

## 生成和运行

PSK可以在本地生成和运行.

1. `cd` 到PSK项目根目录.

1.  生成应用.

        gulp

1.  本地运行应用.

        gulp serve

    本地开发服务器会自动检测文件的修改并自动重新生成应用. 所以在使用`gulp serve`运行应用时不需要手工重新生成和运行了。

    上边的命令会自动打开默认浏览器并导航到本地应用(位于 `http://localhost:5000`).

![PSK 首页](/images/1.0/psk/psk-home.png)

## 下一步

PSK已经创建并运行，可以学习如何[添加一个新的内容页](create-a-page), 或如何[将应用发布到web](deploy).

[shared styles]: /1.0/docs/devguide/styling.html#style-modules
[md]: http://www.google.com/design/spec/material-design/introduction.html
