---
title: 观察器和计算属性
---

<!-- toc -->


当组件的数据发生[可观察的更改](#observable-changes)时调用观察器函数. 有两种基本以观察器类型:


*   只观察一个属性的单一观察器.
*   可观察一个或多个属性*或路径*的复杂观察器.

尽管可以使用不同的语法来声明这两种观察器类型但大多数情况下它们的功能是一样的.

计算属性是基于一个或多个组件数据的虚拟属性.计算属性是由复杂的观察器经过特殊计算而返回的一个值.

除非特殊说明,单一观察器、复杂观察器和计算属性都是观察器.

### 观察器是同步的

和所有属性的效果一样,观察器是同步的.如果观察器被频繁调用就要考虑延迟诸如插入或删除DOM这类耗时的工作.经如可以使用[`异步`](/1.0/docs/api/Polymer.Base#method-async)函数与延迟工作或使用
[`去抖`](/1.0/docs/api/Polymer.Base#method-debounce)函数来确保它只在给定时间段内运行一次.

然后如果异步处理数据更改就要注意当处理时数据有可能不是最新的.


## 单一观察器 {#simple-observers}

单一观察器在`properties`对象中声明并且只能观察一个属性.对于属性不能假定有什么特定的初始化顺序: 当观察器信赖于多个属性的初始化时就要使用一个复杂观察器.

单一观察器在属性变为defined(!=`undefined`)时被第一次触发,之后的任何改变都会触发*_就算是属性变为undefined._*

单一观察器当且仅当*自身*属性更改时被触发.对于子属性的更改或数组的操作都无效. 需要一个复杂观察器使用一个通配路径来观察子属性或数组变换,
参看[观察路径上的所有更改](#deep-observation).

观察器函数接受属性的新值和旧值作为参数.

### 观察一个属性  {#change-callbacks}

通过添加一个`observer`键到属性声明来定义一个单一观察器. 
例如: { .caption }

```
Polymer({

  is: 'x-custom',

  properties: {
    disabled: {
      type: Boolean,
      observer: '_disabledChanged'
    },
    highlight: {
      observer: '_highlightChanged'
    }
  },

  _disabledChanged: function(newValue, oldValue) {
    this.toggleClass('disabled', newValue);
    this.highlight = true;
  },

  _highlightChanged: function() {
    this.classList.add('highlight');
    this.async(function() {
      this.classList.remove('highlight');
    }, 300);
  }

});
```

**警告:**
单一属性观察器不能依赖任何属性、子属性或路径,原因是这些依赖变为undefined时也会调用观察器. 查看[总是是在观察器参加中包含依赖](#dependencies)获取更多信息.
{ .alert .alert-warning }

## 复杂观察器 {#complex-observers}

复杂观察器以`观察器`数组的形式声明来用于监视一个或多个路径. 这些路径被称为观察器的*依赖*.

```
observers: [
  'userListChanged(users.*, filter)'
]
```

每个依赖表示:

*   一个特定的属性(如`firstName`).

*   一个特定的子属性(如`address.street`).

*   特定的数组变换 (如`users.splices`).

*   给定路径上的所有子属性更改和数组变换(如`users.*`).

观察器被调用时每个依赖都传递一个参数.由于所观察的路径不同所以其参数类型也不尽相同.


*   对单一属性或子属性依赖,参数为属性或子属性的新值.

*   对于数组变换或通配路径,参数是一个*更改记录*来描述更改.

根据**所观察属性的数量**来处理undefined值:


*   复杂观察口拙初始化调用将延迟到*所有*它的依赖都*defined*时(也就是说它们的值都不能是`undefined`).

*   **对于单一观察器**的调用规则是明确的:*每一次*依赖发生可观察的更改时都会调用观察器, **就算路径的新值是<code>undefined</code>.**


*   <strong>多属性观察器</strong>在<em>每一次</em>它的依赖产生了可观察的更改后都会被调用, <strong><em>除非</em>其中一个路径的新值是
    <code>undefined</code>.</strong>

复杂观察器只应依赖其所声明的依赖项.

相关任务R:

*   观察多个属性或路径
*   观察数组变换
*   观察路径上的所有更改




### 观察多个属性的更改 {#multi-property-observers}

为了观察一组属性可以使用`observers`数组.

这些观察器与单个属性观察的不同点在于:

*   观察器直到所有的依赖属性都为defined后才被调用(`!== undefined`).
    所每个依赖属性都应有一个默认`值`定义在`properties`(或被初始化为一个非`undefined`的值)来确保观察器被调用.
*   观察不接收`旧`值为参数,只有新值.只有定义在`properties`对象的单一观察器接收`新`和`旧`两个值.

例如 { .caption }

```
Polymer({

  is: 'x-custom',

  properties: {
    preload: Boolean,
    src: String,
    size: String
  },

  observers: [
    'updateImage(preload, src, size)'
  ],

  updateImage: function(preload, src, size) {
    // ... do work using dependent values
  }

});
```

除了属性,观察器还可以观察[子属性的路径](#observing-path-changes),
[通配路径](#deep-observation), or [数组变换](#array-observation).

### 观察子属性更改 {#observing-path-changes}

为了观察对象子属性的更改:

*   定义一个`观察器`数组.
*   为`观察器`数组添加一个元素.元素为一个接收以逗号分隔的一个或多个路径为参数的方法名.例如,
    `onNameChange(dog.name)`为一个路径,或
    `onNameChange(dog.name, cat.name)`为多个路径.每个路径是一个需要观察的子属性路径.
*   在组件原型中定义方法. 方法调用时接受子属性新值为参数.

为了让Polymer能正确的探测的子属性的更改, 子属性必须用以下两种方式中的一种来更新:

*   使用[数据绑定](data-binding#property-binding).
*   调用[`set`](model-data#set-path).

例如: { .caption }

```
<dom-module id="x-sub-property-observer">
  <template>
    <!-- Sub-property is updated via property binding. -->
    <input value="{{user.name::input}}">
  </template>
  <script>
    Polymer({
      is: 'x-sub-property-observer',
      properties: {
        user: {
          type: Object,
          value: function() {
            return {};
          }
        }
      },
      // Each item of observers array is a method name followed by
      // a comma-separated list of one or more paths.
      observers: [
        'userNameChanged(user.name)'
      ],
      // Each method referenced in observers must be defined in
      // element prototype. The argument to the method is the new value
      // of the sub-property.
      userNameChanged: function(name) {
        console.log('new name: ' + name);
      },
    });
  </script>
</dom-module>
```

### 观察数组变换 {#array-observation}

当利用Polymer的[数组变换方法](model-data#array-mutation)添加或删除数组元素时可以使用数组变换观察器来调用一个观察函数.
当数组完成操作后,观察器接收到一个由数组拼接的更改记录来表示经历的操作.

很多情况下希望观察数组变换和数组元素子属性的改变, 就可以使用通配路径[观察路径上的所有更改](#deep-observation).


**避免原生JavaScript数组变换方法.**
尽可能使用Polymer的[数组变换方法](model-data#array-mutation)来确保数组变换中已注册的组件被通知.不可避免原生方法时就需要[使用原生数组变换方法](model-data#notifysplices)来向Polymer通知数组的更改.
{ .alert .alert-warning }

创建一个拼接观察器需要指定一个数组路径并在`observers`数组上使用`.splices`.

``` js
observers: [
  'usersAddedOrRemoved(users.splices)'
]
```

观察器方法应接受单个参数.调用时接受一个数组变换的更改记录.每个更改记录包含以下属性:

*   `indexSplices`.数组索引发生更改的集合.每个`indexSplices`记录包含以下属性:

     -   `index`.拼接开始的索引位置.
     -   `removed`. `已删除`的元素数组.
     -   `addedCount`. 在`index`处插入的元素个数.
     -   `object`: 操作数组的引用.
     -   `type`:'splice'字符串.


**更改记录可能是undefined.**更改记录在观察器被第一次调用有可能是undefined,所以在代码中必须处理这种情况,如下所示.
{ .alert .alert-info }

示例 {.caption}

```
Polymer({

  is: 'x-custom',

  properties: {
    users: {
      type: Array,
      value: function() {
        return [];
      }
    }
  },

  observers: [
    'usersAddedOrRemoved(users.splices)'
  ],

  usersAddedOrRemoved: function(changeRecord) {
    if (changeRecord) {
      changeRecord.indexSplices.forEach(function(s) {
        s.removed.forEach(function(user) {
          console.log(user.name + ' was removed');
        });
        for (var i=0; i<s.addedCount; i++) {
          var index = s.index + i;
          var newUser = s.object[index];
          console.log('User ' + newUser.name + ' added at index ' + index);
        }
      }, this);
    }
  },
  ready: function() {
    this.push('users', {name: "Jack Aubrey"});
  },
});
```

#### 跟踪键拼接 {#key-splices}

某些情况下需要知道[不可变的不透明键](#key-paths)也就是Polymer用它来跟踪数组元素. **这是一种高级用法,只在实现一个诸如[模板重复器](https://www.polymer-project.org/1.0/docs/devguide/templates.html#dom-repeat)的组件时需要用到.**

可以获取数组的`Collection`对象用来注册对键的添加和删除:

<code>Polymer.Collection.get(<var>array</var>);</code>

注册之后更改记录就包含了一个额外的属性:

*   `keySplices`.数组中的键发生更改的一组记录.每个`keySplices`记录包含以下属性:

    -   `added`. 添加的键数组.
    -   `removed`. 删除的键数组.

**模板重复器和键拼接.** 模板重复器(`dom-repeat`)
组件内部使用键,所以如果`dom-repeat`使用了一个数组,那么数组的观察器会收到 `keySplices`属性.
{.alert .alert-info}

### 观察路径上的所有更改 {#deep-observation}

在路径上使用通配符(`*`)使得对象的任意(不论深度多少)子属性或数组的更改都会调用观察器T.

当使用通配符来指定路径时传递给观察器的参数是包含以下属性的一个更改记录对象:

*   `path`. 属性发生更改的路径. 用此来确定更改的属性、子属性或数组.
*   `value`. 更改路径的新值.
*   `base`. 与路径的非通配符部分匹配的对象.

对于数组变换, `path`改变的数组路径并跟在`splices`之后. 更改记录包含了`indexSplices`和
`keySplices`属性,属性描述在 
[观察数组变换](#array-observation).

示例: { .caption }

```
<dom-module id="x-deep-observer">
  <template>
    <input value="{{user.name.first::input}}"
           placeholder="First Name">
    <input value="{{user.name.last::input}}"
           placeholder="Last Name">
  </template>
  <script>
    Polymer({
      is: 'x-deep-observer',
      properties: {
        user: {
          type: Object,
          value: function() {
            return {'name':{}};
          }
        }
      },
      observers: [
        'userNameChanged(user.name.*)'
      ],
      userNameChanged: function(changeRecord) {
        console.log('path: ' + changeRecord.path);
        console.log('value: ' + changeRecord.value);
      },
    });
  </script>
</dom-module>
```

#### 数组元素的深度子属性更改 {#key-paths}

当数组的子属性更改时, `changeRecord.path`是更改的数组元素的"key"的索引, 而不是数组索引. 例如:

```
console.log(changeRecord.path); // users.#0.name
```

`#0`表示示例数组元素的键.所有键加了前缀(`#`)用来和数组索引进行区分.
键提供了数组元素的固定索引,不论数组进行了何种操作(添加或删除).

使用`get`方法来获取路径上的元素.

```
var item = this.get('users.#0');
```

需要数组元素的索引时使用`indexOf`来获取:

```
var item = this.get('users.#0');
var index = this.users.indexOf(item);
```

下例展示了在观察器内部如何使用路径操作和`get`来获取数组元素及它的索引:

```
// Log user name changes by index
usersChanged(cr) {
  // handle paths like 'users.#4.name'
  var nameSubpath = cr.path.indexOf('.name');
  if (nameSubpath) {
    var itempath = cr.path.substring(0, nameSubpath);
    var item = this.get(itempath);
    var index = cr.base.indexOf(item);
    console.log('Item ' + index + ' changed, new name is: ' + item.name);
  }
}
```


### 总是将依赖添加到观察器参数中 {#dependencies}

观察器不能依赖任何没有在参数中列出的属性、子属性或路径.原因是观察器被调用时其它依赖可能还是undefined.例如:

```
properties: {
  firstName: {
    type: String,
    observer: 'nameChanged'
  },
  lastName: {
    type: String
  }
},
// WARNING: ANTI-PATTERN! DO NOT USE
nameChanged: function(newFirstName, oldFirstName) {
  // this.lastName could be undefined!
  console.log('new name:', newFirstName, this.lastName);
}
```

注册Polymer不保证属性以何种顺序被初始化.

通常如果观察器依赖于多个依赖关系,就使用
[多属性观察器](#multi-property-observers)并将每个依赖做为一个参数传递给观察器.这样确保了所有的依赖在观察器被调用之前是deinfed状态.

```
properties: {
  firstName: {
    type: String
  },
  lastName: {
    type: String
  }
},
observers: [
  'nameChanged(firstName, lastName)'
],
nameChanged: function(firstName, lastName) {
  console.log('new name:', firstName, lastName);
}
```

如果必须使用单个属性并且依赖其它的属性(例如需要访问被观察属性的旧值,这个是不能通过多属性观察来访问的),
要注意以下事项:

*   在观察器中使用依赖之前检查所有依赖是defined
    (例如`if this.lastName !== undefined`).
*   为依赖设置默认值.

记住,观察器只在有参数传递的依赖发生更改时才会被调用.例如,观察器虽然依赖于`this.firstName`但它没有在参数中列出所以当`this.firstName`更改时不会调用观察器.

## 计算属性 {#computed-properties}

计算属性是基于一个或多个路径经过计算得出的虚拟属性.计算属性的计算函数规则与复杂观察器一样,除非它的返回值被用于计算属性.

同复杂观察器一样,对于`undefined`值的处理**取决于所观察的属性数量**.查看[复杂观察器](#complex-observers)获取更多信息.

### 定义一个计算属性

Polymer支持基于其它属性而计算其值的虚拟属性.

要定义一个计算属性, 添加到`properties`对象时带`computed`键映射到计算函数:

```
fullName: {
  type: String,
  computed: 'computeFullName(first, last)'
}
```


函数中的参数为其所依赖的属性.依赖属性发生任何[可观察的更改](data-system#observable-changes)都会调用函数.

如果复杂观察器一样,处理`undefined`值**基于其所观察属性的数量**.

计算函数直到**所有**依赖属性都为defined(`!== undefined`)时才可调用
. 在`properties`中声明的每个依赖属性都应有一个默认`默认`值(或初始化为一个
非-`undefined`值)来保证属性被计算.

**注意:**
计算属性的定义看起来像[多属性观察器](#multi-property-observers),
并且二者行为几乎相同. 唯一的不同点是计算属性函数返回一个值来作为虚拟属性.
{ .alert .alert-info }

```
<dom-module id="x-custom">

  <template>
    My name is <span>{{fullName}}</span>
  </template>

  <script>
    Polymer({

      is: 'x-custom',

      properties: {

        first: String,

        last: String,

        fullName: {
          type: String,
          // when `first` or `last` changes `computeFullName` is called once
          // and the value it returns is stored as `fullName`
          computed: 'computeFullName(first, last)'
        }

      },

      computeFullName: function(first, last) {
        return first + ' ' + last;
      }

    });
  </script>

</dom-module>
```

计算函数中的参数可以是组件中的简单属性也可以是`观察器`中支持的任意参数类型包括[路径](#observing-path-changes)、
[通配符路径](#deep-observation)和[数组拼接的路径](#array-observation).
计算函数接收的参数同它的定义相匹配.

**注意:**
如果只需要一个计算属性用于数据绑定就可以使用计算绑定来替代.查看
[计算绑定](data-binding#annotated-computed).
{ .alert .alert-info }
