---
title: 浏览器兼容性
---

Polymer 1.0+的版本兼容各大浏览器_最新的2个版本号_: Safari 7+, IE 11+和 Chrome, Firefox, and Edge.

## 浏览器支持

Polymer是基于[Web Components
APIs](http://webcomponents.org/articles/why-web-components/)的轻量代码. 不同于典型的Ujavascript框架,
Polymer设计用于提供web平台自身特性来创建组件. 甩以一些Polymer特性没有被所有浏览器支持. 为了获得更广泛的web components支持, Polymer使用[polyfills](http://webcomponents.org/polyfills/)由
[webcomponents.org](http://webcomponents.org)提供. 轻量级的代码用来支持Polymer的特性.

通过polyfills, Polymer支持以下浏览器:

<table>
<thead>
  <tr><th></th><th>Chrome</th><th>Firefox</th><th>IE&nbsp;11+/Edge</th><th>Safari 7+</th><th>Chrome
  (Android)</th><th>Safari (iOS&nbsp;7.1)</th></tr>
</thead>
<tr>
  <td class="feature-title"><a href="http://www.html5rocks.com/en/tutorials/webcomponents/template/">Template</a></td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
</tr>
<tr>
  <td class="feature-title"><a href="http://www.html5rocks.com/en/tutorials/webcomponents/imports/">HTML Imports</a></td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
</tr>
<tr>
  <td class="feature-title"><a href="http://www.html5rocks.com/en/tutorials/webcomponents/customelements/">Custom Elements</a></td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
</tr>
<tr>
  <td class="feature-title"><a href="http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom/">Shadow DOM</a></td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
  <td>✅</td>
</tr>
</table>

说明:

-   老版本的Android浏览器可能会有问题- 请提交
    [issue](https://github.com/polymer/polymer/issues)如果你在这类浏览器上遇到了.
    Chrome for Android是支持的.

-   我们建议有条件的加载polyfills到你的应用中:使用服务器或客户端的检测功能来查看浏览器是否原生支持web components,只在不支持的支持加载polyfills,使用基于标准的功能的优点是运行应用程序所需的有效载荷将会随着浏览器实现而减少. 如果你安装polyfills是通过
    `bower install --save webcomponents/webcomponentsjs`, 这里有一些示例代码用来进行客户端特性检测h:

    ```
    (function() {
      if ('registerElement' in document
          && 'import' in document.createElement('link')
          && 'content' in document.createElement('template')) {
        // platform is good!
      } else {
        // polyfill the platform!
        var e = document.createElement('script');
        e.src = '/bower_components/webcomponentsjs/webcomponents-lite.min.js';
        document.body.appendChild(e);
      }
    })();
    ```

#### 使用webcomponents-lite.js 还是 webcomponents.js?

我们推荐使用`webcomponents-lite.js`版本的polyfills同Polymer 1.0+. 该版本可以与[Shady DOM](https://www.polymer-project.org/1.0/blog/shadydom.html)一同工作,
没有包含完整的Shadow DOM polyfill.

尽管完整的`webcomponents.js` polyfill和Polymer 1.0+配合, 但我们不建议这么用.
这个版本包含完整的TShadow DOM polyfill,已知需要消耗大量资源.

**查看** twebcomponents.js [compatibility matrix](https://github.com/WebComponents/webcomponentsjs#browser-support)获取更多支持.
{: .alert .alert-info }

#### Polymer或其它组件使用的其它特性

IE 10已经不支Microsoft支持，大多数Polymer功能可以正常使用，但也可能遇到问题."官方"支持是IE
11/Edge.

## Progress of native browser support

截止2016-05, 围绕v1版本的[Custom
Elements](https://w3c.github.io/webcomponents/spec/custom/)和[Shadow
DOM](https://w3c.github.io/webcomponents/spec/shadow/) APIs已经有了广泛的跨浏览器厂商协议,在主流浏览器中正在实现支持.

Polymer现在依赖于这些API的V0版本, 同时也支持
web components polyfills. Polymer会很快迁移到V1版本. 当前版本的组件在v1 API依然可用，只要使用v0 polyfills. 可以简单的做好从v0到v1的升级工作得益于Polymer已经提供了这些低级别和特性相关的轻量级抽象.

**查看** [Are We Componentized Yet?](http://jonrimmer.github.io/are-we-componentized-yet/) 和
[caniuse.com](http://caniuse.com/) 获取更多本地浏览器支持web components的信息.
{: .alert .alert-info }

说明:

-   Chrome原生实现了v0 APIs，正在实现v1 APIs.

-   WebKit Nightly正在实现Shadow DOM v1.

-   Edge的备忘录中要支持[Shadow
    DOM v1](https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer/suggestions/6263785-shadow-dom-unprefixed)
    和[Custom Elements v1](https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer/suggestions/6261298-custom-elements).

-   Firefox现在通过配置可以支持部分v0 web components. Polymer
    **不能正常工作**在打开这些开关后, 由于和web
    components polyfills不兼容.
