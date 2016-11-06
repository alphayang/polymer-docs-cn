---
title: 动作事件

当声明式监听器添加了类型事件后,Polymer自动为某些用户动作触发自定义"gesture"事件. 这些事件在触摸和鼠标环境下同样被触发,所以我们推荐使用这些事件而不是它们的鼠标或触摸的特定对应事件. 这样为触摸和鼠标设备提供了更好的互操作性.例如 应使用`tap`而不是
`click`来获得更稳定的跨平台结果.

监听触摸输入中控制滚动方向的特定手势.
例如, 结点上有一个监听器用来监听`track`事件可以默认阻止滚动. 组件可以使用`this.setScrollDirection(direction, node)`来覆盖滚动方向,`direction`是`'x'`,
`'y'`, `'none'`,或`'all'`中的一样, `node`默认为`this`.

支持以下手势事件类型, 以及每种类型的简单描述和`event.detail`中的可用属性详情:

* **down**—手指/按钮按下
  * `x`—事件的clientX坐标
  * `y`—事件的clientY坐标
  * `sourceEvent`—引发`down`动作的原始DOM事件
* **up**—手指/按钮放开
  * `x`—事件的clientX坐标
  * `y`—事件的clientY坐标
  * `sourceEvent`—引发`up`动作的原始DOM事件
* **tap**—按下并放开
  * `x`—事件的clientX坐标
  * `y`—事件的clientY坐标
  * `sourceEvent`—引发`tap`动作的原始DOM事件
* **track**—手指/按钮按下时移动
  * `state`—指示跟踪状态的字符串:
      * `start`—跟踪第一次被检测到时触发(手指/按钮并移动超过了预设距离阀值)
      * `track`—跟踪时触发
      * `end`—跟踪结束时触发
  * `x`—事件的clientX坐标
  * `y`—事件的clientY坐标
  * `dx`—与上一个跟踪事件的水平像素更改
  * `dy`—与上一个跟踪事件的垂直像素更改
  * `ddx`—与最后一个跟踪事件的水平像素更改
  * `ddy`—与最后一个跟踪事件的垂直像素更改
  * `hover()`—用来获取可能被遮盖的组件函数

示例 { .caption }

```html
<dom-module id="drag-me">
  <template>
    <style>
      #dragme {
        width: 500px;
        height: 500px;
        background: gray;
      }
    </style>

    <div id="dragme" on-track="handleTrack">{{message}}</div>
  </template>

  <script>
    Polymer({

      is: 'drag-me',

      handleTrack: function(e) {
        switch(e.detail.state) {
          case 'start':
            this.message = 'Tracking started!';
            break;
          case 'track':
            this.message = 'Tracking in progress... ' +
              e.detail.x + ', ' + e.detail.y;
            break;
          case 'end':
            this.message = 'Tracking ended!';
            break;
        }
      }

    });
  </script>
</dom-module>
```

Example with `listeners` { .caption }

```html
<dom-module id="drag-me">
  <template>
    <style>
      #dragme {
        width: 500px;
        height: 500px;
        background: gray;
      }
    </style>

    <div id="dragme">{{message}}</div>
  </template>

  <script>
    Polymer({

      is: 'drag-me',

      listeners: {
        'dragme.track': 'handleTrack'
      },

      handleTrack: function(e) {
        switch(e.detail.state) {
          case 'start':
            this.message = 'Tracking started!';
            break;
          case 'track':
            this.message = 'Tracking in progress... ' +
              e.detail.x + ', ' + e.detail.y;
            break;
          case 'end':
            this.message = 'Tracking ended!';
            break;
        }
      }
    });
  </script>
</dom-module>
```
