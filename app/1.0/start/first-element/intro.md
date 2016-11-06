---
title: "Step 1: 配置"
subtitle: "创建第一个Polymer组件"
---

<!-- toc -->

在这一节里，将学习如何使用Ploymer 1.0创建一个组件.这个简单Polymer组件包括一个切换按钮.完成后的按钮的样子:

![Sample star-shaped toggle buttons, showing pressed and unpressed
state](/images/1.0/first-element/sample-toggles.png)

可以简单的使用这个组件:

```html
<icon-toggle></icon-toggle>
```

可以通过这个项目来了解Polymer的主要原理.

如果还不理解，不用急. 第一个原理都在Polymer文档中有详细的说明.


## Step 1: 配置

需要以下内容来完成配置部分:

-   The starting code.
-   [Polymer CLI](/1.0/docs/tools/polymer-cli)来运行Demo.


**不想安装?** 可以在线学习此部分[Step 1: No install version](#noinstall).
{ .alert .alert-info }


### 下载the starting code

1.  下载starting code的zip包.

    <a class="blue-button" href="https://github.com/googlecodelabs/polymer-first-elements/releases/download/v1.0/polymer-first-elements.zip">
      Download ZIP
    </a>

2.  解压到项目目录.

    项目目录的结构:

    <pre>
    README.md
    bower.json
    bower_components/
    demo/
    icon-toggle-finished/
    icon-toggle.html
    </pre>

    主要使用`icon-toggle.html`, 它包括自定义组件.


### 安装Polymer CLI.

安装Polymer CLI来本地运行demo.

1.  安装LTS版本(4.x)的[Node.js](https://nodejs.org/en/download/).
    最新版本(6.x)也可以工作，但是还没有得到官方支持。不支持低于LTS的版本。

2.  安装[git](https://git-scm.com/downloads).

3.  安装最新版本的[Bower](http://bower.io/#install-bower).

        npm install -g bower

4.  安装Polymer CLI.

        npm install -g polymer-cli

### 运行demo

运行组件demo:

1.  在项目根目录运行`polymer serve`:

        polymer serve

2.  浏览器中打开`localhost:8080/components/icon-toggle/demo/`.

    (注意N`icon-toggle`—组件使用了
    `bower.json`中定义的路径而并非文件在目录中的路径.
    查看`polymer serve`的更多路径信息[HTML imports 和 dependency
    management](/1.0/docs/tools/polymer-cli#element-imports).)

    在切换按钮处有一些文字.看起来没什么作用, 但它表示Demo是工作的.


<img src="/images/1.0/first-element/starting-state.png" alt="Initial state of the demo. The demo shows three icon-toggle elements, two labeled 'statically-configured icon toggles' and one labeled 'data-bound icon toggle'. Since the icon toggles are not implemented yet, they appear as placeholder text reading 'Not much here yet'." title="Initial demo">

**如果文字没有出现**, 确认你是在demo目录中, 或`demo/index.html`,
而不是在`toggle-icon.html` 或`toggle-icon-demo.html`.
{ .alert .alert-info }

如果一切正常, 进入[step 2](step-2).

<a class="blue-button" href="step-2">Step 2: 添加Local DOM</a>



## Step 1: No install version {#noinstall}

不安装软件，在线运行demo:

1.  打开[starter project on Plunker](https://plnkr.co/edit/QfsudzAPCbAu56Qpb7eB?p=preview){target="\_blank"}.

2. 点击 **Fork** 来创建自己的项目.

继续[step 2](step-2).

<a class="blue-button" href="step-2">Step 2: 添加Local DOM</a>
