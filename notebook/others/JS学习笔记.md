# JS学习笔记

## 基础语法

### 基础入门

~~~html
<html>
<head>
  <script>
    alert('Hello, world');
  </script>
</head>
<body>
  ...
</body>
</html>
~~~

> 由`<script>...</script>` 包裹的代码就是js代码，可以直接被浏览器执行
>
> 浏览器都会有执行js代码的js引擎，类似于java虚拟机
>
> node.js 也是一种js运行时环境，可以独立运行js代码，node.js可以用来做后端服务



我们也可以把JavaScript代码放到一个单独的`.js`文件，然后在HTML中通过`<script src="..."></script>`引入这个文件：

~~~html
<html>
<head>
  <script src="/static/js/abc.js"></script>
</head>
<body>
  ...
</body>
</html>
~~~

这样，`/static/js/abc.js`就会被浏览器执行。

把JavaScript代码放入一个单独的`.js`文件中更利于维护代码，并且多个页面可以各自引用同一份`.js`文件。

可以在同一个页面中引入多个`.js`文件，还可以在页面中多次编写`<script> js代码... </script>`，浏览器按照顺序依次执行。



有些时候你会看到`<script>`标签还设置了一个`type`属性：

```html
<script type="text/javascript">
  ...
</script>
```

但这是**没有必要**的，因为默认的`type`就是JavaScript，所以不必显式地把`type`指定为JavaScript。

### 浏览器调试

> 打开浏览器，按**F12** 进入开发者模式，里面很多功能对我们开发前端很有用
>
> 了解一个简单函数，`console.log();` 控制台打印信息

### 基础语法

1. 语法和Java非常类似，**分号结尾**，语句块用 `{...}` 包裹
2. 单行注释：//      多行注释：`/*...*/`

#### 数据类型

+ **Number**

  JavaScript不区分整数和浮点数，统一用Number表示，以下都是合法的Number类型：

  ```javascript
  123; // 整数123
  0.456; // 浮点数0.456
  1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
  -99; // 负数
  NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
  Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
  ```

  计算机由于使用二进制，所以，有时候用十六进制表示整数比较方便，十六进制用0x前缀和0-9，a-f表示，例如：`0xff00`，`0xa5b4c3d2`，等等，它们和十进制表示的数值完全一样。

  Number可以直接做四则运算，规则和数学一致：

  ```javascript
  1 + 2; // 3
  (1 + 2) * 5 / 2; // 7.5
  2 / 0; // Infinity
  0 / 0; // NaN
  10 % 3; // 1
  10.5 % 3; // 1.5
  ```

  注意`%`是求余运算。

  

+ **字符串**

  字符串是以单引号'或双引号"括起来的任意文本，比如`'abc'`，`"xyz"`等等。请注意，`''`或`""`本身只是一种表示方式，不是字符串的一部分，因此，字符串`'abc'`只有`a`，`b`，`c`这3个字符。

  转义字符`\`可以转义很多字符，比如`\n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\`。

  ASCII字符可以以`\x##`形式的十六进制表示，例如：

  ```
  '\x41'; // 完全等同于 'A'
  ```

  还可以用`\u####`表示一个Unicode字符：

  ```
  '\u4e2d\u6587'; // 完全等同于 '中文'
  ```

  由于多行字符串用`\n`写起来比较费事，所以最新的ES6标准新增了一种多行字符串的表示方法，用反引号 *`\* ... \*`* 表示：

  ```
  `这是一个
  多行
  字符串`;
  ```

  如果有很多变量需要连接，用`+`号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量：

  ```
  var name = '小明';
  var age = 20;
  var message = `你好, ${name}, 你今年${age}岁了!`;	
  alert(message);
  ```

  *需要特别注意的是*，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果：

  ```
  var s = 'Test';
  s[0] = 'X';
  alert(s); // s仍然为'Test'
  ```

  JavaScript为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串

  

+ **布尔值**

  布尔值和布尔代数的表示完全一致，一个布尔值只有`true`、`false`两种值，要么是`true`，要么是`false`，可以直接用`true`、`false`表示布尔值，也可以通过布尔运算计算出来。

  与或非逻辑运算 `&&`, `||`, `!`

  比较运算符，`===` 判断等于，`!==` 判断不等于，`==`在做判断时，会自动转化类型，再进行判断，不推荐

  唯一能判断`NaN`的方法是通过`isNaN()`函数：

  ```javascript
  isNaN(NaN); // true
  ```

