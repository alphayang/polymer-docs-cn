---
title: 操作对象和数组数据
---

<!-- toc -->

数据系统提供了方法在组件的模型数据(属性和子属性)上产生[可观察的更改](data-system#observable-changes).使用这些方法在数组和对象子属性上产生可观察的更改.

相关原理:

-   [数据路径](data-system#paths).
-   [可观察的更改](data-system#observable-changes).

## 指定路径

[数据路径](data-system#paths)是一系列路径段. *大多数情况下*, 每个路径段是一个属性名.数据API接受两类路径:

*   以点分割的路径段字符串.

*   字符串数组,每个数据元素可以是路径段或一个点连接路径.

以下都表示一个相同的路径The following all represent the same path:


```
"one.two.three"
["one", "two", "three"]
["one.two", "three"]
```

路径段有一些特殊类型.


*   通配符路径(如`foo.*`)表示 _给定路径和其子属性的所有更改_,
    包括数组变换.
*   数组变动路径(如`foo.splices`)表示给定数组的所有数组变动.
*   数组元素路径(如`foo.11`)表示数组中的一个元素.

## 获取路径上的值 {#get-value}

使用[`get`](/1.0/docs/api/Polymer.Base#method-get)方法来获取给定路径上的值.

```
// retrieve a subproperty by path
var value = this.get('myProp.subProp');
// Retrieve the 11th item in myArray
var item = this.get(['myArray', 11])

```

## 设置路径上的属性或子属性 {#set-path}

使用[`set`](/1.0/docs/api/Polymer.Base#method-set)方法在子属性上产生一个[可观察的更改](data-system#observable-changes).

```
// clear an array
this.set('group.members', []);
// set a subproperty
this.set('profile.name', 'Alex');
```

如果属性或子属性的值没有更改,则调用`set`没有任何效果. 特定情况下, 对对象属性调用不会导致Polymer获取对象子属性的更改,除非对象自身发生了更改.如同对数组属性调用`set`不会导致Polymer获取数组变换一样.

```
// DOES NOT WORK—use notifyPath instead
this.profile.name = Alex;
this.set('profile', this.profile);

// DOES NOT WORK—use notifySplices instead
this.users.push({name: 'Grace'});
this.set('users', this.users);
```

相关任务:

-   [通知子属性更改到Polymer](#notify-path).
-   [通知数组变换到Polymer](#notifysplices)

### 通知子属性更改到Polymer {#notify-path}

对象的子属性发生更改后,调用`notifyPath`让更改被数据系统
[_观察到_](data-system#observable-changes).

```
this.profile.name = Alex;
this.notifyPath('profile.name');
```

调用`notifyPath`时, 需要使用发生更改的 **准确路径**.例如,调用
`this.notifyPath('profile')`不会获取对`profile.name`的更改,因为`profile`对象自身没有更改.

当多个子属性发生更改或不知道发生的具体更改时,查看
[覆盖脏检查](#override-dirty-check).


## 操作数组 {#work-with-arrays}

使用Polymer的数组变换方法对数组产生[可观察更改](data-system#observable-changes).

如果使用原生方法(如`Array.prototype.push`)来操作数组,操作后可以通知Polymer.

注意Polymer的数组处理有以下的约束:

-   **数组元素必须唯一**. 数据系统使用对象标识来比较数组元素所以数组元素必须唯一.
-   **不支持基本类型的数组元素.** 由于基本类型(如数值,字符串和布尔值)如查对象相同则值也相同. 考虑一个数值数组:

    ```
    this.numbers = [1, 1, 2];
    ```

    数组系统不能处理这个数组属性的更改,因为前两个元素不唯一.

可以将基本类型包装到对象中来确保唯一性,就可以绕过这个约束:

```
this.numbers = [{ value: 1}, {value: 1}, {value: 3}];
```

### 数组变换 {#array-mutation}

修改数组时Polymer组件原型`Array.prototype`提供了一组进行数组变换的方法来,只是这些方法第一个参数是一个字符串的`path`.  `path`参数标明了组件上需要变换的数组,其它方法都和原生的`Array`方法一样.

这些方法用于进行数组变换操作然后将更改通知给其它绑定到数组的组件. 必须使用这些方法来确保能被其它组件观察的数组变换(通过观察器,计算属性或数据绑定)保持同步.

每个Polymer组件都有以下的数组变换方法:

*   <code>push(<var>path</var>, <var>item1</var>, [..., <var>itemN</var>])</code>
*   <code>pop(<var>path</var>)</code>
*   <code>unshift(<var>path</var>, <var>item1</var>, [..., <var>itemN</var>])</code>
*   <code>shift(<var>path</var>)</code>
*   <code>splice(<var>path</var>, <var>index</var>, <var>removeCount</var>, [<var>item1</var>,
    ..., <var>itemN</var>])</code>

示例: { .caption }

```
<dom-module id="custom-element">
  <template>
    <template is="dom-repeat" items="[[users]]">{{item}}</template>
  </template>

  <script>
    Polymer({

      is: 'custom-element',

      addUser: function(user) {
        this.push('users', user);
      },

      removeUser: function(user) {
        var index = this.users.indexOf(user);
        this.splice('users', index, 1);
      }

    });
  </script>
</dom-module>
```

### 将数组变换通知给Polymer {#notifysplices}


尽可能的多使用Polymer的[数组变换方法](#array-mutation).
然而这并不总是可以达到的.例如使用了一个没有用Polymer数组变换方法的第三方库.这时可以在变换之后调用
<a href="/1.0/docs/api/Polymer.Base#method-notifySplices">`notifySplices`</a>
来确保任意观察数组的Polymer组件得到更改的通知.

`notifySplices`方法要求数组变换可以被 *归一化* 为一系列的`splice`操作.例如在数组上调用`shift`来移除数组的第一个元素,等同于调用`splice(0, 1)`.

为了让组件可以更新内部的数组,拼接应按索引顺序应用.

若不需知道所发生的确切更改(例如使用第三方库来操作数组),就可以强制数据系统来刷新整个可见数组[覆盖脏检查](#override-dirty-check).

### 使用键来查找数组元素 {#get-array-item}

可以简单使用[使用路径获取值](#get-value)中提到的`get`方法来使用键获取数组元素.

```
var item = this.get(['myArray', key]);
```

### 查找数组元素的索引 {#get-array-index}

在某些情况下,例如在观察器内部,有数组键或数组元素但没有它的索引.如果元素的键或完整路径,使用`get`来查找元素. 然后使用标准数组`index`方法来确定索引.

```
// Delete an item, based on the item's key
var item = this.get(['myArray', key]);
var index = this.myArray.indexOf(item);
if (index != -1) {
  this.splice('myArray', index, 1, )
}
```

## 重写脏检查 {#override-dirty-check}

当处理[可观察更改](data-system#observable-changes)时, Polymer执行脏检查,如果指定路径的值没有更改则不会影响对属性有任何操作.

有时,当对象或数组有了更改或不知道确定的更改时,Sometimes, when you have a number of changes to an object or array, or you don't know the exact
changes to the object or array, 最好跳过脏检查.

为了强制数组系统跳过脏检查, 先对路径赋值为空对象或空数组,
然后再赋值为原始的对象或数组.这样数据系统就会重新评估该路径和它的子路径上的所有属性.

```
// Force data system to pick up subproperty changes
var address = this.address;
this.address = {};
this.address = address;
```

```
// Force data system to pick up array mutations
var array = this.myArray;
this.myArray = [];
this.myArray = array;
```

这个模式同样适用于使用`set`:

```
// Force data system to reevaluate a subproperty
var members = this.group.members;
this.set('group.members', []);
this.set('group.members', members);
```

如果属性是一个大数组或复杂对象,这个过程可能很耗时.

## 连接两个路径个同一个对象 {#linkPaths}

使用[`linkPaths`](/1.0/docs/api/Polymer.Base#method-linkPaths)方法来关联两个路径.
当一个组件需要有两个路径引用到相同的对象时要使用`linkPaths`,查看
[两个路径引用相同的对象](data-system#two-paths).

当两个路径 *连接* 后, 任一路径上[可观察的更改](data-system#observable-changes)在另一路径上也可是可观察的.

```js
linkPaths('selectedUser', 'users.1');
```

**两个路径都必须是对同一个组件的相对路径.** 需要在_组件间_传递更改时,就要使用[数据绑定](data-binding).
{.alert .alert-info}



调用`unlinkPaths`方法并传入想要移除的路径就可以移除一个路径连接:

```js
unlinkPaths('selectedUser');
```
