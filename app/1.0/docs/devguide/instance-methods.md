---
title: 实例方法
---

<!-- toc -->

只需要在组件的原型上添加一个方法就可以为组件的添加一个实例方法.

```
Polymer({
    is: 'cat-element',
    _says: 'meow',
    speak: function() {
      console.log(this._says);
    }
});
```

可以在组件的任意实例中调用此方法.

```
var cat1 = document.querySelector('cat-element');
cat1.speak();
var cat2 = document.createElement('cat-element');
cat2.speak();
```


## 内建方法 {#instance-methods}

所有的Polymer组件都继承自[`Polymer.Base`](/1.0/docs/api/Polymer.Base),它提供了一组方便实例使用的便利功能.

这一节总结了一些常用的实例方法.查看[`Polymer.Base`](/1.0/docs/api/Polymer.Base)API文档获取完整方法列表.



*   [`$$(selector)`](/1.0/docs/api/Polymer.Base#method-$$). 返回组件的local DOM中匹配`selector`的的第一个结点.

*   [`fire(type, [detail], [options])`](/1.0/docs/api/Polymer.Base#method-fire). 触发一个自定义事件.`options`对象包含以下属性:

    -   `node`. 触发事件的结点(默认为`this`).

    -   `bubbles`. 事件是否应该向外传递. 默认`true`.

    -   `cancelable`. 事件是否可以用`preventDefault`来取消.默认为`false`.

### 异步和去抖

*   [`async(method, [wait])`](/1.0/docs/api/Polymer.Base#method-async). 异步调用`method`. 如果没有指定等待时间就以微任务时段(当前方法完成之后,事件队列中的下一个任务开始执行之前)来运行任务. 返回值可用来取消任务.

*   [`cancelAsync(handle)`](/1.0/docs/api/Polymer.Base#method-cancelAsync). 取消所属的异步任务.

*   [`debounce(jobName, callback, [wait])`](/1.0/docs/api/Polymer.Base#method-debounce). 调用`去抖`用以合并同一任务的多个请求到一个调用中,合并会在超过等待时间内没有新请求时调用.  若没有设置等待时间回调在微任务时段内调用(保证在paint之前).

*   [`cancelDebouncer(jobName)`](/1.0/docs/api/Polymer.Base#method-cancelDebouncer). 取消活动去抖器而不调用回调.

*   [`flushDebouncer(jobName)`](/1.0/docs/api/Polymer.Base#method-flushDebouncer). 立即调用去抖回调,取消去抖器.

*   [`isDebouncerActive(jobName)`](/1.0/docs/api/Polymer.Base#method-isDebouncerActive). 若相应的去抖任务在等待执行则返回true.

### 类和属性操作

*   [`toggleClass(name, bool, [node])`](/1.0/docs/api/Polymer.Base#method-toggleClass). 切换宿主组件上的命名布尔类,`bool`为真是添加类,为假时删除.如果指定了`node`, 则切换`node`上的类而不是宿主组件.

*   [`toggleAttribute(name, bool, [node])`](/1.0/docs/api/Polymer.Base#method-toggleAttribute). 如同`toggleClass`, 但是切换命名布尔类属性.

*   [`attributeFollows(name, newNode, oldNode)`](/1.0/docs/api/Polymer.Base#method-attributeFollows). 从`oldNode`上移动布尔属性到`newNode`,移除`oldNode`上的属性值(如果有的话)并设置`newNode`上的值.

*   [`classFollows(name, newNode, oldNode)`](/1.0/docs/api/Polymer.Base#method-classFollows). 从`oldNode`上移动类到`newNode`，移除`oldNode`上的类然后添加到`newNode`.


### CSS转换

*   [`transform(transform, [node])`](/1.0/docs/api/Polymer.Base#method-transform). 将CSS转换应用到指定结点,或宿主组件(没有指定结点).
    `transform`以字符串形式指定.例如:

         this.transform('rotateX(90deg)', this.$.myDiv);

*   [`translate3d(x, y, z, [node])`](/1.0/docs/api/Polymer.Base#method-translate3d). 转换指定结点或宿主组件(没有指定结点).例如:

        this.translate3d('100px', '100px', '100px');

### 引入和URLs

*   [`importHref(href, onload, onerror, optAsync)`](/1.0/docs/api/Polymer.Base#method-importHref). 动态引入一个HTML文档.

    ```
    this.importHref('path/to/page.html', function(e) {
        // e.target.import is the import document.
    }, function(e) {
        // loading error
    });
    ```

    **注意:** 在Polymer组件外调用`importHref`,需使用Polymer.Base.importHref`.
    { .alert .alert-info }

*   [`resolveUrl(url)`](/1.0/docs/api/Polymer.Base#method-resolveUrl). 将相对于已引入组件的`<dom-module>`相对URL转换为当前文档的相对路径. 例如该方法可被用于引用一个HTML Import之外的资源.
