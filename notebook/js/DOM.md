# JavaScript HTML DOM

**通过 HTML DOM，可访问 JavaScript HTML 文档的所有元素。**

## HTML DOM （文档对象模型）

当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。

HTML DOM 模型被构造为对象的树。

### HTML DOM 树

通过可编程的对象模型，JavaScript 获得了足够的能力来创建动态的 HTML。

- JavaScript 能够改变页面中的所有 HTML 元素
- JavaScript 能够改变页面中的所有 HTML 属性
- JavaScript 能够改变页面中的所有 CSS 样式
- JavaScript 能够对页面中的所有事件做出反应

```js
document.getElementById(id); // 返回的是单个对象，id是唯一的
document.getElementByTagName('p'); // 返回的是对象数组，因为标签不唯一
document.getElementByClassName('f');
```



## 查找 HTML 元素

通常，通过 JavaScript，您需要操作 HTML 元素。

为了做到这件事情，您必须首先找到该元素。有三种方法来做这件事：

- 通过 id 找到 HTML 元素
- 通过标签名找到 HTML 元素
- 通过类名找到 HTML 元素

## 通过 id 查找 HTML 元素

在 DOM 中查找 HTML 元素的最简单的方法，是通过使用元素的 id。

### 实例

本例查找 id="intro" 元素：

```
var x=document.getElementById("intro");
```

<iframe src="http://jsrun.net/cwkKp/embedded/all/light" id="JSREMB_18791" width="100%" height="280" frameborder="0" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin" allow="microphone;camera;midi;encrypted-media;" __idm_frm__="482" style="text-rendering: optimizelegibility; box-sizing: border-box;"></iframe>

如果找到该元素，则该方法将以对象（在 x 中）的形式返回该元素。

如果未找到该元素，则 x 将包含 null。

## 通过标签名查找 HTML 元素

### 实例

本例查找 id="main" 的元素，然后查找 "main" 中的所有 <p> 元素：

```
var x=document.getElementById("main");
var y=x.getElementsByTagName("p");
```

<iframe src="http://jsrun.net/dwkKp/embedded/all/light" id="JSREMB_18791" width="100%" height="280" frameborder="0" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin" allow="microphone;camera;midi;encrypted-media;" __idm_frm__="483" style="text-rendering: optimizelegibility; box-sizing: border-box;"></iframe>

提示：通过类名查找 HTML 元素在 IE 5,6,7,8 中无效。