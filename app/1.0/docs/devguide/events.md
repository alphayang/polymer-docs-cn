---
title: 事件
---

<!-- toc -->

## 配置事件监听器 {#event-listeners}

添加到宿主组件上事件监听器提供了一个`listeners`对象来匹配事件到事件处理器方法名.

可以将事件监听器添加到`this.$`集合中的任意组件上,使用<code><var>nodeId</var>.<var>eventName</var></code>.

示例: { .caption }

```
<dom-module id="x-custom">
  <template>
    <div>I will respond</div>
    <div>to a tap on</div>
    <div>any of my children!</div>
    <div id="special">I am special!</div>
  </template>

  <script>
    Polymer({

      is: 'x-custom',

      listeners: {
        'tap': 'regularTap',
        'special.tap': 'specialTap'
      },

      regularTap: function(e) {
        alert("Thank you for tapping");
      },

      specialTap: function(e) {
        alert("It was special tapping");
      }

    });
  </script>
</dom-module>
```

## 批注式配置事件监听器 {#annotated-listeners}

要将事件监听器添加到local DOM子结点上,可以在模板中使用
<code>on-<var>event</var></code>批注. 这样就可以消除仅为了给组件绑定一个事件监听器而要赋与组件一个`id`.

示例: { .caption }

```html
<dom-module id="x-custom">
  <template>
    <button on-tap="handleTap">Kick Me</button>
  </template>
  <script>
    Polymer({
      is: 'x-custom',
      handleTap: function() {
        alert('Ow!');
      }
    });
  </script>
</dom-module>
```


**提示: 在事件中使用`on-tap`而不是`on-click`来触发可以在跨触摸(移动)和点击(桌面)设备中获得一致性**. 查看[手势事件](gesture-events)获取完整的跨平台一致事件列表.
{.alert .alert-info}

由于事件名称使用HTML标记进行了指定, **所以事件名称总是被转换为小写**. 这主要是因为HTML标记是大小写敏感的. 通过指定`on-myEvent`为`myevent`添加一个监听器. 事件处理器
_名称_ (例如`handleClick`) **也是**大小写敏感的.

**小写事件名称.** 当使用声明式处理器时,事件名被转换为小写, 原因是HTML标记是大小写敏感的.
所在标记`on-core-signal-newData`为`core-signal-newdata`配置了一个监听器,
_而不是_ `core-signal-newData`. 为了避免混乱所以事件名称要小写.
{.alert .alert-info}

## 强制添加和删除监听器 {#imperative-listeners}

使用[自动节点查找](local-dom#node-finding)和方法
[`listen`](/1.0/docs/api/Polymer.Base#method-listen)、
[`unlisten`](/1.0/docs/api/Polymer.Base#method-unlisten).

```js
this.listen(this.$.myButton, 'tap', 'onTap');
this.unlisten(this.$.myButton, 'tap', 'onTap');
```

监听器回调使用组件实例上的`this`进行调用.

如果强制添加了一个监听器就要强制的删除它.
通知在`attached`和`detached`
[回调](registering-elements#lifecycle-callbacks)中实现. 如果使用了[`listeners`](#event-listeners)对象或[批注式事件监听器](#annotated-listeners), Polymer会自动添加和删除事件监听器.

## 自定义事件 {#custom-events}

在宿主组件上使用`fire`方法来触发一个自定义事件. 可以在`fire`方法的参数中传递数据到事件处理器中.

示例: { .caption }

```html
<dom-module id="x-custom">
  <template>
    <button on-click="handleClick">Kick Me</button>
  </template>

  <script>
    Polymer({

      is: 'x-custom',

      handleClick: function(e, detail) {
        this.fire('kick', {kicked: true});
      }

    });

  </script>

</dom-module>
<x-custom></x-custom>

<script>
    document.querySelector('x-custom').addEventListener('kick', function (e) {
        console.log(e.detail.kicked); // true
    })
</script>
```


## 属性更改事件 {#property-changes}

当属性更改时可以配置组件触发一个不冒泡的DOM事件. 查看[更改通知协议](data-binding#change-notification-protocol)获取更多信息.

## 事件重定目标 {#retargeting}

Shadow DOM一个称为"event retargeting"的特性可以在事件冒泡时改变事件的目标,新目标须和接收组件在同一个范围里. (例如,主文档的监听器,它的目标是主文档里的一个组件而不是一个shadow树.)

Shady DOM不支持事件冒泡时重定目标,由于性能成本会过高. 反而在Polymer中提供一个机制在需要时来模仿事件重定目标.

使用`Polymer.dom(event)`获取一个在shady DOM和shadow DOM上提供了等效目标数据的归一化事件.特别是归一化事件有以下属性:

*   `rootTarget`: shadow重定目标前的原始目标或根目标T
    (等效于shadow DOM下的`event.path[0]`或shady DOM下`event.target`).

*   `localTarget`: 重定后的事件目标(等效于shadow DOM下的`event.target`). 此结点同监听器所监听的结点处于同一范围.

*   `path`: 事件会经过的所有结点数组
    (等效于shadow DOM下的`event.path`).

示例: { .caption }

```html
<!-- event-retargeting.html -->
 ...
<dom-module id="event-retargeting">
  <template>
    <button id="myButton">Click Me</button>
  </template>

  <script>
    Polymer({

        is: 'event-retargeting',

        listeners: {
          'click': 'handleClick',
        },

        handleClick: function(e) {
          console.info(e.target.id + ' was clicked.');
        }
      });
  </script>
</dom-module>


<!-- index.html  -->
  ...
<event-retargeting></event-retargeting>

<script>
  var el = document.querySelector('event-retargeting');
  el.addEventListener('click', function(){
    var normalizedEvent = Polymer.dom(event);
    // logs #myButton
    console.info('rootTarget is:', normalizedEvent.rootTarget);
    // logs the instance of event-targeting that hosts #myButton
    console.info('localTarget is:', normalizedEvent.localTarget);
    // logs [#myButton, document-fragment, event-retargeting,
    //       body, html, document, Window]
    console.info('path is:', normalizedEvent.path);
  });
</script>
```

上例中,原始事件被触发于`<event-retargeting>`组件的local DOM树中的`<button>`. 监听器添加在`<event-retargeting>`组件自身上, 位于主文档中.为了隐藏组件的实现,事件应被重定目标,所以看起来事件来自
`<event-retargeting>`而不是 `<button>`标记.

事件路径中出现的文档片段是local DOM树的根结点.在shady DOM中它是一个`DocumentFragment`实例.在原生shadow DOM中它是一个`ShadowRoot`的实例.
