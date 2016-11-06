---
title: App storage
---

<!-- toc -->

**App storage组件处于Preprelease状态。** APIs可能会改变。。.
{.alert .alert-info}

App storage组件集合提供了一组在应用中操作数据的新工具。初始的工具集包含ready-made组件用来整合Firebase和PouchDB。

## Firebase整合

Firebase 3.0.0 SDK 支持了一组新的Firebase组件，使用了app storage，称为
[PolymerFire](https://github.com/Firebase/PolymerFire). 这些组件使得Firebase SDK的关键整合如应用初始化、用户认证、数据库访问声明都变得更加容易。

### 离线数据镜像

[`<app-indexeddb-mirror>`](https://elements.polymer-project.org/elements/app-storage?active=app-indexeddb-mirror)组件提供了数据库如Firebase的只读镜像。这样可以保证用户在没有网络的情况下也能访问自己的个人数据。


Firebase对网络连接的临时丢失具有弹性，就像用户突然通过隧道还可以同时使用您的Firebase应用程序。 Firebase将继续工作并在网络恢复连接后尽快更新服务器。 但是在其它离线情况比如用户在断网时启动应用，Firebase和其它流行的存储层都不能很好的处理。


## PouchDB Elements

 Polymer [`app-pouchdb`](https://elements.polymer-project.org/elements/app-pouchdb) 组件包含PouchDB数据访问、查询、本地和远程同步和使用远程CouchDB数据进行用户认证的组件。由于PouchDB可以自动同步一个本地的IndexedDB数据库，在你的progressive web app中优先添加离线数据访问变得无比轻松。
