---
title: 测试组件
---

<!-- toc -->

这一节展示了使用Polymer CLI进行单元测试的基本用法以及如何利用Web Component Tester(Polymer CLI的测试工具也是基于它)完成测试更多任务和场景.

## 概览

Polymer CLI是一个覆盖了包含单元测试等大多数Polymer开发任务的全功能命令行工具.Polymer CLI的单元测试工具底层使用了Web Component Tester来完成.

Web Component Tester是由Polymer团队创建的完整的测试环境.不仅可以用来做本地组件测试,也可以通过Sauce Labs进行远程测试.它基于一些流行的第三方工具,如:

*   [Mocha](https://mochajs.org/)完整支持BDD和TDD的测试框架.
*   [Chai](http://chaijs.com/)在Mocha测试中使用更多各类的assertion.
*   [Sinon](http://sinonjs.org/)用于spies, stubs和mocks.
*   [Selenium](http://www.seleniumhq.org/)用于浏览器中运行单元测试.
*   [Accessibility Developer
    Tools](https://github.com/GoogleChrome/accessibility-developer-tools)
    用于辅助功能的审计.

## 快速入门 {#quick-start}

基于演示目的,该节展示了如何安装Polymer CLI并初始化一个组件项目. 然后使用这个项目来学习如何添加和运行单元测试Y.

1.  (仅限OS X) [手动安装][selenium]最新的SafariDriver
    extension for Selenium [(`SafariDriver.safariextz`)][safaridriver]来确保Web Component Tester正常工作.查看
    [Web Component Tester Polycast][workaround-example]中的一个演示
    . 注意: Polycas可能有些过时了,但URL链接依然有效.

1.  安装Polymer CLI. 参考
    [安装Polymer CLI](polymer-cli#install).

1.  [创建一个组件项目](polymer-cli#element). 假设组件项目根目录名称和组件名称都为`my-el`.

1.  `cd`到项目根目录.

1.  运行`ls`.

    可以看到组件项目包含一个`test`目录. 这里存放了所有的单元测试.

    运行`polymer test`后, Web Component Tester自动描述`test`目录并执行找到的所有测试.如果使用其它目录名称,需要在运行
    `polymer test`时指定目录名.
    {.alert .alert-info}

1.  打开`test/my-el_test.html`可以看到一个基本单元测试的示例.

1.  运行测试.

        polymer test

    Web Component Tester自动查找系统里已安装的所有浏览器并逐一运行全部测试.如果想要针对单个浏览器比如Google Chrome运行测试则需要运行
    `polymer test -l chrome`.
    {.alert .alert-info}

### 与测试进行交互

还可以在浏览器中运行测试.这样就可以使用浏览器的DevTools来查看或调试单元测试.

例如, 使用Polymer CLI和刚在[快速入门](#quick-start)创建的示例组件项目, 启动服务器:

    polymer serve

然后在浏览器中运行基本的`my-el_test.html`单元测试,打开浏览器输入以下URL:

    localhost:8080/components/my-el/test/my-el_test.html

## 创建测试

已经了解了使用Polymer CLI来运行测试的基础,可以开始创建测试了.

这一节中展示了如何通过创建单元测试来覆盖不同的任务或场景.

### 异步测试 {#async}

为了创建异步测试需要传递一个参数`done`到测试方法中,测试完成后调用`done()`. `done`参数在Mocha中表示测试是异步的. 当Mocha运行测试时将一直等待测试代码调用
`done()`. 如果`done()`没有被调用
,测试会超时并在Mocha报告中显示测试失败.

```js
test('fires lasers', function(done) {
  myEl.addEventListener('seed-element-lasers', function(event) {
    assert.equal(event.detail.sound, 'Pew pew!');
    done();
  });
  myEl.fireLasers();
});
```

### 避免测试模板中的共享状态 {#test-fixtures}

测试模板可以定义一个模板以便在每一个测试套件中使用一个基于模板的全新的实例.测试模板可以最小化测试套件之间的共享状态.

使用测试模板:

*   定义测试模板并添加一个ID.
*   测试脚本中定义模板的引用变量.
*   在`setup()`方法中实例化一模板.

```html
<test-fixture id="seed-element-fixture">
  <template>
    <seed-element>
      <h2>seed-element</h2>
    </seed-element>
  </template>
</test-fixture>

<script>
  suite('<seed-element>', function() {
    var myEl;
    setup(function() {
      myEl = fixture('seed-element-fixture');
    });
    test('defines the "author" property', function() {
      assert.equal(myEl.author.name, 'Dimitri Glazkov');
    });
  });
</script>
```

### 创建存根方法Create stub methods

存根使您能够使用自定义方法替换默认实现。 这个对于捕获很有用。

```
setup(function() {
  stub('paper-button', {
    click: function() {
      console.log('paper-button.click called');
    }
  });
});
```

不需要在单独的组件直接使用存根. 可以为一组同类型的组件重新实现.

### 创建组件存根

使用[组件存根](http://stackoverflow.com/questions/463278/what-is-a-stub)
来隔离组件测试. 例如一个测试需要另一个组件返回方法,可以实现这个组件存根来总是返回固定数据而不用将这个组件(可能不稳定)引入到测试中.

使用`replace()`来创建组件存根.

```js
setup(function() {
  replace('paper-button').with('fake-paper-button');
});
```

例如使用上面的`replace()`和下边的组件:

```
<dom-module id='x-el'>
  <template>
    <paper-button id="pb">button</paper-button>
  </template>
</dom-module>
```

测试时模板内容会被替换如下:

```
<dom-module id='x-el'>
  <template>
    <fake-paper-button id="pb">button</fake-paper-button>
  </template>
</dom-module>
```

组件属性和内容被保留但是标记被替换为特定的标记存根.

由于在`setup()`调用该方法, 所有的改变在测试结束时被还原.

### AJAX

<google-youtube video-id="_9qARcdCAn4" autoplay="0"
                rel="0" fluid></google-youtube>

Web Component Tester包含了[Sinon](http://sinonjs.org/)可以用来模仿XHR请求以及创建虚假服务器.

下面是一个针对[`<iron-ajax>`](https://elements.polymer-project.org/elements/iron-ajax)的简单XHR单元测试示例.
查看Sinon的文档获取更多的示例.

```
<!-- create test fixture template -->
<test-fixture id="simple-get">
  <template>
    <iron-ajax url="/responds_to_get_with_json"></iron-ajax>
  </template>
</test-fixture>
<script>
  suite('<iron-ajax>', function() {
    var ajax;
    var request;
    var server;
    var responseHeaders = {
      json: { 'Content-Type': 'application/json' }
    };
    setup(function() {
      server = sinon.fakeServer.create();
      server.respondWith(
        'GET',
        /\/responds_to_get_with_json.*/, [
          200,
          responseHeaders.json,
          '{"success":true}'
        ]
      );
    });
    teardown(function() {
      server.restore();
    });
    suite('when making simple GET requests for JSON', function() {
      setup(function() {
        // get fresh instance of iron-ajax before every test
        ajax = fixture('simple-get');
      });
      test('has sane defaults that love you', function() {
        request = ajax.generateRequest();
        server.respond();
        expect(request.response).to.be.ok;
        expect(request.response).to.be.an('object');
        expect(request.response.success).to.be.equal(true);
      });
      test('has the correct xhr method', function() {
        request = ajax.generateRequest();
        expect(request.xhr.method).to.be.equal('GET');
      });
    });
  });
</script>
```

**注意:** 上面的示例使用了Chai的[`expect`](http://chaijs.com/api/bdd/)
断言风格.
{ .alert .alert-info }

### 运行一组测试 {#test-sets}

为了运行一组测试需要创建一个文件名`loadSuites()`的HTML文件. 使用Web Component Tester时把这个HTML文件做为第一个参数传入, 
(例如`wct test/my-test-set.html`.

```
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <script src=”../bower_components/webcomponentsjs/webcomponents-lite.js”></script>
    <script src=”../bower_components/web-component-tester/browser.js”></script>
  </head>
  <body>
    <script>
      WCT.loadSuites([
        'basic.html',
        'async.html'
      ]);
    </script>
  </body>
</html>
```

`loadSuites()`参数应该是一个字符串数组.每个字符串是一个代表了测试套件的相对URL. 还可以在URL使用查询字符串来配置测试. 查看 [测试 shadow DOM](#shadow-dom)
的示例.

### 测试local DOM

使用Polymer的[DOM API](/1.0/docs/devguide/local-dom#dom-api)来访问来修改local DOM的内容.

```js
test('click sets isWaiting to true', function() {
  myEl.$$('button').click();
  assert(myEl.isWaiting, true);
});
```

**注意:** `myEl.$$('button')`返回`myEl`的local DOM中第一个`button`组件.
{ .alert .alert-info }

#### 测试DOM更改

如果组件模板包含一个[模板重复器
 (`dom-repeat`)](/1.0/docs/devguide/templates#dom-repeat)或[条件模板 (`dom-if`)](/1.0/docs/devguide/templates#dom-if)或者测试涉及到一个local DOM更改，都需要把测试包装到`批处理`中.Polymer为了性能考虑会被执行这些操作. `批处理`确保这些异步更改已经生效.测试方法中接收一个 `done`参数来表示测试是[异步的](#async)并且在`批处理`的最后要调用`done()`.

```
suite('my-list tests', function() {
  var list, listItems;
  setup(function() {
    list = fixture('basic');
  });
  test('Item lengths should be equal', function(done) {
    list.items = [
      'Responsive Web App boilerplate',
      'Unit testing with Web Component Tester',
      'Offline support with the Platinum Service Worker Elements'
    ];
    // Data bindings will stamp out new DOM asynchronously
    // so wait to check for updates
    flush(function() {
      listItems = Polymer.dom(list.root).querySelectorAll('li');
      assert.equal(list.items.length, listItems.length);
      done();
    });
  });
)};
```

#### 测试本地shadow DOM {#shadow-dom}

为了查看测试套件在浏览器以native shadow DOM运行时的行为,创建一个[测试集合](#test-sets)并以查询字符串方式传递`dom=shadow` 给Web Component Tester.

```js
WCT.loadSuites([
  'basic-test.html',
  'basic-test.html?dom=shadow'
]);
```

示例运行`basic-test.html`两次分别使用shady DOM和native shadow DOM(浏览器支持时).

### 云端自动测试

在项目开发时越早的引入测试越好.使用诸如Travis做为持续集成时可以配合Sauce Labs的跨浏览器测试来确保你的代码在不同的平台和设备上都能正常工作.可以查看下面的Polycast来获取配置这些工作的指南.

<google-youtube video-id="afy_EEq_4Go" autoplay="0"
                rel="0" fluid></google-youtube>

## 更多

Polymer Summit 2015测试视频:

<google-youtube video-id="kX2INPJY4Y4" autoplay="0"
                rel="0" fluid></google-youtube>

[Web Component Tester README][wct-readme]有更深入的用法介绍.

[seed-element]: https://github.com/PolymerElements/seed-element
[wct-readme]: https://github.com/Polymer/web-component-tester/blob/master/README.md
[selenium]: https://github.com/SeleniumHQ/selenium/wiki/SafariDriver#getting-started
[safaridriver]: http://selenium-release.storage.googleapis.com/2.48/SafariDriver.safariextz
[workaround-example]: https://youtu.be/YBNBr9ECXLo?t=74
[wct-polycast]: https://youtu.be/YBNBr9ECXLo
[ajax-polycast]: https://www.youtube.com/watch?v=_9qARcdCAn4
