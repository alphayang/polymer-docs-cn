---
title: 声明属性
---

<!-- toc -->

可以通过在原型上添加`properties`对象来为自定义组件添加声明属性. 允许用户使用标记的属性来为`properties`添加属性(查看
[属性反序列化](#attribute-deserialization)获取更多信息).
**任意自定义组件的公开属性API都应在`properties`对象中声明.**

另外, `properties`对象可以用来指定:

*   属性类型.
*   默认值.
*   属性更改观察器. 当属性值有更改时调用一个方法.
*   只读状态. 防止对属性值的意外修改.
*   双向数据绑定支持. 当属性值更改时触发一个事件.
*   计算属性. 其它属性更改时动态计算新值.
*   与标记属性关联. 当属性值更改时更新相应的标记值Updates the corresponding attribute value when the property
    value changes.

很多特性都紧密集成到了[数据系统](data-system), 在数据系统部分也有文档说明.

示例 { .caption }

```
Polymer({

  is: 'x-custom',

  properties: {
    user: String,
    isHappy: Boolean,
    count: {
      type: Number,
      readOnly: true,
      notify: true
    }
  },

  ready: function() {
    this.textContent = 'Hello World, I am a Custom Element!';
  }

});
```

`properties`对象对各个属性支持的键如下:

<table>
<tr>
<th>Key</th><th>Details</th>
</tr>
<tr>
<td><code>type</code></td>
<td>
Type: constructor (one of <code>Boolean</code>, <code>Date</code>, <code>Number</code>, <code>String</code>, <code>Array</code> or <code>Object</code>)<br>

标记属性类型用于对标记属性的反序列化. 不像0.5版本中，
属性类型是显式的，使用类型构造函数指定。 查看
<a href="#attribute-deserialization">标记属性反序列化</a>获取更多信息.

</td>
</tr>
<tr>
<td><code>value</code></td>
<td>
Type: <code>boolean</code>, <code>number</code>, <code>string</code> or <code>function</code>.<br>

属性的默认值. 如果 <code>value</code>是一个函数且被调用后返回值做为属性值. 如果默认值是一个数组或对象实例则在函数内部创建数组或对象.
查看<a href="#configure-values">配置属性默认</a>获取更多信息.
</td>
</tr>
<tr>
<td><code>reflectToAttribute</code></td>
<td>Type: <code>boolean</code><br>

为<code>true</code>时将和宿主结点的HTML标记属性值联动. 如果属性值为Boolean那么就创建一个标准的HTMLboolean标记(是true而不是false).
对于其它属性类型,标记值是属性值的字符串形成. 等同于在 Polymer 0.5 中的<code>reflect</code>.
查看<a href="#attribute-reflection">属性和标记联动</a>来获取更多信息.
</td>
</tr>
<tr>
<td><code>readOnly</code></td>
<td>Type: <code>boolean</code><br>

为<code>true</code>时属性不能直接使用赋值或数据绑定来设置.查看 <a href="#read-only">只读属性</a>.
</td>
</tr>
<tr>
<td><code>notify</code></td>
<td>Type: <code>boolean</code><br>

为<code>true</code>时属性可使用双向绑定.另外当属性更改时会触发<code><var>属性名</var>-changed</code>事件. 查看<a href="#notify">属性更改通知事件(notify)</a>
获取更多信息.
</td>
</tr>
<tr>
<td><code>computed</code></td>
<td>Type: <code>string</code><br>

属性值被解析为一个函数名和参数列表. 当参数值更改时调用函数为计算属性值. 计算属性全部是只读. 查看<a href="observers#computed-properties">计算属性</a>
获取更多信息.
</td>
</tr>
<tr>
<td><code>observer</code></td>
<td>Type: <code>string</code><br>

属性值更改时调用以属性值为函数名称的函数. 注意不同于0.5版本, <strong>属性更改接收器必须显示注册.</strong> <code><var>propertyName</var>Changed</code> 函数会被自动调用. 查看<a href="observers">属性更改回调(观察器)</a>
获取更多信息.
</td>
</tr>
</table>

## 属性名称与标记名称匹配 {#property-name-mapping}

在数据绑定中属性和标记的序列化和反序列化都是由Polymer来对属性名称和标记名称进行匹配来完成的.

当进行属性名称与标记名称匹配:

*   标记名称转化为小写的属性名称. 例如
    `firstName`标记匹配到`firstname`.

*   标记名称中的_中划线_转化为_camelCase_属性名称,每个中划线之后的都进行首字母大写并去掉中划线.
    例如标记`first-name`匹配为`firstName`.

从属性名称转化为标记名称时恰好相反的操作(例如属性被定义为`reflectToAttribute: true`.)

**兼容性考虑:** 在0.5版本中, Polymer会把标记名称匹配到相应的属性名称.
例如标记`foobar`会匹配到其所在组件内的属性`fooBar`. **在1.0中不会发生**—标记与属性的匹配是基于根据以上规则所注册的组件.
{ .alert .alert-warning }

## 标记反序列化 {#attribute-deserialization}

如果属性在`properties`对象中进行了配置, 实例中匹配属性名称的标记会根据指定类型进行反序列化并赋值给组件实例相对应的属性.

如果属性没有指定其它`properties`选项, `type`
(由类型构造函数指定如`Object`, `String`等等.)就可以直接设置为`properties`对象的属性值; 除此之外它应当在`properties`配置对象中充当`type`键的值.

类型系统支持Boolean和数值,对象和数组值以JSON形式存在 ,日期对象则以任意可解析的日期字符串形式存在.

Boolean属性基于标记的_存在_与否:
如果标记存在, 不论标记_值_为何其属性值总是`true`. 如果标记不存在,其属性就被赋以默认值.

示例: { .caption }

```
<script>

  Polymer({

    is: 'x-custom',

    properties: {
      user: String,
      manager: {
        type: Boolean,
        notify: true
      }
    },

    attached: function() {
      // render
      this.textContent = 'Hello World, my user is ' + (this.user || 'nobody') + '.\n' +
        'This user is ' + (this.manager ? '' : 'not') + ' a manager.';
    }

  });

</script>

<x-custom user="Scott" manager></x-custom>
<!--
<x-custom>'s text content becomes:
Hello World, my user is Scott.
This user is a manager.
-->
```

为了在组件中使用标记来配置came-case属性, 标记名中需使用中划线形式.

Example: { .caption }

```
<script>

  Polymer({

    is: 'x-custom',

    properties: {
      userName: String
    }

  });

</script>

<x-custom user-name="Scott"></x-custom>
<!-- Sets <x-custom>.userName = 'Scott';  -->
```

**注意:** 反序列化会在创建期和运行期都发生(例如标记由于`setAttribute`发生了更改).然后建议只使用静态标记来配置属性而不直接在运行期改变属性.

### 配置布尔属性

对于标记中可配置的布尔属性, 默认值必须为`false`.如果默认为`true`, 由于标记的存在不论值是什么都将解析为`true`,尽管被设置为`false`. 这是web平台上标记的标准行为.

如果这个行为不能满足需要,可以使用字符串值或数值标记来代替.

### 配置对象和数组属性

可以使用JSON格式的对象或数组为对象和数组属性赋值:

```
<my-element book='{ "title": "Persuasion", "author": "Austen" }'></my-element>
```

注意JSON需要双引号,如上所示.

## 配置属性默认值 {#configure-values}

可以使用`properties`对象的`value`字段来指定属性的默认值.  值可以是基本类型也可以是一个函数的返回值.

如果使用函数,Polymer对_每个实例_调用一次函数.

当将属性初始化为对象或数组时使用一个函数来确保各个组件拥有自己的值而不是在所有组件的实例间共享一个对象或数组.

示例: { .caption }

```
Polymer({

  is: 'x-custom',

  properties: {

    mode: {
      type: String,
      value: 'auto'
    },

    data: {
      type: Object,
      notify: true,
      value: function() { return {}; }
    }

  }

});
```


## 属性改变通知事件(通知) {#notify}

当属性设置了`notify: true`, 当属性值更改时就会触发一个事件. 事件名称为:

<code><var>property-name</var>-changed</code>

<code><var>property-name</var></code>是带有中划线的属性名.例如,`this.firstName`的改变触发了
`first-name-changed`.

这些事件被用于双向数据绑定. 外部脚本也可直接使用`addEventListener`来监听些事件(如`first-name-changed`).

查看[数据流动](data-system#data-flow)获取更多关于属性更改通知和数据系统.

## 只读属性 {#read-only}

当一个属性只"produces"数据却从不消费数据, 可以显式设置`properties`属性定义中的`readOnly`标志为`true`用来避免宿主造成的意外改变.为了让组件可以修改属性的值必须使用一个私有的赋值器，其形式为<code>\_set<var>Property</var>(value)</code>.

```
<script>
  Polymer({

    properties: {
      response: {
        type: Object,
        readOnly: true,
        notify: true
      }
    },

    responseHandler: function(response) {
      this._setResponse(response);
    }

  });
</script>
```

想了解更多关只读属性和数据绑定可查看
[如何控制数据流动](data-system#data-flow-control).


## 属性和标记联动  {#attribute-reflection}

在特定情况下, 保持一个HTML标记值和属性值的同步是很有用的.  这个可以通过设置`properties`配置对象的`reflectToAttribute: true`来达到.这就使得任何属性的[可观察的更改](data-system#observable-changes)都被序列到与之同名的标记.

```
<script>
  Polymer({

    properties: {
     response: {
        type: Object,
        reflectToAttribute: true
     }
    },

    responseHandler: function(response) {
      this.response = 'loaded';
      // results in this.setAttribute('response', 'loaded');
    }

  });
</script>
```

### 标记序列化 {#attribute-serialization}

当反射属性到标记时或
[绑定属性到标记时binding a property to an attribute](data-binding#attribute-binding),
属性值被_序列化_成标记.

默认情况下根据属性值的_当前_类型进行序列化
(不论属性的`type`值):

*   `String`. 无需序列化.
*   `Date` 或 `Number`. 使用`toString`进行序列化.
*   `Boolean`. 为`true`时序列化为一个非值标记,为`false`无任何序列化结果为空.
*   `Array`或`Object`. 使用`JSON.stringify`进行序列化.

可以覆盖组件的`serialize`方法来为组件提供一个自定义序列化方式.

## 移到它处的部分=

以下部分移至[观察器和计算属性](observers):

<a href="#change-callbacks"></a>

-   [观察一个属性](observers#change-callbacks).

    <a href="#multi-property-observers"></a>

-   [观察多个属性或路径](observers#multi-property-observers).

    <a href="#observing-path-changes"></a>

-   [观察一个子属性的更改](observers#observing-path-changes).

    <a href="#array-observation"></a>

-   [观察数组的更改](observers#array-observation).

    <a href="#key-splices"></a>

-   [跟踪关键拼接](observers#key-splices).

    <a href="#deep-observation"></a>

-   [观察深度子属性](observers#deep-observation).

    <a href="#key-paths"></a>

-   [观察数组元素的深度子属性](observers#key-paths).

    <a href="#dependencies"></a>

-   [观察器参数中总是包含信赖](observers#dependencies).

    <a href="#computed-properties"></a>

-   [计算属性](observers#computed-properties)

以下部分移至[操作对象和数组](model-data):

<a href="#array-mutation"></a>

-   [操作数组](model-data#array-mutation).

    <a href="#notifysplices"></a>

-   [数组的操作通知给Polymer](model-data#notifysplices).


