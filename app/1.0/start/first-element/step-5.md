---
title: "Step 5: 使用CSS属性进行自定义样式"
subtitle: "创建第一个Polymer组件"
---

现在已经有了一个具有基本功能的按钮. 但是现有的文字颜色在按下和放开的时候都是一样的. 想更漂亮的效果怎么办呢?

Local DOM可以阻止用户在组件内意外的修改组件样式.为了让用户可以修改组件的样式,可以使用_custom CSS properties_. Polymer
提供了一个定义CSS属性的实现，这个实现参考了
[CSS Custom Properties for Cascading Variables](http://www.w3.org/TR/css-variables/).

可以在组件中使用`var`方法来应用一个自定义属性.


<pre><code>background-color: var(<em>--my-custom-property</em>, <em>defaultValue</em>);</pre></code>

Where <code>--<em>my-custom-property</em></code> is a custom property name, always starting with two dashes (`--`), and <code><em>defaultValue</em></code> is an (optional) CSS value that's used if the custom property isn't set.

## 添加新的自定义属性值

修改组件的`<style>`标签并替换现有的`fill`和`stroke`值为自定义属性:

icon-toggle.html  { .caption }

```
  <style>
    /* local styles go here */
    :host {
      display: inline-block;
    }
    iron-icon {
      fill: var(--icon-toggle-color, rgba(0,0,0,0));
      stroke: var(--icon-toggle-outline-color, currentcolor);
    }
    :host([pressed]) iron-icon {
      fill: var(--icon-toggle-pressed-color, currentcolor);
    }
  </style>
```

由于默认值, 你仍然可以用设置`color`的方法来定义`<icon-toggle>`的样式, 但是你现在有其它的选择了.

编辑`icon-toggle-demo.html`并设置新的属性. (下载的文件位于`demo`文件夹中.)

icon-toggle-demo.html { .caption }

```
    <style>
      :host {
        font-family: sans-serif;
        --icon-toggle-color: lightgrey;
        --icon-toggle-outline-color: black;
        --icon-toggle-pressed-color: red;
      }
    </style>
```

运行demo查看新样式的效果.


<img src="/images/1.0/first-element/toggles-styled.png" alt="Demo showing
icon toggles with star and heart icons. The heart icon is pressed and the text
above it reads, 'You really like me!'">

你的组件就完成了.创建了一个带有基本UI、API和自定义样式属性的组件.

如果组件不能正常工作, 可以查看在线的
[完成版本](https://github.com/googlecodelabs/polymer-first-elements/blob/master/icon-toggle-finished/icon-toggle.html).

如果想学习更多的自定义属性，继续吧..

## 更多的功能: 设置文件级别的自定义属性

经常需要定义页面级别的自定义属性值,例如来设置整个应用的样式. 由于自定义属性还没有得到所有浏览器的支持,你需要使用一个特别的`custom-style`标签来定义Polymer组件之外的自定义属性. 尝试在`index.html`文件的`<head>`标签内添加如下代码:

```
    <style is="custom-style">
      /* Define a document-wide default—will not override a :host rule in icon-toggle-demo */
      :root {
        --icon-toggle-outline-color: red;
      }
      /* Override the value specified in icon-toggle-demo */
      icon-toggle-demo {
        --icon-toggle-pressed-color: blue;
      }
      /* This rule does not work! */
      body {
        --icon-toggle-color: purple;
      }
    </style>
```

主要内容:

*   `:root` 页面中最高级另的标准CSS选择器,_通常_等同于`html`.
    在`custom-style`组件中, **需要使用`:root`而不是`html`来指定页面的默认值.**

*   `icon-toggle-demo`选择器用来匹配`icon-toggle-demo`组件,在`icon-toggle-demo`内拥有比`:host`规则**更高的**优先级，可以用来覆盖`:host`中的值.

*   自定义属性**只能**只能定义用来匹配`:root`选择器 **或一个Polymer自定义组件.**的规则集合.这是用Polymer实现自定义属性的限制.

重新运行demo, 可以看到按下的按钮为蓝色,但是**主面和轮廓颜色并没有变.**

`--icon-toggle-color`属性因为不能被应用到`body`所以没有set方法. 尝试移动到`icon-toggle-demo`中来应用它.

`:root`规则集合为`--icon-toggle-outline-color`创建一个页面范围内的默认值.
但是这个值被`icon-toggle-demo`组件相应的规则给重写了. 想要查看默认值, 需要将注释掉
`icon-toggle-demo.html`中对应的规则:

icon-toggle-demo.html { .caption }

```
    <style>
      :host {
        font-family: sans-serif;
        --icon-toggle-color: lightgrey;
        /* --icon-toggle-outline-color: black; */
        --icon-toggle-pressed-color: red;
      }
    </style>
```

最后, 为了在`custom-style`中匹配一个选择器, 组件必须处于**页面范围内**—，比如`index.html`, 而不是在另一个local DOM的组件内. 比如这些规则**不会**在
`custom-style`中生效:

```
    iron-icon {
      --iron-icon-width: 40px;
      --iron-icon-height: 40px;
    }
```

由于`iron-icon`组件位于另一个local DOM的组件中. 然而, 由于自定义属性继承自DOM树, 可以在页面级别来为所有`iron-icon`组件设置这些属性:

```
    :root {
      --iron-icon-width: 40px;
      --iron-icon-height: 40px;
    }
```

快使用Polymer CLI来创建属于自己的组件吧！
[创建一个组件项目](/1.0/docs/tools/polymer-cli#element).

也可以看看[构建一个应用](/1.0/start/toolbox/set-up)
的教程来开始使用Polymer App Toolbox构建应用.

或者查看之前的章节:

<a class="blue-button" href="step-4">
  上一步: 输入响应
</a>
