---
title: 服务
---

<!-- toc --> 

本文提供了一些在创建Polymer组件时有用的服务. 

## <b>polygit</b>—托管组件的CDN web服务 {#polygit}

[Polygit](http://polygit.org/)是一个通过CDN来托管组件的代理服务器. **不适用于生产应用**, 但对于开发原型和分享jsbins很有用.

使用:

```
<head>
  <base href="https://polygit.org/components/"> <!-- saves typing! -->
  <script src="webcomponentsjs/webcomponents-lite.min.js"></script>
  <link rel="import" href="paper-button/paper-button.html">
  <link rel="import" href="iron-selector/iron-selector.html">
</head>
```

查看更多[http://polygit.org](http://polygit.org/).

源码: [github.com/PolymerLabs/polygit](https://github.com/PolymerLabs/polygit)

## <b>polystyle</b>—创建模块样式的web服务 {#polystyle}

[polystyle](https://poly-style.appspot.com/demo/)是一个将现有远程服务器上的样式包装为Polymer[style module](/1.0/docs/devguide/styling#style-modules)的web服务. 对于托管的第三方样式使用到你的组件或应用中非常有用.

使用:

```
<head>
  <link rel="import" href="bower_components/polymer/polymer.html">
  <link rel="import" href="https://poly-style.appspot.com?id=theme-styles&url=https://example.com/styles.css">
  <style is="custom-style" include="theme-styles">
    ...
  </style>
</head>
```

查看更多[https://poly-style.appspot.com/demo/](https://poly-style.appspot.com/demo/).

**相关工具**

- [gulp-style-modules](https://github.com/MaKleSoft/gulp-style-modules)—第三方Gulp插件用来包装本地CSS文件到style modules

源码: [github.com/PolymerLabs/polystyles](https://github.com/PolymerLabs/polystyles)

## <b>polyicon</b>—创建一个优化过的自定义图标集 {#polyicon}

[polyicon](https://github.com/PolymerLabs/polyicon) 用来创建只包含应用需要的经过化的自定义图标集的在线工具.不用加载整个集合,这个工具创建一个自定义的图标集用在应用中.

直接使用: [https://poly-icon.appspot.com/](https://poly-icon.appspot.com/)

源码: [github.com/PolymerLabs/polyicon](https://github.com/PolymerLabs/polyicon)
