---
title: 使用Service Worker Precache技术进行离线缓存
---

为了在离线环境和不稳定的网络环境下提供更好的体验, App
Toolbox 使用了 service worker 来提供对重要资源的离线缓存.
Service worker 是一个关联于特定网站的脚本可用来做为客户端网络请求的代理.
Service worker 可以截获网络请求, 访问浏览器的缓存, 使用缓存来响应请求而不是访问网络.

用户第一次访问网站时, 浏览器安装了网站的 service
worker, service worker 确保网站的关键资源被缓存. 后续访问时, service worker 直接从缓存中加载资源.
如果用户处于离线状态, service worker 仍然可以加载网站, 并且显示离线数据或一个合适有离线消息.

Service worker 可以很好的配合一个 _app shell_ 策略, 应用的主UI视图和逻辑 ( app shell)
被缓存,以后这些就可以从缓存中响应请求.

App Toolbox 使用 Service Worker Precache (`sw-precache`) 模块来支持离线.
这个模块对列表中的文件进行缓存并在构建时生成一个 service
worker , 所以并不需要编写自己的 service worker 代码.

 对于service worker更多的调试和技巧查看在 Web Fundamentals 上的 [Service
Worker介绍](https://developers.google.com/web/fundamentals/primers/service-worker/) .

## 条件

要使用service worker, 应用 **必须** 以HTTPS进行响应. 然而, 如果在本地系统测试service worker就不需要一个SSL证书,
因为 `localhost` 被认为是安全的.

## 添加一个 service worker

 Service Worker Precache 支持添加到了 [Polymer CLI](/1.0/docs/tools/polymer-cli),
所以当构建应用时一个service worker脚本就会自动生成.

然后, 为了使用 service worker, 需要添加代码来注册它:

```js
// Register service worker if supported.
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/service-worker.js');
}
```

通过注册一个 service worker 并不能加速网站的首次加载时间, 所以可以在应用加载完成后进行注册.

## 配置 service worker

通过在构建命令中指定一个选项文件来配置 Service Worker Precache :

<code>polymer build --sw-precache-config <var>config-file</var>.json</code>

配置文件是一个 JavaScript 文件用来提供一组被
Service Worker Precache 所支持的配置选项. 查看`sw-precache`文明中的 [参数](https://github.com/GoogleChrome/sw-precache#options-parameter)
来获取更多信息.

如果使用 `--entrypoint`, `--shell` 和 `--fragment` 来定义资源, 这些文件被添加到 `staticFileGlobs` 参数来确保被正确缓存.

如果是一个单页应用并且希望具有完整的离线功能, 要指定一个 _fallback_ 文档, 以便请求的URL不在缓存中时使用.
对于单页应用, 这个跟入口是一样的.  使用
[navigateFallback](https://github.com/GoogleChrome/sw-precache#navigatefallback-string) and
[navigateFallbackWhitelist](https://github.com/GoogleChrome/sw-precache#navigatefallbackwhitelist-arrayregexp)
参数来配置 fallback.

下面的配置文件提供了一些通用选项, 包括当离线时回退到 `/index.html` 文件.

```js
module.exports = {
  staticFileGlobs: [
    '/index.html',
    '/manifest.json',
    '/bower_components/webcomponentsjs/webcomponents-lite.min.js',
    '/images/*'
  ],
  navigateFallback: '/index.html',
  navigateFallbackWhitelist: [/^(?!.*\.html$|\/data\/).*/]
}
```

只有匹配白名单的路径会回退到 `/index.html`. 此时白名单包含所有文件 _除了_ 以 `.html` (对应于 HTML imports) 结尾和位于 `/data/` 目录中的资源
(对应于动态加载的数据).

## 更多资源

库还支持其它的功能，包括运行时缓存应用的动态内容.

查看 [Service Worker Precache 入门](https://github.com/GoogleChrome/sw-precache/blob/master/GettingStarted.md)来获取更多信息.

Web Fundamentals中的[Service
Worker 介绍](https://developers.google.com/web/fundamentals/primers/service-worker/) 可以获取更多servic worker的背景知识.
