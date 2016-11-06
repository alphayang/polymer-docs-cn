---
title: Polymer CLI
---

<!-- toc -->

Polymer CLI还处于pre-release阶段. 有些参数可能会改变.
{.alert .alert-warning}

## 安装 {#install}

1.  安装 [Git](https://git-scm.com/downloads).

1.  安装LTS版本(4.x)的
    [Node.js](https://nodejs.org/en/download/)或最新版.

1.  安装最新版本的Bower.

        npm install -g bower

1.  安装Polymer CLI.

        npm install -g polymer-cli

全部安装好了.运行`polymer help`来查看命令列表.

## 概览 {#overview}

Polymer CLI是Polymer项目的命令行接口. 它包含一个构建工具, 一个组件和应用模板生成器,一个静态检查器, 一个开发服务器以及一个测试运行工具.

Polymer CLI适用于两种项目类型:

* 组件项目. 在组件项目中你只暴露出一个或一种相关的组件用于其它的组件或应用项目中,也可以发布到例如Bower或NPM的库中. 组件可以其它项目中重复使用.
* 应用项目. 在应用项目中，可以使用Polymer组件创建一个应用发部署为一个网站. 应用内部以独立的组件存在.

参照[安装](#install)来如何安装
Polymer CLI然后进入[创建一个组件](#element)或
[创建一个应用](#app)来开始你的第一个Polymer项目.

## 创建一个组件项目{#element}

该部分展示了如何创建一个组件项目.

1.  新建一个组件项目的根目录. 最好的办法是根据见名思义的原则来命令目录名称,使用你的组件名.

    <pre><code>mkdir <var>my-el</var> && cd <var>my-el</var></code></pre>

    Where <code><var>my-el</var></code> is the name of the element you're
    creating.

1.  初始化组件. Polymer CLI需要一些信息来创建组件项目.

        polymer init

1.  选择`element`.

1.  输入组件名称.

    [自定义组合规范](https://www.w3.org/TR/2016/WD-custom-elements-20160226/#concepts)规则组件名包含一个中划线.
    {.alert .alert-info}

1.  输入组件描述.

Polymer CLI这时会生成组件所需的文件和目录并安装项目的依赖.

### 组件项目构成

初始化完成后Polymer CLI生成如下的文件和目录.

*   `bower.json`. Bower的配置文件.
*   `bower_components/`. 项目依赖. 查看
    [管理依赖](#dependencies).
*   `demo/index.html`. <code><var>my-el</var></code>`.html`.
*   `index.html`的demo. 自动生成API文档.
*   <code><var>my-el</var></code>`.html`. 组件源码.
*   `test/`<code><var>my-el</var></code>`_test.html`. 组件的单元测试.

#### HTML imports和依赖管理{#element-imports}

简介:

When importing Polymer elements via 当通过HTML imports引用Polymer组件时, 要使用相对URL
<code>../<var>package-name</var>/<var>element-name</var>.html</code> (例如.
`../polymer/polymer.html` 或 `../paper-elements/paper-button.html`).

详情:

假设你用Polymer CLI生成一个名为`my-el`的组件项目. 打开`my-el.html`可以看到Polymer也这样引用:

    <link rel="import" href="../polymer/polymer.html">

路径不重要,相对于`my-el.html`,
Polymer实际上使用的是`bower_components/polymer/polymer.html`. 所以上面的HTML import引用位置在你的组件项目之*外*. 这是为什么呢?

Bower的依赖安装路径为:

    bower_components/
      polymer/
        polymer.html
      my-el/
        my-el.html
      other-el/
        other-el.html

这个在应用层一切正常. 所有的组件在同一层,所以他们都可以用`../polymer/polymer.html`这种方式来稳定的互相引用. 这也正是为什么Polymer CLI使用相对路径来初始化你的组件项目.

但是这个结构在你的组件项目里却是个问题. 组件项目的结构是这样的:

    bower_components/
      polymer/
        polymer.html
    my-el.html

Polymer CLI使用了重新映射路径来处理这个问题. 当你使用`polymer serve`时,
所有位于`bower_components`的组件被重新映射从而可以在`my-el`中使用相对路径. 当前组件的访问路径为 <code>/components/<var>bower name</var></code>,
<code><var>bower name</var></code>是`bower.json`文件中定义的你的组件`name`字段.

## 创建一个应用项目 {#app}

Polymer CLI支持利用一个模板来初始化你的项目目录.  CLI自带了一个`basic`模板,
可以用来创建一个简单的Polymer应用,同样也可以使用其它具有更多复杂而已和应用模式的模板.

这一节将使用`basic`应用模板.  See
[Polymer App Toolbox模板](../../toolbox/templates)可以看到更多模板详情.

### 应用项目结构 {#app-architecture}

Polymer CLI用来开发带有 [app shell
architecture](https://developers.google.com/web/updates/2015/11/app-shell)的应用.

在使用Polymer CLI来创建项目应用之前先理解App shell architecture的基本原理: 入口,
shell, fragments. 查看位于App Toolbox中的[App结构](/1.0/toolbox/server#app-structure)
来深入了解这些原理.

### 配置基本的应用项目 {#basic-app}

使用如下步骤来配置`基本`应用项目.

1.  为应用项目创建一个根目录.

    <pre><code>mkdir <var>app</var>
    cd <var>app</var></code></pre>

   <code><var>app</var></code>是根目录名称.

1.  初始化应用. Polymer CLI需要一些信息来配置应用.

        polymer init

1.  选择`application`.

1.  输入应用名称. 默认使用当前目录名称.

1.  输入主组件的名称.主组件是最顶层的应用级别组件. 默认使用当前目录名称,使用`-app`来指定.

    这个文档中的救命代码使用<code><var>my-app</var></code>做为救命应用组件的名称. 在创建应用时需要把<code><var>my-app</var></code>全部替换为你的主组件名称.
    {.alert .alert-info}

1.  输入应用的描述.

此时, Polymer CLI生成应用的文件和目录并安装项目依赖.

### 应用项目结构App project layout

完成初始化后Polymer CLI生成如下文件和目录.

*   `bower.json`.Bower的配置文件.
*   `bower_components/`. 项目依赖. 查看
    [管理依赖](#dependencies).
*   `index.html`. 应用的入口页面.
*   `src/`<code><var>my-app</var></code>/<code><var>my-app</var></code>`.html`.
    主组件的源码.
*    `test/`<code><var>my-app</var></code>/<code><var>my-app</var></code>`_test.html`. 主组件的测试.

#### 添加组件

你也可以从一些与应用专属的较小的组件来创建主组件.这些应用特定的组件放置于`src`目录中, 和<code><var>my-app</var></code>处于同一级.

    app/
      src/
        my-app/
        my-el/

现在Polymer CLI命令还不支持生成应用专属的组件. 可以手动创建但不能在应用项目中再创建一个[组件项目](#element).

## 命令 {#commands}

这一部分将介绍一些Polymer CLI命令,可以用来在开发组件或应用项目时放到自己的开发流程中.

这些命令除非单独说明均适用于组件和应用项目.

### 运行测试 {#tests}

想在组件或应用项目中运行测试, `cd`到项目根目录而后运行:

    polymer test

Polymer CLI自动运行所有位于`test`目录中的测试. 所有测试结果会显示在命令行中.

如果创建了自定义测试也应当放到`test`目录中.

`polymer test`使用了`web-component-tester` (`wct`)这个工具. 查看更多关于利用`wct`创建测试,点击
[组件测试](/1.0/tools/tests).

### 运行本地服务器{#serve}

运行本地web服务器来查看组件或应用的效果:

    polymer serve

在浏览器里导航到如上的URL.

组件项目demo:

<pre><code>http://localhost:8080/components/<var>my-el</var>/demo/</code></pre>

组件项目API文档:

<pre><code>localhost:8080/components/<var>my-el</var>/</code></pre>

应用项目demo:

    http://localhost:8080

#### 服务器选项 {#server-options}

这一部分展示了使用`polymer serve`选项的一些例子.

使用3000端口:

    polymer serve --port 3000

使用域名`test` (例如应用项目demo位于
`http://test:8080`):

    polymer serve --hostname 'test'

在指定浏览器中打开指定的页面而不是默认页面`index.html`
(此处使用Apple Safari):

    polymer serve --open app.html --browser Safari

### 组件静态检查 {#lint}

检查项目中的组件是否有语法错误或不符合规范等.

组件项目示例:

    polymer lint --input my-el.html

应用项目示例 (检查多个组件):

    polymer lint --root src/ --input my-app/my-app.html my-el/my-el.html

如果所要检查没的组件都位于同一个目录中,可以使用`--root`参数来指定所有`--input`文件.

### 构建应用 {#build}

*此命令只适用于应用项目.*

生成应用的生产版本. 这个过程包含压缩应用依赖的HTML, CSS和JS并生成一个service worker用来预缓存这个依赖.

Polymer CLI的构建过程适用于遵循了[app shell architecture](https://developers.google.com/web/updates/2015/11/app-shell)的应用.
构建命令有如下参数:

*   `entrypoint`. 应用的入口,必须引用`shell`参数中定义的app shell文件. 由于在每个页面中加载和缓存所以要体积尽量小I.
*   `shell`. 应用app shell文件提供通用代码.
*   `fragment`. 没有在app shell中同步加载的引用,比如异步引用或任何的按需引用(例如
    `importHref`).可以是空格分析或重复指定.
*   `sw-precache-config`.Polymer CLI用来生成service workers的参数, `sw-precache`接受配置选项. 可用使用`sw-precache`来指定配置文件的路径. 查看`sw-precache`中的README[选项参数 ][op]获得更多信息.

例如在新创建的`basic`应用项目中使用如下命令来构建:

    polymer build --entrypoint index.html

假设你为`basic`应用项目添加了一个app shell和两个视图,
位于`src/app-shell/app-shell.html`和`src/view-one/view-one.html`
、`src/view-two/view-two.html`. 在构建使用如下参数来指定:

    polymer build --entrypoint index.html --shell src/app-shell/app-shell.html /
    --fragment src/view-one/view-one.html src/view-two/view-two.html

[op]: https://github.com/GoogleChrome/sw-precache#options-parameter

#### 绑定和非绑定的构建Bundled and unbundled builds {#bundles}

Polymer CLI生成两个版本:

*   `bundled`. 所有fragments打包在一起来减少资源请求. are bundled together to reduce the number of file
    requests. 是不兼容HTTP/2的服务器或客户端的最佳选择.
*   `unbundled`. Fragments没有绑定. 是兼容HTTP/2的服务器和客户端的最佳选择.

#### 构建配置文件 (polymer.json) {#build-config}

比在命令行中使用参数指定的方式更好的是创建一个`polymer.json`文件,位于项目根目录中用来保存构建配置. For example例如:

```json
{
  "entrypoint": "index.html",
  "shell": "src/my-app/my-app.html",
  "fragments": [
    "src/view-one/view-one.html",
    "src/view-two/view-two.html",
    "src/view-three/view-three.html"
  ]
}
```

## 管理依赖 {#dependencies}

Polymer CLI使用[Bower](http://bower.io)做依赖管理.

所有依赖保存在`bower_components`目录中. 不要手动修改这个目录中的内容.

使用Bower CLI来管理依赖.

```bash
# downloads dependency to bower_components/
# --save flag saves new dependency to bower.json
bower install --save PolymerElements/iron-ajax
# removes dependency from bower_components/ and bower.json
bower uninstall PolymerElements/iron-ajax
```

## 全局参数 {#global-options}

运行`polymer help`来查看所有的全局参数. 大多遵循见名思义的规则.

以下参数目前只支持`polymer build`命令, 其它命令的支持已在计划中.

*   `entry`
*   `shell`
*   `fragment`

查看[构建应用](#build)来获取如何这些参数的更多信息.

