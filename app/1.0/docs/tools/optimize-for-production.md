---
title: 为线上环境优化
---

<!-- toc -->

[Polymer CLI](polymer-cli)是构建Polymer应用的推荐入门工具. 如果它不能满足你的需求,可以利用其中的库来定制自己的工具链.查看
[高级工具](advanced#build)来获取更多工具信息.

这部分包含了其中的两个工具: Vulcanize
和Cripser. 此文档对于需要自定义开发工具链的人更有用.

## 概览

减少网络请求对于应用的体验和性能至关重要. 在Polymer中, [Vulcanize](https://github.com/Polymer/vulcanize)工具用来把组件和它们的HTML依赖引用都**合并**成一个文件. Vulcanize递归获取所有的引用和它们的依赖,然后输出可以**应用网络请求的**的资源. 另外, Polymer工具还可以用来转换代码到执行[content security policy (CSP)](#content-security-policy)的环境中，包含了Chrome Apps和扩展.

**说明:** 在使用HTMl Imports时关于更多的性能考虑请参照[HTML Imports - #include for the web](http://www.html5rocks.com/en/tutorials/webcomponents/imports/#performance)
{ .alert .alert-info }

使用如下步骤来配置或观看Polycast:

<google-youtube
  video-id="EUZWE8EZ0IU"
  autoplay="0"
  rel="0"
  fluid>
</google-youtube>

## 安装

安装Vulcanize和依赖，使用[NPM](http://npmjs.org):

    npm install -g vulcanize

推荐将Vulcanize安装到系统中可以在任何目录下使用命令行调用. 想要安装到本地,只需去掉`-g`参数.

## 使用

Vulcanize可以在命令行中单独使用也可以配合`gulp`/`grunt`一起使用.

### 在命令行中使用Vulcanize

有一个`elements.html`文件使用了HTML imports,使用Vulcanize处理:

    vulcanize elements.html -o elements.vulcanized.html

`-o` 或 `--output`参数指定新的输出文件`elements.vulcanized.html`. 如果去掉`-o`参数, Vulcanize输出到stdout, 可以用做其它工作的输入.

`elements.vulcanized.html`包含了`elements.html`以及所有的引用和依赖资源. 除了在JavaScript指定的URL外其它所有URL都被重写到新的输出位置.

Vulcanize还有很多参数. 可以查看 [Vulcanize官方文档](https://github.com/Polymer/vulcanize#using-vulcanize-programmatically)来获取更多信息.

依然是上边的示例. 参数指定Vulcanize剥离掉注释并合并所有的外部脚本和CSS文件到输出文件中.

    vulcanize -o elements.vulcanized.html elements.html --strip-comments --inline-scripts --inline-css

### 在Gulp中使用Vulcanize

尽管Vulcanize可以很好的处理引用,但你现在有构建系统中还要通过预处理器来混淆/压缩代码或CSS.这部分内部展示了在`gulp`中使用gulp-vulcanize](https://github.com/sindresorhus/gulp-vulcanize).

*在用Grunt?* 可用使用[grunt-vulcanize](https://github.com/Polymer/grunt-vulcanize). 参数都差不多[maybe link to README?]
{ .alert .alert-info }

将Vulcanize添加到构建流程中:

安装 `gulp-vulcanize`

    npm install --save-dev gulp gulp-vulcanize

`gulpfile`需要Vulcanize模块并添加一个任务来运行它 and add a task to run it.

```
var gulp = require('gulp');
var vulcanize = require('gulp-vulcanize');

gulp.task('vulcanize', function() {
  return gulp.src('app/elements/elements.html')
    .pipe(vulcanize())
    .pipe(gulp.dest('dist/elements'));
});

gulp.task('default', ['vulcanize']);
```

示例中假设项目有一个`elements.html`文件引用了其它web component依赖.
{ .alert .alert-info }

现在可以运行`gulp`和Vulcanize.

将一个添加对象传给Vulcanize可以添加`stripComments`, `inlineScripts`和`inlineCss`这些选项:

```
gulp.task('vulcanize', function() {
  return gulp.src('app/elements/elements.html')
    .pipe(vulcanize({
      stripComments: true,
      inlineScripts: true,
      inlineCss: true
    }))
    .pipe(gulp.dest('dist/elements'));
});
```

取决于应用的结构,除了把所有任务集合到一个文件中也可以分拆为多个小的vulcanized任务. 这个技术可以降低运行时间由于只加载组件所需的部分.

为了避免特定的引用被内联可以使用`excludes`和`stripExcludes`, 通过传输一个文件路径或正则表达式的数组.

```
gulp.task('vulcanize', function() {
  return gulp.src('app/elements/elements.html')
    .pipe(vulcanize({
      excludes: ['elements/x-foo.html'],
      stripExcludes: ['elements/x-foo.html']
    }))
    .pipe(gulp.dest('dist/elements'));
});
```

如果希望为组件留下链接标记可能忽略`stripExcludes`选项. 这样可以防止资源被_内联_. 对于多个页面使用不同的组件集合（但全部依赖`polymer.html`）非常有用,这样就可以单独保留`polymer.html`文件以便浏览器可以有效的进行缓存.

#### 示例

Polymer应用有四个HTML文件: index.html, elements/elements.html, elements/x-foo.html, 和 elements/x-bar.html.

app/index.html:

```
<!doctype html>
<html>
  <head>
    <script src="bower_components/webcomponentsjs/webcomponents-lite.js"></script>
    <link rel="import" href="elements/elements.html">
  </head>
  <body>
   ...
  </body>
</html>
```

app/elements/elements.html:

```
<link rel="import" href="x-foo.html">
<link rel="import" href="x-bar.html">
```

 app/elements/x-foo.html:

```
<link rel="import" href="../bower_components/polymer/polymer.html">
<dom-module id="x-foo">
  <template>
    <p>Hello from x-foo!</p>
  </template>
  <script>
    Polymer({
      is: 'x-foo'
    });
  </script>
</dom-module>
```

 app/elements/x-bar.html:

```
<link rel="import" href="../bower_components/polymer/polymer.html">
<dom-module id="x-bar">
  <template>
    <p>Hello from x-bar!</p>
  </template>
  <script>
    Polymer({
      is: 'x-bar'
    });
  </script>
</dom-module>
```

没有经过合并的话,加载这个应用需要至少5个网络请求. 我们来降低这个请求数量. 运行Vulcanize处理`elements/elements.html`, 指定`elements.vulcanized.html`为输出:

    vulcanize elements.html -o elements.vulcanized.html

输出文件`elements.vulcanized.html` 的内容:

```
<!-- all the code for polymer.html -->
<dom-module id="x-foo" assetpath="/">
  <template>
    <p>Hello from x-foo!</p>
  </template>
  <script>
    Polymer({is: 'x-foo'});
  </script>
</dom-module>
<dom-module id="x-bar" assetpath="/">
  <template>
    <p>Hello from x-bar!</p>
  </template>
  <script>
    Polymer({is: 'x-bar'});
  </script>
</dom-module>
```

## Content Security Policy

[Content Security Policy](http://en.wikipedia.org/wiki/Content_Security_Policy) (CSP)是一种JavaScript安全模型用来防止XSS和其它攻击. 它禁止使用内联脚本.

在CSP环境(如Chrome App或扩展)中使用Polymer,可以利用Crisper项目. Crisper移除了所有HTML Imports中的脚本并合并到一个单独的JavaScript文件中.

<google-youtube
  video-id="VrajHIZZbE4"
  autoplay="0"
  rel="0"
  fluid>
</google-youtube>

如同Vulcanize, Crisper可以在命令行中使用或利用`gulp`插件.

### 命令行

使用如下命令安装Crisper:

    npm install -g crisper

Crisper可以直接通过管道使用Vulcaize的输出,比如:

    vulcanize elements/elements.html --inline-script | crisper --html elements/elements.vulcanized.html --js elements/elements.vulcanized.js

好像有点奇怪先用了`vulcanize` 带 `--inline-script`参数再传递给`crisper` 分离出JavaScript. 其实如果你的组件使用了外部脚本,这个参数可以确保不论是内联或是外部脚本都被提取并合并到`elements.vulcanized.js`.
{ .alert .alert-info }

### Gulp

同样的, 可以在`gulp`中通过管道让Crisper任务使用Vulcanize的输出y.

Run the following command:

    npm install -g gulp-crisper

添加到`gulpfile`:

```
var gulp = require('gulp');
var vulcanize = require('gulp-vulcanize');
var crisper = require('gulp-crisper');

gulp.task('vulcanize', function() {
  return gulp.src('app/elements/elements.html')
    .pipe(vulcanize())
    .pipe(crisper())
    .pipe(gulp.dest('dist/elements'));
  });

gulp.task('default', ['vulcanize']);
```

## FAQ

### 合并好吗?

这取决于你的应用有多大. 过度请求远比大文件效果差. 假设, 有20个0.5MB的文件.其中只有2(1MB)个在第一个页面中需要.你可以将这个2个请求通过Vulcaize合并为一个包, 然后使用Polymer’s importHref来延迟分别请求其它的文件.

例如:

```
Polymer.Base.importHref('/elements/less-important-stuff.html',
  // onsuccess callback
  function() {
    console.log('yay! our app is ready!');
  },
  // onerror callback
  function(err) {
    console.log('uh oh, something failed', err);
  },
  // use `async` on import
true);
```

通过合并100个文件可以减少99次请求.但是合并也有一些缺点:

* 单个文件需要更长时间来下载从而阻塞了重要页面的加载.

* 浏览器需要解析和渲染额外的还不需要的代码.

答案是"不用猜, 而用测". 使用合并是总有一个妥协, 但是用 [WebPageTest](http://webpagetest.org)这些有用的工具可以帮我们确定到底真正的瓶颈是什么.
