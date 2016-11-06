---
title: 高级工具
---

<!-- toc -->

[Polymer CLI](polymer-cli)是开发Polymer组件的官方推荐工具.如果它还满足不了你的需求，可以查看下面的这些工具。可以将它们与Gulp或Grunt相结合来创建属于自己的工作流程.

## 开发

### <b>polyserve</b>—针对组件开发的web服务器 {#polyserve}

*等同于Polymer CLI命令`polymer serve`.*

[polyserve](https://github.com/PolymerLabs/polyserve)是一个简单的web服务器可以用来提供`bower_components`的本地访问. 对于组件开发很有用.

polyserve提供的组件访问基于当前目录`/components/{element-name}/`,`element-name`在`bower.json`中定义. 其它的依赖资源位于`./bower_components/`.

安装:

    npm install -g polyserve

使用:

    cd my-element/
    polyserve -p 8080

源码: [github.com/PolymerLabs/polyserve](https://github.com/PolymerLabs/polyserve)

### <b>polylint</b>—静态检查工具用来发现错误和常见问题{#polylint}

*等同于Polymer CLI命令`polymer lint`.*

[polylint](https://github.com/PolymerLabs/polylint)静态检查工作用来检测项目中的错误和常见问题.

安装:

    npm install -g polylint

使用:

    polylint --root my-project/ \
        --input elements.html my-element.html

**源码:** [github.com/PolymerLabs/polylint](https://github.com/PolymerLabs/polylint)

### Web component tester—组件单元测试工具 {#wct}

*等同于Polymer CLI命令`polymer test`.*

[Web component tester](https://github.com/Polymer/web-component-tester)工具提供了一个基于浏览器的组件测试环境. 原生支持Mocha, Chai, Async和Sinon. 查看[测试组件](tests)获取更多详情.

安装:

    npm install -g web-component-tester

使用:

    wct
    wct -l chrome (只在Chrome中运行测试)

默认情况下, 所有位于`test/`都会运动. 但也可以通过指定部分（或全部），例如`wct path/to/files`.

如果你不喜欢使用WCT'的命令行工具, 你也可以[在浏览器中直接运行WCT测试](https://github.com/Polymer/web-component-tester#web-server)使用你选用的web服务器.

WCT更多详情, 查看[组件测试](tests).

源码: [github.com/Polymer/web-component-tester](https://github.com/Polymer/web-component-tester)

## 构建和优化B {#build}

这一部分的命令被用于Polymer CLI命令`polymer build`.
在自己的构建流程中可以直接使用它们.

### <b>vulcanize</b>—优化HTML Imports {#vulcanize}

[vulcanize](https://github.com/polymer/vulcanize)是一个用来将一个HTML Import文件和它的所有HTML Imports依赖合并成一个文件的工具.构建web组件的一个步骤.Vulcanizesk可以减少应用的资源请求数据通过合并imports到一个文件中.

安装:

    npm install -g vulcanize

使用 (CLI):

    vulcanize --inline-scripts --inline-css --strip-comments \
        elements.html > elements.build.html

Vulcanize可以在CLI中使用也可以在Node中用代码调用, `gulp-vulcanize`,或`grunt-vulcanize`.vulcanize的更多信息[为线上版本优化](optimize-for-production).

源码: [github.com/Polymer/vulcanize](https://github.com/Polymer/vulcanize)

相关工具

- [gulp-vulcanize](https://www.npmjs.com/package/gulp-vulcanize)
- [grunt-vulcanize](https://www.npmjs.com/package/grunt-vulcanize)
- [broccoli-vulcanize](https://www.npmjs.com/package/broccoli-vulcanize)

### <b>crisper</b>—从一个HTML Import中提取内联脚本 {#crisper}

[crisper](https://github.com/PolymerLabs/crisper)工具用于从HTML文件中提取内联脚本然后放到一个单独的文件中. 可以让你的应用符合CSP（内容安全政策）的.

安装:

    npm install -g crisper

使用:

    crisper --html build.html --js build.js index.html

源码: [github.com/PolymerLabs/crisper](https://github.com/PolymerLabs/crisper)

相关工具

- [gulp-crisper](https://www.npmjs.com/package/gulp-crisper)
- [grunt-crisper](https://www.npmjs.com/package/grunt-crisper)

### <b>polyclean</b>—压缩JS/CSS/HTML {#polyclean}

[polyclean](https://github.com/PolymerLabs/polyclean)是一个Gulp插件,用来用来压缩和清理JS, CSS, and HTML.

安装:

    npm install -g polyclean

使用:

    vulcanize --inline-css --inline-scripts index.html | polyclean

源码: [github.com/PolymerLabs/polyclean](https://github.com/PolymerLabs/polyclean)

### <b>polybuild</b>—是优化应用的全套工具{#polybuild}

*等同于Polymer CLI命令`polymer build`.*

[polybuild](https://github.com/PolymerLabs/polybuild)工具组合了vulcanize, crisper和polyclean. 尽管没有单独使用这些工具那么灵活, polybuild适用于快速上手来做一些简单的应用.

安装:

    npm install -g polybuild

使用:

    polybuild index.html --maximum-crush

源码: [github.com/PolymerLabs/polybuild](https://github.com/PolymerLabs/polybuild)


