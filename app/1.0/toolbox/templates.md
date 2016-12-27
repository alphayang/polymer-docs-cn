---
title: 应用模板
---

<!-- toc -->

You can get started with the Polymer App Toolbox using one of several templates
that incorporate the elements and patterns discussed here using the Polymer CLI.
通过Polymer CLI就可以用Polymer App Toolbox中的一些模板开始使用这里提到的组件和模式

## 使用模板初始化项目

为了使用模板来初始化项目需确保已经安装了
[Polymer CLI](../tools/polymer-cli), `cd` 到一个空的项目目录,
运行以下命令, 就会提示你从可用模板中选择一个.

```
    $ polymer init
```

## 模板

### 基本应用模板

`application` 模板是最常用的Polymer应用切入点.  使用一个骨架自定义组件做为应用的根，从这里可以有最大的灵活性向任意方向拓展.

### 应用抽屉模板

![](/images/1.0/toolbox/app-drawer-template-desktop.png)

`app-drawer-template` 引入了 [`app-layout`](app-layout) 组件,
并且配合一个工具栏组成一个通用的左手抽屉布局.
模板提供了在主内容区域加载和渲染的视图之间导航.

模板使用了 [PRPL pattern](server) 来提高效率,利用渐进式应用加载,视图被按需加载并且被离线预缓存备用.

### 商店示例应用

![](/images/1.0/toolbox/shop-template-desktop.png)

`shop` 模板是一个使用了`app-drawer-template`并且使用了一系列组件来打造电商应用的成熟应用.  体现了一个经典的
"主面 - 列表 - 详情" 类型的应用流, 可作为启迪或完整应用的切入点.

## 下一步

模板只是开始, 下一步可以根据需要任意添加在Polymer App Toolbox文档中提到的web组件来构建你的应用, 以及
[Polymer Element Catalog](https://elements.polymer-project.org/)中的组件.

查看 [开发第一个Polymer应用](../start/toolbox/set-up)
教程来使用一个App Toolbox模板进行开发.
