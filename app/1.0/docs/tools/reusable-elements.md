---
title: 发布组件
---

<!-- toc -->

## 介绍

想发布你的第一个可重用Polymer组件?
完美! 这一节帮助你进行发布.

你需要:

-   使用官方模板的组件并添加本地git管理.
-   在Github上有一个发布后的可利用Bower安装的组件版本.
-   托管在Github pages上的文档和一个运行中的组件demo.

## 配置

本文假设你已经安装了Git, Node.js和Bower，也有一个GitHub帐户. 

1.  [安装 Polymer CLI](polymer-cli#install). 
1.  [创建一个组件项目](polymer-cli#element). 

## 开发

开发新组件时确保使用相对URL以便于引用其它组件到你的组件项目中. 查看 [HTML 
imports和依赖管理](polymer-cli#element-imports)获取更多信息.

一个快捷测试组件的方法是访问组件的demo，使用Polymer CLI的`serve` 命令:

    polymer serve 

然后就可以在以下URL中查看你的组件demo:

<pre><code>http://localhost:8080/components/<var>test-element</var>/demo/</code></pre>

<code><var>test-element</var></code>是组件项目的根目录名称. 

查看 [运行本地web服务器](polymer-cli#serve)获取更多帮助. 

### 单元测试

使用Polymer CLI的`test`命令来运行组件的测试. 

    polymer test

查看[运行测试](polymer-cli#tests)获取更多信息. 

### 文档

Polymer CLI组件项目自带了内置的文档.

查看[为组件编写文档](documentation)获取更多信息. 

## 发布

将组件发布到GitHub需要两个步骤:

-   将项目推送到GitHub库中并打了一个发布版本号的标记,其它人可以使用Bower来安装.

    这一步中需要创建一个*master*分支只包含最小的代码量来其它应用或组件来使用.

-   推送一个`gh-pages`分支到GitHub库.通过GitHub pages提供在线文档和组件预览.

    这步中创建的*gh-pages (GitHub pages)*分支包含一个组件的首页.分支包含**checked-in dependencies**, **demos**和**documentation**.

### 把组件摄像头到GitHub

组件开发完成后,推送一个新的版本到GitHub库中.

 点击[此处](https://github.com/new)在GitHub上创建一个新库. 最好库的命令和组件的命令保持一致(例如组件为`<test-element>`,那么库也应当命名为`test-element`).

按照下列步骤(假设组件的名称为
`test-element`):

    # 进入组件目录
    cd test-element

    # 初始化Git库
    git init

    # 提交代码
    git add .
    git commit -m 'My initial version'

    # 添加GitHub上新创建库的远程地址.
    # 将<username>替换为你的GitHub用户名.
    git remote add origin https://github.com/<username>/test-element.git

    # 把代码推送到远程master分支
    git push -u origin master


下面，为GitHub库中的组件添加一个版本号标记. 可以在GitHub UI **或** 终端中完成此操作.

#### 使用终端

    # 标记一个可以发布的组件版本
    # 比如标记version 0.0.1
    git tag -a v0.0.1 -m '0.0.1'

    # 推送标记到GitHub
    git push --tags


#### 使用GitHub UI

在组件的GitHub主页中点击导航栏的"releases"链接. 如下图:

![Preview of the GitHub navigation bar for a repository listing four navigation items—commits, branches, releases, and contributors. The releases link is highlighted.](/images/1.0/reusable-elements/publishing/image_2.png)

导航到*Releases*页面中. 没有任何发布的一个项目会显示如下图的一个消息.

![GitHub Releases page message stating that there aren't any releases here yet. The Create a new release button is highlighted](/images/1.0/reusable-elements/publishing/image_3.png)

点击‘Create a new release’按钮.

显示一个Release草稿页面来输入版本号和发布信息.
例如, 设置标记为v0.0.1在`master`分支中创建

![The GitHub releases form displaying an input field for entering in a version number, a drop-down box for selecting the target branch, a release titles field and a description field](/images/1.0/reusable-elements/publishing/image_4.png)

表单中的信息填写好后,点击‘Publish release’按钮来完成标记和版本发布.

![Preview of the Publish release button in the GitHub releases page](/images/1.0/reusable-elements/publishing/image_5.png)

#### 对组件进行安装测试

现在应该可以安装你的组件了. 切换到一个新的目录中运行命令进行测试:

    # 替换<username>为你的GitHub用户名,<test-element>
    # 为你的代码库名称. 
    bower install <username>/<test-element>

### 为组件发布一个demo和首页

下如之前提到的,Polymer CLI组件项目模板内建了demo和文档支持.在这一部分中就可以看到如何使用GitHub Pages来托管demo和文档.

下面的命令使用了Polymer团队的 [`gp.sh` 
脚本](https://github.com/Polymer/tools/blob/master/bin/gp.sh)来推送一个组件首页到GitHub Pages. 

**重要:** 确保在一个临时目录中运行`gp.sh`脚本,
. 脚本会重写当前目录中的内容.
{ .alert .alert-error }

下面的命令中, 替换<code><var><username></var></code>为你的GitHub用户名,  <code><var><test-element></var></code>为你的GitHub库名称. 

    # git clone the Polymer tools repository somewhere outside of your 
    # element project
    git clone git://github.com/Polymer/tools.git

    # Create a temporary directory for publishing your element and cd into it
    mkdir temp && cd temp

    # Run the gp.sh script. This will allow you to push a demo-friendly
    # version of your page and its dependencies to a GitHub pages branch
    # of your repository (gh-pages). Below, we pass in a GitHub username
    # and the repo name for our element
    ../tools/bin/gp.sh <username> <test-element>

    # Finally, clean-up your temporary directory as you no longer require it
    cd ..
    rm -rf temp

这样就创建了一个新的`gh-pages`分支 (或clone并清理当前分支),然后推送一个共享的组件版本.要查看新发布的文档,
用浏览器访问:

    http://<username>.github.io/<test-element>/

## 分享

现在可以分享你的组件链接给其它人了.Polymer CLI组件项目提供了一个定义了样式的文档页面如下图所示: 

![Preview of the component langing page, displaying the element title in the header with a demo link next to it. The rest of the page contains formatted summary and attribute/method/event information parsed from the documentation in your element](/images/1.0/reusable-elements/documentation-page.png)
