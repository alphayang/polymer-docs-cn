---
title: 试验性功能和组件
---

<!-- toc -->

## 功能分层 {#feature-layering}

**实验: API可能改变.**
{ .alert .alert-error }

Polymer当前分为三个功能层来提供3个相互分享的HTML引入,各个组件开发者就可以只使用所需功能的Polymer版本.开发者如果只用local DOM或数据绑定功能,组件就不会使用过度依赖,也就是说运行时用户不会为页面功能不需要的这些高级功能消耗资源.总之,所有的功能都被设计为不被组件使用时只使用最低限度的运行时资源.

高层依赖于低层,组件所需要的低层实际用页面中最高层的Polymer版本来填充(这些组件不使用/利用高级功能).
这样就做了很好的妥协,组件开发者既可以避免组件使用时依赖无用的功能又以可以让终端用户可以在页面中混合使用不同层的组件.

*   `polymer-micro.html`: [最小功能版的Polymer](#polymer-micro) (最小自定义组件)

*   `polymer-mini.html`: [迷你功能版的Polymer](#polymer-mini) (模板中提供了"local DOM"和DOM树生命周期)

*   `polymer.html`: [标准功能版的Polymer](#polymer-standard) (迷你功能版本外的所有功能: 声明式数据绑定和事件处理器,属性通知,
    计算属性以及实验性功能)

这个分层在将来可能改变,层数可能减少.

### 最小功能的Polymer {#polymer-micro}

Polymer最小功能版提供了最小自定义组件.


| Feature | Usage
|---------|-------
| [自定义组件构造器](registering-elements#element-constructor) | Polymer.Class({ … });
| [自定义组件注册](registering-elements#register-element) | Polymer({ is: ‘...’,  … }};
| [自定义构造器支持](registering-elements#bespoke-constructor) | constructor: function() { … }
| [基本生命周期回调](registering-elements#basic-callbacks) | created, attached, detached, attributeChanged
| [原生HTML组件扩展](registering-elements#type-extension) | extends: ‘…’
| [声明的属性](properties) | properties: { … }
| [标记反序列化为属性](properties#attribute-deserialization) | properties: { \<property>: \<Type> }
| [宿主上的静态标记](registering-elements#host-attributes) | hostAttributes: { \<attribute>: \<value> }
| [行为](behaviors) | behaviors: [ … ]


### 迷你功能版的Polyme {#polymer-mini}

Polymer迷你功能版提供了与local DOM相关的功能:
模板内容克隆到了自定义组件的local DOM,DOM API以及DOM树生命周期.

| Feature | Usage
|---------|-------
| [模板内容克隆到local DOM](local-dom#template-stamping) | \<dom-module>\<template>...\</template>\</dom-module>
| [DOM分布](local-dom#dom-distribution) | \<content>
| [DOM API](local-dom#dom-api)  | Polymer.dom
| [配置默认值](properties#configure-values)  | properties: \<prop>: { value: \<primitive>\|\<function> }
| [配置后向上传递回调](registering-elements#ready-method) | ready: function() { … }

<a name="polymer-standard"></a>

### 标准功能版的Polymer {#polymer-standard}

Polymer标准功能版添加了声明式数据绑定,事件,属性通知和实用方法.

| Feature | Usage
|---------|-------
| [自动结点查找](local-dom#node-finding) | this.$.\<id>
| [配置事件监听器](events#event-listeners)| listeners: { ‘\<node>.\<event>’: ‘function’, ... }
| [批注式配置事件监听器](events#annotated-listeners) | \<element on-[event]=”function”>
| [属性更改回调](properties#change-callbacks) | properties: \<prop>: { observer: ‘function’ }
| [批注式属性绑定](data-binding#property-binding) | \<element prop=”{{property\|path}}”>
| [属性更改通知](data-binding#property-notification) | properties: { \<prop>: { notify: true } }
| [绑定到结构化数据](data-binding#path-binding) | \<element prop=”{{obj.sub.path}}”>
| [路径更改通知](data-binding#set-path) | set(\<path>, \<value>)
| [声明式标记绑定](data-binding#attribute-binding) | \<element attr$=”{{property\|path}}”>
| [属性映射到标记](properties#attribute-reflection) | properties: { \<prop>: { reflectToAttribute: true } }
| [计算属性](properties#computed-properties) | computed: { \<property>: ‘computeFn(dep1, dep2)’ }
| [计算绑定](data-binding#annotated-computed) | \<span>{{computeFn(dep1, dep2)}}\</span>
| [只读属性](properties#read-only) |  properties: { \<prop>: { readOnly: true } }
| [实用函数](instance-methods) | toggleClass, toggleAttribute, fire, async, …
| [范围内样式](styling) | \<style> in \<dom-module>, Shadow-DOM styling rules (:host, ...)
| [常用polymer配置](#settings) | \<script> Polymer = { ... }; \</script>