+ #### null 和 undefined

  **null表示"没有对象"，即该处不应该有值。**典型用法是：

  > （1） 作为函数的参数，表示该函数的参数不是对象。
  >
  > （2） 作为对象原型链的终点。

  > ```javascript
  > Object.getPrototypeOf(Object.prototype)
  > // null
  > ```

  **undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。**典型用法是：

  > （1）变量被声明了，但没有赋值时，就等于undefined。
  >
  > （2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
  >
  > （3）对象没有赋值的属性，该属性的值为undefined。
  >
  > （4）函数没有返回值时，默认返回undefined。

  > ```javascript
  > var i;
  > i // undefined
  > 
  > function f(x){console.log(x)}
  > f() // undefined
  > 
  > var  o = new Object();
  > o.p // undefined
  > 
  > var x = f();
  > x // undefined
  > ```

+ **数组**

  数组是一组按顺序排列的集合，集合的每个值称为元素。JavaScript的数组可以包括任意数据类型。例如：

  ```
  [1, 2, 3.14, 'Hello', null, true];
  ```

  上述数组包含6个元素。数组用`[]`表示，元素之间用`,`分隔。

  另一种创建数组的方法是通过`Array()`函数实现：

  ```
  new Array(1, 2, 3); // 创建了数组[1, 2, 3]
  ```

  然而，出于代码的可读性考虑，**强烈建议直接使用`[]`**。

  数组的元素可以通过索引来访问。请注意，索引的起始值为`0`：

  ```javascript
  var arr = [1, 2, 3.14, 'Hello', null, true];
  arr[0]; // 返回索引为0的元素，即1
  arr[5]; // 返回索引为5的元素，即true
  arr[6]; // 索引超出了范围，返回undefined
  ```

+ #### 对象

  JavaScript的对象是一组由键-值组成的无序集合，例如：

  ```javascript
  var person = {
      name: 'Bob',
      age: 20,
      tags: ['js', 'web', 'mobile'],
      city: 'Beijing',
      hasCar: true,
      zipcode: null
  };
  ```

  JavaScript对象的键都是字符串类型，值可以是任意数据类型。上述`person`对象一共定义了6个键值对，其中每个键又称为对象的属性，例如，`person`的`name`属性为`'Bob'`，`zipcode`属性为`null`。

  要获取一个对象的属性，我们用`对象变量.属性名`的方式：

  ```javascript
  person.name; // 'Bob'
  person.zipcode; // null
  ```


#### **变量**

变量的概念基本上和初中代数的方程变量是一致的，只是在计算机程序中，变量不仅可以是数字，还可以是任意数据类型。

变量在JavaScript中就是用一个变量名表示，变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。申明一个变量用`var`语句，比如：

```js
var a; // 申明了变量a，此时a的值为undefined
var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1
var s_007 = '007'; // s_007是一个字符串
var Answer = true; // Answer是一个布尔值true
var t = null; // t的值是null
```

变量名也可以用中文，但是，请不要给自己找麻烦。

在JavaScript中，使用等号`=`对变量进行赋值。可以把任意数据类型赋值给变量，**同一个变量可以反复赋值，而且可以是不同类型的变量**，但是要注意只能用`var`申明一次，例如：

```javascript
var a = 123; // a的值是整数123
a = 'ABC'; // a变为字符串
```

这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言，赋值语句如下：

```js
int a = 123; // a是整数类型变量，类型用int申明
a = "ABC"; // 错误：不能把字符串赋给整型变量
```

和静态语言相比，动态语言更灵活，就是这个原因。

要显示变量的内容，可以用`console.log(x)`，打开Chrome的控制台就可以看到结果

#### Strict模式

JavaScript在设计之初，为了方便初学者学习，并不强制要求用`var`申明变量。这个设计错误带来了严重的后果：**如果一个变量没有通过`var`申明就被使用，那么该变量就自动被申明为全局变量**：

```
i = 10; // i现在是全局变量
```

在同一个页面的不同的JavaScript文件中，如果都不用`var`申明，恰好都使用了变量`i`，将造成变量`i`互相影响，产生难以调试的错误结果。

使用`var`申明的变量则不是全局变量，它的范围被限制在该变量被申明的函数体内，同名变量在不同的函数体内互不冲突。

为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了strict模式，在strict模式下运行的JavaScript代码，强制通过`var`申明变量，未使用`var`申明变量就使用的，将导致运行错误。

启用strict模式的方法是在JavaScript代码的第一行写上：

```
'use strict';
```

这是一个字符串，不支持strict模式的浏览器会把它当做一个字符串语句执行，支持strict模式的浏览器将开启strict模式运行JavaScript。

#### 数组

JavaScript的`Array`可以包含任意数据类型，并通过索引来访问每个元素。

要取得`Array`的长度，直接访问`length`属性：

