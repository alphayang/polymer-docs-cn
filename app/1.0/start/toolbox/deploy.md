---
title: Step 4. 发布
subtitle: "使用App Toolbox构建应用"
---

<!-- toc -->

在一节中，你会将应用发布到web。

## 发布前的Build

运行Polymer CLI命令来做发布前的一系列准备动作:

    polymer build

这个命令，包含了HTML, JS, 和 CSS 依赖管理,还生成了service worker来预缓存应用所有的依赖，这样应用就可以离线工作。

输入文件夹:

* `build/unbundled`. 包含了支持HTTP/2服务器推送的资源。
* `build/bundled`. 包含了不支持HTTP/2服务器推送的资源。

## 发布到服务器

Polymer应用可以发布到任意的web服务器。

这个模板使用了`<app-location>`组件来确保URL路由的正确，需要服务器把`index.html`作为应用的入口。

下面将应用发布到
[Google AppEngine](https://cloud.google.com/appengine) 或 [Firebase
Static Hosting](https://www.firebase.com/docs/hosting/), 都提供免费且安全的Polymer应用部署环境。这个部署和其它的托管服务商类似。 

### 发布到AppEngine

1. 下载[Google App Engine SDK](https://cloud.google.com/appengine/downloads)
并按照相应的说明安装.

1.  [注册AppEngine帐号](https://cloud.google.com/appengine).

1.  [打开项目管理面板](https://console.cloud.google.com/iam-admin/projects)
创建一个新项目

    * 点击Create Project按钮。
    * 输入项目名称。
    * 点击Create按钮。

1.  `cd` 进入到项目文件夹.

1. 创建一个`app.yaml`文件，让服务器使用
`index.html`来做为应用的入口。
替换 `{project name}` 为你之前选择的项目名称.

    ```
    application: {project name}
    version: 1
    runtime: python27
    api_version: 1
    threadsafe: yes

    handlers:
    - url: /bower_components
      static_dir: build/bundled/bower_components
      secure: always

    - url: /images
      static_dir: build/bundled/images
      secure: always

    - url: /src
      static_dir: build/bundled/src
      secure: always

    - url: /service-worker.js
      static_files: build/bundled/service-worker.js
      upload: build/bundled/service-worker.js
      secure: always

    - url: /manifest.json
      static_files: build/bundled/manifest.json
      upload: build/bundled/manifest.json
      secure: always

    - url: /.*
      static_files: build/bundled/index.html
      upload: build/bundled/index.html
      secure: always    
    ```

1.  发布.

        appcfg.py -A {project name} update app.yaml

### 发布到Firebase

以下内容基于[Firebase hosting quick start
guide](https://www.firebase.com/docs/hosting/quickstart.html).

1.  [注册Firebase帐号](https://www.firebase.com/signup/).

1.  安装Firebase命令行工具.

        npm install -g firebase-tools

    `-g`参数让npm把包安装到系统中，以便在任何文件夹都能使用`firebase`命令. 安装时可能需要`sudo`权限.

1.  `cd`进入项目文件夹.

1.  初始化Firebase应用.

        firebase login
        firebase init

    Firebase会询问要使用哪个应用来托管.注册时会自动创建一个随机名称的应用，可以使用。也可以到
    [https://www.firebase.com/account](https://www.firebase.com/account)创建一个来用。

1.  Firebase会询问应用的公共文件夹. 输入`build/bundled`.  因为使用`polymer build`来构建应用时, Polymer CLI将所有Firebase托管所需要的资源都放到了`build/bundled` 文件夹.

1.  编辑firebase配置来添加URL路由.  添加下列内容到`firebase.json`文件, 便得Firebase始终使用`index.html`作为应用的入口来响应任何的URL请求。 

    ```
    "rewrites": [ {
      "source": "**/!{*.*}",
      "destination": "/index.html"
    } ]
    ```

1.  发布.

        firebase deploy

    会输入应用的在线URL.
