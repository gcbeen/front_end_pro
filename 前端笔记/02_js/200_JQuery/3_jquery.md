## jQuery补充知识点

### 链式编程

> 通常情况下，只有设置操作才能把链式编程延续下去。
>
> 因为获取操作的时候，会返回获取到的相应的值，无法返回 jQuery对象。

```javascript
// 筛选选择器会改变jQuery对象的DOM对象，
// 想要回复到上一次的状态，并且返回匹配元素之前的状态。
end();
```



### each方法

> jQuery的隐式迭代会对所有的DOM对象设置相同的值，
>
> 但是如果我们需要给每一个对象设置不同的值的时候，就需要自己进行迭代了。

作用：遍历jQuery对象集合，为每个匹配的元素执行一个函数

```javascript
// 参数一表示当前元素在所有匹配元素中的索引号
// 参数二表示当前元素（DOM对象）
$(selector).each(function(index, element) {});
```



### 多库共存

jQuery使用作 为 标 示 符 ， 但 是 如 果 与 其 他 框 架 中 的 作为标示符，但是如果与其他框架中的作为标示符，但是如果与其他框架中的冲突时，jQuery可以释放$符的控制权.

```javascript
var c = $.noConflict(); //释放$的控制权,并且把$的能力给了c
```



## 插件

### 常用插件

> 插件：jquery不可能包含所有的功能，我们可以通过插件扩展jquery的功能。
>
> jQuery有着丰富的插件，使用这些插件能给jQuery提供一些额外的功能。

jquery.color.js

> animate不支持颜色的渐变，但是使用了jquery.color.js后，就可以支持颜色的渐变了。

使用插件的步骤

```javascript
//1. 引入jQuery文件
//2. 引入插件（如果有用到css的话，需要引入css）
//3. 使用插件
```

懒加载插件  jquery.lazyload.js

### jquery.ui.js插件

jQueryUI专指由jQuery官方维护的UI方向的插件。

官方API：http://api.jqueryui.com/category/all/

其他教程：[jQueryUI教程](http://www.runoob.com/jqueryui/jqueryui-tutorial.html)

基本使用:

```javascript
//1.	引入jQueryUI的样式文件
//2.	引入jQuery
//3.	引入jQueryUI的js文件
//4.	使用jQueryUI功能
```

使用jquery.ui.js手风琴菜单

## 制作jquery插件

> 原理：jquery插件其实说白了就是给jquery对象增加一个新的方法，让jquery对象拥有某一个功能。

```javascript
//通过给$.fn添加方法就能够扩展jquery对象
$.fn.pluginName = function() {};
```



