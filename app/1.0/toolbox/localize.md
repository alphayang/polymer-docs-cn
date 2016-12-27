---
title: 本地化
---

<!-- toc -->

**本地化处于预发布阶段.** API可能改变.
{.alert .alert-info}

[`Polymer.AppLocalizeBehavior`](https://elements.polymer-project.org/elements/app-localize-behavior)
封装了 [format.js](http://formatjs.io/) 库来帮助进行应用的国际化.
注意如果在没有原生支持
[Intl](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl) 对象的浏览器上,
**你必须加载一个polyfill.** 示例 polyfill
[github.com/andyearnshaw/Intl.js](https://github.com/andyearnshaw/Intl.js/).

`Polymer.AppLocalizeBehavior` 完全支持与`format.js`相同的
[消息语法](http://formatjs.io/guides/message-syntax/) ; 可以使用库文档来查看可用的消息格式和选项.

需要对显示内容进行本地化的组件都要添加 `Polymer.AppLocalizeBehavior`.
所有这些组件共享一个通用本地化缓存所以只需要加载一次翻译内容.

## 安装 AppLocalizeBehavior

使用Bower来安装 `app-localize-behavior` 包:

    bower install --save PolymerElements/app-localize-behavior


## 在应用中添加本地化

主应用通常用来负责加载本地化消息和设置当前语言.

示例应用加载一个外部资源文件. { .caption.}

```
<dom-module id="x-app">
   <template>
    <!-- use the localize method to localize text -->
    <div>{{localize('hello', 'name', 'Batman')}}</div>
   </template>
   <script>
      Polymer({
        is: "x-app",

        // include the behavior
        behaviors: [
          Polymer.AppLocalizeBehavior
        ],

        // set the current language—shared across all elements in the app
        // that use AppLocalizeBehavior
        properties: {
          language: {
            value: 'en'
          },
        }

        // load localized messages
        attached: function() {
          this.loadResources(this.resolveUrl('locales.json'));
        },
      });
   </script>
</dom-module>
```

主应用同样负责加载 `Intl` polyfill
(上边没有用到).

每个需要本地化消息的组件都要添加 `Polymer.AppLocalizationBehavior`
并如上所示使用 `localize` 方法来翻译字符串.
