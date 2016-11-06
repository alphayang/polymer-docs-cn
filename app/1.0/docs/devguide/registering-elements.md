---
title: 组件注册和生命周期
---

<!-- toc -->

## 注册一个自定义组件 {#register-element}


使用`Polymer`函数来注册一个自定义组件并传递一个新组件的原型做为参数.原型必须有一个`is`属性来指定自定义组件的HTML标记名称.

按照规范自定义组件名**必须包含一个中划线(-)**.

例如: { .caption }

```
    // register an element
    MyElement = Polymer({

      is: 'my-element',

      // See below for lifecycle callbacks
      created: function() {
        this.textContent = 'My element!';
      }

    });

    // create an instance with createElement:
    var el1 = document.createElement('my-element');

    // ... or with the constructor:
    var el2 = new MyElement();
```

`Polymer`函数在浏览器中注册组件并返回可在代码中创建组件新实例的一个构造函数.

`Polymer`函数为自定义组件配置好了原型链并链接到Polymer `Base`原型(提供Polymer的功能),不能使用自己的原型链.然而可以使用[行为](#prototype-mixins).

<!-- legacy anchor -->
<a id="bespoke-constructor"></a>

### 定义一个自定义构造函数 {#custom-constructor}

`Polymer`函数返回一个基本的构造函数可用来创建自定义组件的实例. 需要传一个参数给构造函数来配置新组件时可以指定一个自定义`factoryImpl`函数给原型.

构造函数使用`document.createElement`来返回`Polymer`创建的实例然后使用绑定到组件实例的`this`来调用用户提供的`factoryImpl`函数.

例如: { .caption }

```
    MyElement = Polymer({

      is: 'my-element',

      factoryImpl: function(foo, bar) {
        this.foo = foo;
        this.configureWithBar(bar);
      },

      configureWithBar: function(bar) {
        ...
      }

    });

    var el = new MyElement(42, 'octopus');
```

关于自定义构造函数的两个注意的地方:

*   `factoryImpl`函数_只_在使用构造函数创建实例时才被调用.使用HTML解析器由标记创建组件或使用`document.createElement`创建组件时`factoryImpl`函数不会被调用.

*   `factoryImpl`函数在组件初始化(local DOM已创建,默认值已赋值等等)**之后**. 查看
    [组件初始化和回调准备完成](#ready-method)获取更多信息.

### 扩展原生HTML组件 {#type-extension}

Polymer现在只支持扩展原生HTML组件(例如
`input`或`button`, 相比于将来会支持扩展其它自定义组件).这些原生组件扩展被称为
_自定义类型扩展组件_.

**注意:**
使用原生shadow DOM时扩展原生组件可能引起意外的结果所以不被允许. 使用原生shadown DOM来测试组件以便在开发时发现问题.
关于如何使用原生shadow DOM可查看
[Polymer全局设置](settings).
{.alert .alert-error}

为了扩展原生的HTML组件,需要在组件的原型标记中设置`extends`属性.


例如: { .caption }

```
    MyInput = Polymer({

      is: 'my-input',

      extends: 'input',

      created: function() {
        this.style.border = '1px solid red';
      }

    });

    var el1 = new MyInput();
    console.log(el1 instanceof HTMLInputElement); // true

    var el2 = document.createElement('input', 'my-input');
    console.log(el2 instanceof HTMLInputElement); // true
```

在标记中使用类型扩展,使用_native_标记并添加一个
`is`属性来指定类型扩展名称:

```
    <input is="my-input">
```

<!-- legacy anchor -->
<a id="basic-callbacks"></a>

### 在主HTML文档中定义组件Define an element in the main HTML document {#main-document-definitions}

**注意:**
在主文档中定义组件还处于实验阶段. 实际工作中组件应当定义在不同的文件中并引用到主文档里.
{.alert .alert-error}

通过使用`HTMLImports.whenReady(callback)`来在主HTML文档中定义组. `callback`在所有文档的引用加载完成后被调用.

```
    <!DOCTYPE html>
    <html>
      <head>
        <script src="bower_components/webcomponentsjs/webcomponents-lite.js">
        </script>
        <link rel="import" href="bower_components/polymer/polymer.html">
        <title>Defining a Polymer Element from the Main Document</title>
      </head>
      <body>
        <dom-module id="main-document-element">
          <template>
            <p>
              Hi! I'm a Polymer element that was defined in the
              main document!
            </p>
          </template>
          <script>
            HTMLImports.whenReady(function () {
              Polymer({
                is: 'main-document-element'
              });
            });
          </script>
        </dom-module>
        <main-document-element></main-document-element>
      </body>
    </html>
```

## 生命周期回调 {#lifecycle-callbacks}

Polymer的原型实现了标准的自定义组件生命周期回调来完成Polymer内置功能的必要任务.Polymer在原型中调用短命名的生命周期方法.

Polymer会添加一个额外的`ready`回调用来在Polymer完成创建和初始化组件的local DOM后调用.

<table>
  <tr>
    <th>Callback</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>created</code></td>
    <td>Called when the element has been created, but before property values are
       set and local DOM is initialized.
      <p>Use for one-time set-up before property values are set.
      </p>
      <p>Use instead of <code>createdCallback</code>.
      </p>
    </td>
  </tr>
  <tr>
    <td><code>ready</code></td>
    <td>Called after property values are set and local DOM is initialized.
      <p>Use for one-time configuration of your component after local
        DOM is initialized. (For configuration based on property values, it
        may be preferable to use an <a href="properties#multi-property-observers">observer</a>.)
      </p>
    </td>
  </tr>
  <tr>
    <td><code>attached</code></td>
    <td>Called after the element is attached to the document. Can be called multiple
        times during the lifetime of an element. The first `attached`  callback
        is guaranteed not to fire until after `ready`.
      <p>Uses include accessing computed style information, and adding
        document-level event listeners. (If you use declarative
        event handling, such as <a href="events.html#annotated-listeners">annotated
        event listeners</a> or the
        <a href="events#event-listeners"><code>listeners</code> object</a>,
        Polymer automatically adds listeners on attach and removes
        them on detach.)
      </p>
      <p>Use instead of <code>attachedCallback</code>.
      </p>
    </td>
  </tr>
  <tr>
    <td><code>detached</code></td>
    <td>Called after the element is detached from the document. Can be called
        multiple times during the lifetime of an element.
      <p>Uses include removing event listeners added in <code>attached</code>.
      </p>
      <p>Use instead of <code>detachedCallback</code>.
      </p>
    </td>
  </tr>
  <tr>
    <td><code>attributeChanged</code></td>
    <td>Called when one of the element's attributes is changed.
      <p>Use to handle attribute changes that <em>don't</em> correspond to
        declared properties. (For declared properties, Polymer
        handles attribute changes automatically as described in
        <a href="properties#attribute-deserialization">attribute deserialization</a>.)
      </p>
      <p>Use instead of <code>attributeChangedCallback</code>.
      </p>
    </td>
  </tr>
</table>


例如: { .caption }

```
    MyElement = Polymer({

      is: 'my-element',

      created: function() {
        console.log(this.localName + '#' + this.id + ' was created');
      },

      ready: function() {
        console.log(this.localName + '#' + this.id + ' has local DOM initialized');
      },

      attached: function() {
        console.log(this.localName + '#' + this.id + ' was attached');
      },

      detached: function() {
        console.log(this.localName + '#' + this.id + ' was detached');
      },

      attributeChanged: function(name, type) {
        console.log(this.localName + '#' + this.id + ' attribute ' + name +
          ' was changed to ' + this.getAttribute(name));
      }

    });
```

<!-- ToDo: the following section should probably be moved to the local DOM chapter. -->

### local DOM初始化完成后的回调 {#ready-method}

`ready`回调在在Polymer组件的local DOM完成初始化后被调用.

**什么是local DOM?**
Local DOM组件创建的子DOM树并由组件管理.它与组件的为区分起见被称为_light DOM_的子级相分离. 查看信息查看
[Local DOM](local-dom).
{.alert .alert-error}

组件的_可用_ 是指:

*   属性值已配置,通过父级的数据绑定、标记属性值的反序列化或赋与了默认值.

*   local DOM模板已被初始化.

*   所有在**组件local DOM内部的**组件已经可用并完成了它们的`ready`方法调用.

需要在组件完成构造后操作它的local DOM就可以实现`ready`回调.

```
    ready: function() {
      // access a local DOM element by ID using this.$
      this.$.header.textContent = 'Hello!';
    }
```

**注意:**
此例中使用[自动节点查找](local-dom#node-finding)来访问一个local DOM组件.
{.alert .alert-info}

在一个DOM树中`ready`会按照_文档顺序_被调用,但是兄弟组件或宿主组件与它的**light DOM**子级之间的初始化回调顺序是不确定的.

### 初始化顺序和时机 {#initialization-order}

组件的初始化顺序是:

-   `created`回调.
-   Local DOM被初始化(**local DOM**的子级已被创建,根据模板已对它们进行了赋值以及
    如果注册了`ready`回调也已经完成调用).
-   `ready`回调.
-   [`factoryImpl`回调](#custom-constructor).
-   `attached`回调.

Local DOM子级只有`ready`回调如果它们注册了自定义组件. 如果loal DOM在之后注册一个子元素那么这些子元素的`created`和`ready`函数会在子元素更新时被调用,不会导致宿主元素剩余回调的延迟. 在资源使用前引用它们可以确保组件按顺序被创建.

注意尽管组件的生命周期回调会按照上列的顺序被享受生活但由于很多因素导致不同组件的**初始化时机各不相同**,包括浏览器是否原生支持web components等等.

#### light DOM子级的初始化时机

light DOM子级的初始化时机没有保证.正常情况下组件按照文档顺序被初始化所以子级通常在父级初始化之后.

例如`avatar-list`的light DOM:

```
    <avatar-list>
      <my-photo class="photo" src="one.jpg">First photo</my-photo>
      <my-photo class="photo" src="two.jpg">Second photo</my-photo>
    </avatar-list>
```

`<avatar-list>`的`ready`函数会在它各个
`<my-photo>`组件之_前_被调用.

另外父组件创建后它的light DOM可在任意时间添加. 一个好的组件应在运行期提供它的light DOM操作.

可以使用如下策略来避免超时:

*   被动处理light DOM子级. 例如一个弹出菜单组件对它的light DOM子元素进行计数. 只在菜单打开时进行计数就可以最小代价来添加和删除菜单项.

*   使用[`observeNodes`函数](local-dom#observe-nodes)来处理子元素的添加和删除.

#### Local DOM子级的初始化时机

在local DOM初始化中,local DOM子元素被创建,属性被赋与了模板中的值, 子元素的`ready`大父元素的`ready`回调之前被调用.

需要注意以下两个方面:

 *  `dom-repeat`和`dom-if`模板在它们的值更新后会**异步**创建DOM树.例如在组件的local DOM中有一个
    `dom-repeat`,`ready`回调在`dom-repeat`完成实例创建前被调用.

    如果需要捕获`dom-repeat`或`dom-if`创建或删除模板实例的动作只需要为`dom-change`添加一个事件监控器.
    查看[`dom-change`事件](templates#dom-change)获取更多信息.

*   Polymer保证local DOM子元素的`ready`回调在它们的父元素之前被调用; 然而不能为`attached`的回调做同样的保证. 这是原生行为和polyfill的根本区别.

#### 兄弟节点的初始化时机

对于兄弟节点的初始化时机没有顺序的保证.

也可以说兄弟节点可能以任何顺序回调各自的`ready`.

当组件初始化时可以在其内部`async`调用`attached`回调来访问其兄弟节点:

```
    attached: function() {
      this.async(function() {
        // access sibling or parent elements here
      });
    }
```

### 注册回调 {#registration-callback}

Polymer提供了两个注册期回调, `beforeRegister`和`registered`.

使用`beforeRegister`回调在注册前转化组件原型. 当然使用ES6类来注册组件时很有用,
如同[Building web components using ES6 classes](/1.0/blog/es6)中所提到的.

可以实现`registered`回调在组件注册后进行一次初始化操作. 主要用来实现
[行为](behaviors).

## 宿主上的表态属性 {#host-attributes}

如果自定义组件需要在创建期使用HTML属性进行赋值,可以通过在原型上定义一个`hostAttributes`键值对属性来完成.由于HTML属性只能是字符串所以属性值通常也是字符串;然后标准的`serialize`方法会把值转化为字符串所以`true`会被序列化为一个空值,`false`则不被赋值等等,
(查看[属性序列化](properties#attribute-serialization)查看更多信息).

例如:  { .caption }

```
    <script>

      Polymer({

        is: 'x-custom',

        hostAttributes: {
          'string-attribute': 'Value',
          'boolean-attribute': true,
          tabindex: 0
        }

      });

    </script>
```

结果是:

```
    <x-custom string-attribute="Value" boolean-attribute tabindex="0"></x-custom>
```

**注意:**
`class`属性不能使用`hostAttributes`来配置.
{.alert .alert-error}

## 行为 {#prototype-mixins}

组件间可以利用_行为_的行为来分享代码,可以用来定义属性、生命周期回调、事件监听器和其它功能.

查看[行为](behaviors)获取更多信息.

## 类样式构造函数 {#element-constructor}

想要配置自定义组件的原型链而**不**立即注册可以使用I`Polymer.Class`函数. `Polymer.Class`接受与`Polymer`函数相同的原型参数来配置原型链但_不_注册组件. 
它返回一个构造函数可用于传递给`document.registerElement`用来在浏览器中注册组件,稍后可以在代码中来初始化组件实例.

想要在组件中一次性定义和注册就可以使用[`Polymer` function](#register-element).

例如: { .caption }

```
    var MyElement = Polymer.Class({

      is: 'my-element',

      // See below for lifecycle callbacks
      created: function() {
        this.textContent = 'My element!';
      }

    });

    document.registerElement('my-element', MyElement);

    // Equivalent:
    var el1 = new MyElement();
    var el2 = document.createElement('my-element');
```

`Polymer.Class`旨在提供和ES6能提供给`document.registerElement`的类似.