```javascript
var arr = [1, 2, 3.14, 'Hello', null, true];
arr.length; // 6
```

*请注意*，直接给`Array`的`length`赋一个新的值会导致`Array`大小的变化：

```javascript
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```

`Array`可以通过索引把对应的元素修改为新的值，因此，对`Array`的索引进行赋值会直接修改这个`Array`：

```javascript
var arr = ['A', 'B', 'C'];
arr[1] = 99;
arr; // arr现在变为['A', 99, 'C']
```

*请注意*，如果通过索引赋值时，索引超过了范围，同样会引起`Array`大小的变化：

```javascript
var arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```

大多数其他编程语言不允许直接改变数组的大小，越界访问索引会报错。然而，JavaScript的`Array`却不会有任何错误。在编写代码时，不建议直接修改`Array`的大小，访问索引时要确保索引不会越界。

#### 对象

JavaScript的对象是一种无序的集合数据类型，它由若干键值对组成。

JavaScript的对象用于描述现实世界中的某个对象。例如，为了描述“小明”这个淘气的小朋友，我们可以用若干键值对来描述他：

```
var xiaoming = {
    name: '小明',
    birth: 1990,
    school: 'No.1 Middle School',
    height: 1.70,
    weight: 65,
    score: null
};
```

<font color=red>JavaScript用一个`{...}`表示一个对象，键值对以`xxx: xxx`形式申明，用`,`隔开。</font>注意，最后一个键值对不需要在末尾加`,`，如果加了，有的浏览器（如低版本的IE）将报错。

上述对象申明了一个`name`属性，值是`'小明'`，`birth`属性，值是`1990`，以及其他一些属性。最后，把这个对象赋值给变量`xiaoming`后，就可以通过变量`xiaoming`来获取小明的属性了：

```
xiaoming.name; // '小明'
xiaoming.birth; // 1990
```

<font color= red>访问属性是通过`.`操作符完成的，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须用`''`括起来：</font>

```
var xiaohong = {
    name: '小红',
    'middle-school': 'No.1 Middle School'
};
```

`xiaohong`的属性名`middle-school`不是一个有效的变量，就需要用`''`括起来。访问这个属性也无法使用`.`操作符，必须用`['xxx']`来访问：

```
xiaohong['middle-school']; // 'No.1 Middle School'
xiaohong['name']; // '小红'
xiaohong.name; // '小红'
```

也可以用`xiaohong['name']`来访问`xiaohong`的`name`属性，不过`xiaohong.name`的写法更简洁。我们在编写JavaScript代码的时候，属性名尽量使用标准的变量名，这样就可以直接通过`object.prop`的形式访问一个属性了。

实际上**JavaScript对象的所有属性都是字符串，不过属性对应的值可以是任意数据类型**。

如果访问一个不存在的属性会返回什么呢？JavaScript规定，访问不存在的属性不报错，而是返回`undefined`

#### Map

`Map`是一组键值对的结构，具有极快的查找速度。

举个例子，假设要根据同学的名字查找对应的成绩，如果用`Array`实现，需要两个`Array`：

```
var names = ['Michael', 'Bob', 'Tracy'];
var scores = [95, 75, 85];
```

给定一个名字，要查找对应的成绩，就先要在names中找到对应的位置，再从scores取出对应的成绩，Array越长，耗时越长。

如果用Map实现，只需要一个“名字”-“成绩”的对照表，直接根据名字查找成绩，无论这个表有多大，查找速度都不会变慢。用JavaScript写一个Map如下：

```javascript
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95
```

初始化`Map`需要一个二维数组，或者直接初始化一个空`Map`。`Map`具有以下方法：

```javascript
var m = new Map(); // 空Map
m.set('Adam', 67); // 添加新的key-value
m.set('Bob', 59);
m.has('Adam'); // 是否存在key 'Adam': true
m.get('Adam'); // 67
m.delete('Adam'); // 删除key 'Adam'
m.get('Adam'); // undefined
```

由于一个key只能对应一个value，所以，多次对一个key放入value，后面的值会把前面的值冲掉：

```
var m = new Map();
m.set('Adam', 67);
m.set('Adam', 88);
m.get('Adam'); // 88
```

#### Set

`Set`和`Map`类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在`Set`中，没有重复的key。

要创建一个`Set`，需要提供一个`Array`作为输入，或者直接创建一个空`Set`：

```
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```

重复元素在`Set`中自动被过滤：

```
var s = new Set([1, 2, 3, 3, '3']);
s; // Set {1, 2, 3, "3"}
```

注意数字`3`和字符串`'3'`是不同的元素。

