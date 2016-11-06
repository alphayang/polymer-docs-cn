---
title: "Step 4: 输入响应"
subtitle: "创建第一个Polymer组件"
---

当然，按钮如果不能按，就不是个真正的按钮.添加一个事件监听器让按钮工作. 添加一个事件监听器到宿主组件上(此处为`icon-toggle`), 添加一个`listeners`对象到组件原型上:

icon-toggle.html { .caption }

```
  Polymer({
    /* this is the element's prototype */
    is: 'icon-toggle',
    properties: {
      toggleIcon: String,
      pressed: {
        type: Boolean,
        value: false,
        notify: true,
        reflectToAttribute: true
      }
    },
    listeners: {
      'tap': 'toggle'
    },
    toggle: function() {
      this.pressed = !this.pressed;
    },
  });
```

主要内容:

*   [`listeners object`](/1.0/docs/devguide/events#event-listeners)
    提供了一种简单的方法来设置事件处理器.把事件名称映射到处理器.

*   `tap`事件由Polymer[gesture system](/1.0/docs/devguide/gesture-events)生成，当用户使用鼠标或手指点击/点触目标时.(`listeners`对象可以处理内置的事件,如
    `keydown`和`keyup`.)

保存`icon-toggle.html`文件然后查看demo. 现在可以点击按钮来查看切换的状态.

<img src="/images/1.0/first-element/databound-toggles.png" alt="Demo showing icon toggles with star and heart icons. The icons have a black border, and the pressed icons are colored red.">

**更多信息:数据绑定.** 想要看看demo是如何工作的, 打开 `icon-toggle-demo.html`
(下载的存放在`demo`文件夹.)
当然,这个组件的demo_同样_也是一个组件. 
组件使用<a href="/1.0/docs/devguide/data-binding#property-notification">双向数据绑定</a>和一个<a href="/1.0/docs/devguide/data-binding#annotated-computed">计算绑定</a>在切换按钮时修改显示字符串.
{ .alert .alert-info }

<a class="blue-button" href="step-3">
  Previous step: 使用数据绑定和属性
</a>

<a class="blue-button" href="step-5">
  Next step: 使用CSS属性进行自定义样式
</a>
