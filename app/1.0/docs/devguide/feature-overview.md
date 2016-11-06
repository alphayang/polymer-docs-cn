---
subtitle: 功能概览
title: Polymer库
---

Polymer库提供了用来创建自定义组件的一组功能.这些功能被设置用来让开发一个像标准DOM组件一样工作的自定义组件变得容易且快速. 与标准DOM组件类似, Polymer组件可以 :

* 使用一个构造器或`document.createElement`进行初始化.
* 使用标记或属性进行配置.
* 由各个实例内部的DOM进行填充.
* 响应属性和标记的更改 (例如向DOM填充数据或触发一个事件).
* 使用内部默认样式或外部样式.
* 响应操作操作状态的方法.

一个基本的Polymer组件定义如下:

```
    <dom-module id="element-name">

      <template>
        <style>
          /* CSS rules for your element */
        </style>

        <!-- local DOM for your element -->

        <div>{{greeting}}</div> <!-- data bindings in local DOM -->
      </template>

      <script>
        // element registration
        Polymer({
          is: "element-name",

          // add properties and methods on the element's prototype

          properties: {
            // declare properties for the element's public API
            greeting: {
              type: String,
              value: "Hello!"
            }
          }
        });
      </script>

    </dom-module>
```


本指南将功能分为如下几组:

*   [注册和生命周期](registering-elements). 注册组件将内(原型)和组件名称关联. 组件提供了回调来管理它的生命周期. 使用行为来共享代码.

*   [声明式属性Declared properties](properties). 声明式属性可以使用标记来配置.声明式属性可选地支持更改观察器,双向绑定和标记映射.
    同样可以声明计算属性和只读属性.

*   [Local DOM](local-dom). Local DOM是由组件创建和管理的DOM.

*   [Events](events). 添加事件监听器到宿主对象和local DOM子结点. 事件重定向.

*   [Data binding](data-binding). 属性绑定. 绑定到标记.

*   [Behaviors](behaviors). 行为是可重用的代码模块用来混入Polymer组件中.

*   [Utility functions](instance-methods). 常用任务的辅助方法.

*   [Experimental features and elements](experimental). 试验性模板和样式功能.

如果是从现有的0.5版本组件迁移到新的API上,查看[迁移指南](/1.0/docs/migration)来获取建议.

使用是从0.8版本升级,查看Release notes](/1.0/docs/release-notes).