通过`add(key)`方法可以添加元素到`Set`中，可以重复添加，但不会有效果：

```
s.add(4);
s; // Set {1, 2, 3, 4}
s.add(4);
s; // 仍然是 Set {1, 2, 3, 4}
```

通过`delete(key)`方法可以删除元素：

```
var s = new Set([1, 2, 3]);
s; // Set {1, 2, 3}
s.delete(3);
s; // Set {1, 2}
```

#### iterable

遍历`Array`可以采用下标循环，遍历`Map`和`Set`就无法使用下标。为了统一集合类型，ES6标准引入了新的`iterable`类型，`Array`、`Map`和`Set`都属于`iterable`类型。

具有`iterable`类型的集合可以通过新的`for ... of`循环来遍历。

你可能会有疑问，`for ... of`循环和`for ... in`循环有何区别？

`for ... in`循环由于历史遗留问题，它**遍历的实际上是对象的属性名称**。一个`Array`数组实际上也是一个对象，它的每个元素的索引被视为一个属性。

然而，更好的方式是直接使用`iterable`内置的`forEach`方法，它接收一个函数，每次迭代就自动回调该函数。以`Array`为例：

```javascript
'use strict'; 
var a = ['A', 'B', 'C'];
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(element + ', index = ' + index);
});
```

### 函数

#### 函数定义

在JavaScript中，定义函数的方式如下：

```
function abs(x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```

上述`abs()`函数的定义如下：

- `function`指出这是一个函数定义；
- `abs`是函数的名称；
- `(x)`括号内列出函数的参数，多个参数以`,`分隔；
- `{ ... }`之间的代码是函数体，可以包含若干语句，甚至可以没有任何语句。

请注意，函数体内部的语句在执行时，一旦执行到`return`时，函数就执行完毕，并将结果返回。因此，函数内部通过条件判断和循环可以实现非常复杂的逻辑。

如果没有`return`语句，函数执行完毕后也会返回结果，只是结果为`undefined`。

由于JavaScript的函数也是一个对象，上述定义的`abs()`函数实际上是一个函数对象，而函数名`abs`可以视为指向该函数的变量。

因此，第二种定义函数的方式如下：

```
var abs = function (x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
};
```

在这种方式下，`function (x) { ... }`是一个匿名函数，它没有函数名。但是，这个匿名函数赋值给了变量`abs`，所以，通过变量`abs`就可以调用该函数。

上述两种定义*完全等价*，注意第二种方式按照完整语法需要在函数体末尾加一个`;`，表示赋值语句结束。

#### 调用函数

调用函数时，按顺序传入参数即可：

```
abs(10); // 返回10
abs(-9); // 返回9
```

由于JavaScript允许传入任意个参数而不影响调用，因此传入的参数比定义的参数多也没有问题，虽然函数内部并不需要这些参数：

```
abs(10, 'blablabla'); // 返回10
abs(-9, 'haha', 'hehe', null); // 返回9
```

传入的参数比定义的少也没有问题：

```
abs(); // 返回NaN
```

此时`abs(x)`函数的参数`x`将收到`undefined`，计算结果为`NaN`。

要避免收到`undefined`，可以对参数进行检查：

```
function abs(x) {
    if (typeof x !== 'number') {
        throw 'Not a number';
    }
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```

#### arguments

JavaScript还有一个免费赠送的关键字`arguments`，它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。`arguments`类似`Array`但它不是一个`Array`：

~~~javascript
function foo(x) {
    console.log('x = ' + x); // 10
    for (var i=0; i<arguments.length; i++) {
        console.log('arg ' + i + ' = ' + arguments[i]); // 10, 20, 30
    }
}
foo(10, 20, 30);
~~~

#### rest

由于JavaScript函数允许接收任意个参数，于是我们就不得不用`arguments`来获取所有参数：

```
function foo(a, b) {
    var i, rest = [];
    if (arguments.length > 2) {
        for (i = 2; i<arguments.length; i++) {
            rest.push(arguments[i]);
        }
    }
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}
```

为了获取除了已定义参数`a`、`b`之外的参数，我们不得不用`arguments`，并且循环要从索引`2`开始以便排除前两个参数，这种写法很别扭，只是为了获得额外的`rest`参数，有没有更好的方法？

ES6标准引入了rest参数，上面的函数可以改写为：

```javascript
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

rest参数只能写在最后，前面用`...`标识，从运行结果可知，传入的参数先绑定`a`、`b`，多余的参数以数组形式交给变量`rest`，所以，不再需要`arguments`我们就获取了全部参数。

如果传入的参数连正常定义的参数都没填满，也不要紧，rest参数会接收一个空数组（注意不是`undefined`）。