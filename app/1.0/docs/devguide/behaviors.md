---
title: 行为
---

<!-- toc -->

Polymer支持使用称为_行为_的共享代码模块来扩展自定义组件原型.

行为是一个看起来像一个典型的Polymer原型的对象. 行为可以定义
[生命周期回调](registering-elements#basic-callbacks),
[声明的属性](properties), [默认标记](registering-elements#host-attributes),
[`观察器`](properties#observing-changes-to-multiple-properties), 和 [`监听器`](events#event-listeners).

要在Polymer组件定义中添加一个行为,只需将其添加到到原型的`behaviors`数组.

```
Polymer({
  is: 'super-element',
  behaviors: [SuperBehavior]
});
```

F对于生命周期事件,生命周期回调按照在`behaviors`数组中的顺序进行调用, 位于原型回调之后.

在行为对象的任意非生命周期函数混合在父类的原型中, **除非原型中已经定义一个同名的函数.**  若多个行为定义了相同的函数,那么`behaviors`数组中的
**最后一个** 行为优先于其它行为.

## 定义行为

要定义行为,需要创建一个可在自定义组件中引用的JavaScript对象.
下例中简单添加国`HighlightBehavior`到全局:


`highlight-behavior.html`: { .caption }

```
<script>
    HighlightBehavior = {

      properties: {
        isHighlighted: {
          type: Boolean,
          value: false,
          notify: true,
          observer: '_highlightChanged'
        }
      },

      listeners: {
        click: '_toggleHighlight'
      },

      created: function() {
        console.log('Highlighting for ', this, 'enabled!');
      },

      _toggleHighlight: function() {
        this.isHighlighted = !this.isHighlighted;
      },

      _highlightChanged: function(value) {
        this.toggleClass('highlighted', value);
      }

    };
</script>
```

`my-element.html`: { .caption }
```
<link rel="import" href="highlight-behavior.html">

<script>
  Polymer({
    is: 'my-element',
    behaviors: [HighlightBehavior]
  });
</script>
```

Polymer不会指定任意特定方法来引用行为. 由Polymer团队创建的行为会添加到Polymer对象. 当创建自己的行为时, 需使用其它的命名空间来避免与Polymer行为功能的冲突.例如:

```
window.MyBehaviors = window.MyBehaviors || {};
MyBehaviors.HighlightBehavior = { ... }
```

这里的H`MyBehaviors`命名空间被显式的添加到了全局`window`对象, 所以行为可以在组件使用`MyBehaviors.HighlightBehavior`来引用.

## 扩展行为 {#extending}

扩展一个行为或创建的行为包含了已有行为,就要把行为定义为一个行为数组:

```
<!-- import an existing behavior -->
<link rel="import" href="oldbehavior.html">

<script>
  // Implement the extended behavior
  NewBehaviorImpl = {
    // new stuff here
  }

  // Define the behavior
  NewBehavior = [ OldBehavior, NewBehaviorImpl ]
</script>
```

在组件的`behaviors`数组中,最右边的行为优先于更早加入到数组的行为.
此时,任意定义在`NewBehaviorImpl`会优先于其它`OldBehavior`的定义.

对行为数组中的每个元素进行命名是一个很好的实践, 因为它使得行为可以显式引用它所扩展的其它行为中的方法s(例如`NewBehaviorImpl`调用 `OldBehavior`中的方法).

## 在注册时执行任务

某些情况下,行为可能需要在组件注册时执行一些一次性任务. 例如, 分配一个供所有组件实例访问的共享对象或修改组件原型.

例如`iron-a11y-keys-behavior`允许组件和其它行为在原型上使用一个`keyBindings`来添加绑定. 一个组件可以拥有多个`keyBindings`对象, 一个由自身prototype提供和几个继承自其它行为. `iron-a11y-keys-behavior`
使用了`registered`回调来来把这些`keyBindings`对象整理为组件原型上的单一对象.

下面的简单示例显示了`iron-a11y-keys-behavior`如何整个多个行为上的对象
.

```
registered: function() {
  // collate keyBindings objects from behaviors & element prototype
  var keyBindings = this.behaviors.map(function(behavior) {
    return behavior.keyBindings;
  });
  if (keyBindings.indexOf(this.keyBindings) === -1) {
    keyBindings.push(this.keyBindings);
  }
  // process key bindings in order
  keyBindings.forEach(function() {
    ...
  });
}
```
