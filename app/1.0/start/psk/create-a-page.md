---
title: 新建一个页面
subtitle: "创建第一个Polymer应用"
---

<!-- toc -->

Polymer Starter Kit教程包括 :

*   导航菜单中创建一个菜单项.
*   创建一个新内容页面Create content for a new page.
*   路由访问新页面.

这个教程需要你完成[配置](set-up).

## 本地运行应用

1.  `cd` 到项目根目录.

1.  启动本地开发服务器.

        gulp serve

自动启动默认浏览器并打开应用. 服务器会自动检测应用文件的更改并自动生成和更新，不需要手动生成和刷新浏览器.

## 创建一个导航目录项

1.  编辑`app/index.html`.

1.  查找到导航目录.

```
...
<!-- Drawer Content -->
<paper-menu attr-for-selected="data-route" selected="[[route]]">
  <a data-route="home" href="{{baseUrl}}">
    <iron-icon icon="home"></iron-icon>
    <span>Home</span>
  </a>
...
```

第一个导航目录荐包含一个anchor组件 (`<a>`) 和2个子组件: `<iron-icon>` 和 `<span>`.

*   `<iron-icon>` 显示图标.
*   `<span>` 显示图标后的文字.

1.  添加新的导航目录项到菜单的底部.

```
<a data-route="books" href="{{baseUrl}}books">
  <iron-icon icon="book"></iron-icon>
  <span>Books</span>
</a>
```

新的菜单部分:

```
...
<!-- Drawer Content -->
<paper-menu attr-for-selected="data-route" selected="[[route]]">
  <a data-route="home" href="{{baseUrl}}">
    <iron-icon icon="home"></iron-icon>
    <span>Home</span>
  </a>
  ...
  <a data-route="contact" href="{{baseUrl}}contact">
    <iron-icon icon="mail"></iron-icon>
    <span>Contact</span>
  </a>
  <a data-route="books" href="{{baseUrl}}books">
    <iron-icon icon="book"></iron-icon>
    <span>Books</span>
  </a>
</paper-menu>
...
```

查看应用, 可以在导航目录中看到新内容, 但链接还没有指定到内容.

## 添加内容

上一步添加一个导航目录项来让用户导航到新页面. 现在,为新页面添加内容.

1.  编辑`app/index.html`并找到main content.

```
<div class="content">
  <iron-pages attr-for-selected="data-route" selected="{{route}}">

    <section data-route="home">
      <paper-material elevation="1">
        <my-greeting></my-greeting>

        <p class="subhead">You now have:</p>
        <my-list></my-list>
        ...
      </paper-material>
    </section>

    <section data-route="users">
      <paper-material elevation="1">
        <h2 class="page-title">Users</h2>
        <p>This is the users section</p>
        <a href$="{{baseUrl}}users/Addy">Addy</a><br>
        <a href$="{{baseUrl}}users/Rob">Rob</a><br>
        <a href$="{{baseUrl}}users/Chuck">Chuck</a><br>
        <a href$="{{baseUrl}}users/Sam">Sam</a>
      </paper-material>
    </section>
    ...
```

*   PSK的设计模式是让`<section>`组件表示一个页面. `<iron-pages>`组件来控制哪个页面会显示给用户.
*   `data-route`属性来用定义路由系统.下面会为新页面设置路由.
*   `<paper-material>`组件创建一个卡片悬浮在主内容区域上方. 如果遵照Material Design规范
    , 所有的主内容应该显示在这个浮层上方.
*   `elevation`属性决定了`<paper-material>`组件在主内容上的高度. 一般设置在`0` 和 `5`之间.

1.  添加以下内容到main section区域底部.

```
<section data-route="books">
  <paper-material elevation="1">
    <p>Hello, World!</p>
  </paper-material>
</section>
```

添加后的代码:

```
...
<!-- Main Content -->
<div class="content">
  <iron-pages attr-for-selected="data-route" selected="{{route}}">
    ...
    <section data-route="contact">
      <paper-material elevation="1">
        <h2 class="page-title">Contact</h2>
        <p>This is the contact section</p>
      </paper-material>
    </section>

    <section data-route="books">
      <paper-material elevation="1">
        <p>Hello, World!</p>
      </paper-material>
    </section>

  </iron-pages>
</div>
...
```

现在已经有了新页面的内容. 下一步将导航目录连接到新内容.

## 路由访问到新内容

在这一步骤里, 需要修改路由系统让用户在点击新"Books"导航时, 显示新页面.

1.  编辑 `app/elements/routing.html`并添加下列代码到底部，也就是`/contact`的下方.

```
page('/books', function () {
  app.route = 'books';
});
```

更新后的代码:

```
...

page('/', function () {
  app.route = 'home';
});

...

page('/contact', function () {
  app.route = 'contact';
});

page('/books', function () {
  app.route = 'books';
});

...
```

新的页面已经完成! 在浏览器中访问
[http://localhost:5000/#!/books](http://localhost:5000/#!/books).

![Example of new page](/images/1.0/psk/psk-tutorial-books-page.png)
