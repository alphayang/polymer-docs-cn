---
title: 使用 <app-route> 进行路由
---

<!-- toc -->

**组件路由处于预发布阶段.** API可能改变.
{.alert .alert-info}

对于客户端路由, App Toolbox使用
[`<app-route>`](https://elements.polymer-project.org/elements/app-route) 组件来提供
_modular routing_. 组件路由意味着没有一个中心仓库来提供所有应用的路由信息,而是由每个组件自己来管理各自的路由并委托剩余的路由给其它组件.

**为什么使模块化路由?**  `<app-route>` 和模块化路由的背景信息, 查看
[带路由封装的组件](/1.0/articles/routing).
{.alert .alert-info}

## 安装应用路由

使用Bower安装 `app-route` 包:

    bower install --save PolymerElements/app-route

## 添加路由Add routing

首先要决定应用路由如何对应到组件. 例如, 如果一个应用有几个主视图:

| View | Route |
| :--- | :---- |
| User profile view | <code>/profile/<var>:user_id</var></code> |
| Message list | <code>/messages</code> |
| Message view | <code>/detail/<var>:message_id</var></code> |

有一个主应用组件和每个标签对应的视图组件. 应用组件管理最顶层的路由, 选择当前的显示视图, 并 _委托_ 剩余的路由给活动视图t. 应用组件模板可能包含如下标记:

```
<!-- app-location binds to the app's URL -->
<app-location route="{{route}}"></app-location>

<!-- this app-route manages the top-level routes -->
<app-route
    route="{{route}}"
    pattern="/:view"
    data="{{routeData}}"
    tail="{{subroute}}"></app-route>
```

`<app-location>` 组件是一个简单  `window.location` 代理用来提供双向数据绑定t. 一个 `<app-location>` 组件绑定到顶层 `<app-route>` 组件的URL栏.

`<app-route>` 组件使用一个`pattern`来匹配当前的 `route`  ( `:view` 表示一个参数
). 如果匹配成功, 路由被 _激活_ 并且任意的URL参数被添加到
`data` 对象. 此时,  `/profile/tina` 路径匹配到顶层路由, 添加
`routeData.view` 到 `profile`. 其它的路由 (`/tina`) 形成 `tail`.

基于路由, 应用就可以用 `<iron-pages>` 来选择一个视图进行显示:

```
<!-- iron-pages selects the view based on the active route -->
<iron-pages selected="[[routeData.view]]" attr-for-selected="name">
  <my-profile-view name="profile" route="{{subroute}}"></my-profile-view>
  <my-message-list-view name="messages" route="{{subroute}}"></my-message-list-view>
  <my-detail-view name="detail" route="{{subroute}}"></my-detail-view>
</iron-pages>
```

如果当前URL是 `/profile/tina`,  `<my-profile-view>` 组件会被显示, 以及 _它的_
路由被设置为 `/tina`. 这个视图可以内嵌自己的 `<app-route>` 来处理路由: 例如,
加载用户数据:

```
<app-route
    route="{{route}}"
    pattern="/:user_id"
    data="{{routeData}}"></app-route>
<iron-ajax url="{{_profileUrlForUser(routeData.user_id)}}
           on-response="handleResponse" auto>
```


## 路由对象

 `route` 对象包含两个属性:

-   `prefix`. 路径匹配到上一个 `<app-route>` 组件. 对于顶层 `<app-route>` 组件, `prefix` 总是空字符串.
-   `path`. 路由对象匹配到的路径.

 `tail` 对象同样是一个路由对象, 表示传递给下一个 `<app-route>` 的路由.

例如, 如果当前URL是 `/users/bob/messages` 并且顶层
`<app-route>` 有 `/users/:user' 这样的模式:

 `route` 对象是:

    {
      prefix: '',
      path: '/users/bob/messages'
    }

 `tail` 对象是:

    {
      prefix: '/users/bob',
      path: '/messages'
    }

 `routeData` 对象是:

    {
      user: 'bob'
    }

## 改变路由

使用 `<app-route>` 时, 有两种方式来改变当前URL.

-   链接. 当用户点击一个链接, `<app-location>` 截获导航事件并更新它的 `route` 属性. 使用链接作为主导航是一个很好的主意,有助于帮助搜索引擎更好的理解应用结构.

-   更新路由.  `route` 对象可读写, 可以使用双向数据绑定或 `this.set` 来更新路由.  `route`
    和 `routeData` 对象都可以这样来操作. 例如:

    `this.set('route.path', '/search/);`

    或:

    `this.set('routeData.user', 'mary');`

## 路由改变时的操作

上一节中展示了数据绑定到路由和路由数据, 但是有时需要在路由改变时进行某些操作. 使用观察器就可以简单的在路由或数据改变时作出反应:

```
observers: [
  '_routeChanged(route.*)',
  '_viewChanged(routeData.view)'
],
_routeChanged: function(changeRecord) {
  if (changeRecord.path === 'path') {
    console.log('Path changed!');
  }
},
_viewChanged: function(view) {
  // load data for view
}
```

## 更多资源

-   [Encapsulated routing with elements](/1.0/blog/routing)
-   [`<app-route>`
    API reference](https://elements.polymer-project.org/elements/app-route)
