---
title: 发布
subtitle: "创建第一个Polymer应用"
---

<!-- This page does not have a ToC because it currently only has one H2.
     Add a ToC if you add another H2. -->

学习如何发布Polymer Starter Kit应用.

学习发布前需要完成[配置](set-up).

## 发布到Firebase (推荐)

Firebase是一种简单安全发布PSK应用的服务. 可以在5分钟内注册免费帐户并发布你的应用.

内容基于[Firebase hosting quick start
guide](https://www.firebase.com/docs/hosting/quickstart.html).

1.  [注册一个Firebase帐户](https://www.firebase.com/signup/).

1.  安装Firebase命令行工具.

        npm install -g firebase-tools

    `-g`参数让`npm`将包包装到系统中以便在任何目录中使用. 可能需要`sudo`权限来完成安装.

1.  `cd` 到项目目录.

1.  初始化Firebase应用.

        firebase login
        firebase init

    Firebase会询问要使用哪个应用来托管.注册时会自动创建一个随机名称的应用，可以使用。也可以到
    [https://www.firebase.com/account](https://www.firebase.com/account)创建一个来用。

1.  Firebase会询问应用的公共文件夹. 输入`dist`.  因为使用`gulp`来构建应用时, PSK将所有Firebase托管所需要的资源都放到了`dist`文件夹.

1.  发布.

        firebase deploy

    输出内容中有线上的URL.
