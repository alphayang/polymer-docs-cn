---
title: Polymer全局设置
---

页面级别的全局Polymer设置可以通过在引用Polymer之前创建一个属于window的`Polymer`对象:

```
<html>
  <head>
  <meta charset="utf-8">
  <script src="components/webcomponentsjs/webcomponents-lite.js"></script>
  <script>
    /* this script must run before Polymer is imported */
    window.Polymer = {
      dom: 'shadow',
      lazyRegister: true
    };
  </script>
  <!-- import a component that relies on Polymer -->
  <link rel="import" href="elements/my-app.html">
  </head>
  <body>
  ...
```

也可以通过URL查询字符串来设置:

    http://example.com/test-app/index.html?dom=shadow

可用设置:

*   `dom`—选项:
    * `shady`. 使用shady DOM渲染也有local DOM就算支持shadow DOM时也一样(当前默认使用此选项).
    * `shadow`. Local DOM在支持shadow DOM是使用shadow DOM做渲染(未来会默认使用此选项).

*   `lazyRegister`—选项:
    * `true`, 将一些注册时的活动延迟到第一个组件实例被创建时.默认是`false`. (默认值将来可能改变.)
    * `"max"`, 延迟所有行为执行一直到第一个组件被创建.当设置`lazyRegister`为`"max"`时,不能改变一个组件的`is`属性或通过定义`factoryImpl`方法来创建一个自定义构造函数. Polymer会调用组件的`beforeRegister`用以保留使用ES6定义组件的能力.组件的`beforeRegister`会在特性的`beforeRegister`之前调用.
*   `useNativeCSSProperties` - 为`true`时, Polymer在浏览器支持时使用本地自定义CSS属性. 默认是`false`由于Safari 9还不支持. 查看[1.6.0 release notes](https://www.polymer-project.org/1.0/docs/release-notes#v-1-6-0)获取更多信息.
*   `noUrlSettings`- 为`true`时, Polymer的设置只能通过页面上脚本来设置. 也就是说通过URL查询字符串设置的`?dom=shadow`会被忽略. 默认为`false`.
