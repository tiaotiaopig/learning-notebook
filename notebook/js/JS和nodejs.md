# JS 和 nodejs

> 这里把运行在浏览器端的JavaScript称为JS,运行在服务器端（nodejs）的JavaScript称为nodejs
>
> 用于两者的的宿主环境不同，所以会有一些差异：
>
> 1. 在JS中的全局对象是`window`,所有全局变量和函数都是`window`对象的属性；window还有一些浏览器相关的操作。
> 2. 在nodejs中的全局对象是`global`，所有全局变量和函数都是`global`对象的属性；
> 3. nodejs中每个文件都是一个模块作用域，文件中定义的“全局变量”其实是在模块作用域下，还是局部的，无法通过`global`对象引用；**每个模块都有自己的作用域，每一个模块内声明的变量都是局部变量， 不会污染全局作用域；**
> 4. JS中每个文件都是在全局作用域下，文件中的“全局变量”是在全局作用域下，可以通过`window`对象引用，存在全局污染。

## nodejs全局对象

> 在浏览器 `JavaScript` 中，通常`window` 是全局对象， 而 `Nodejs`中的全局对象是 `global`
>
> 在`NodeJS`里，是不可能在最外层定义一个变量，因为所有的用户代码都是当前模块的，只在当前模块里可用，但可以通过`exports`对象的使用将其传递给模块外部
>
> 所以，在`NodeJS`中，用`var`声明的变量并不属于全局的变量，只在当前模块生效
>
> 像上述的`global`全局对象则在全局作用域中，任何全局变量、函数、对象都是该对象的一个属性值
>
> Node. js 将全局对象分成两类：真正的全局对象；模块级别的全局对象
> 真正的全局对象：常见的全局对象有global、console、process、[Buffer](https://so.csdn.net/so/search?q=Buffer&spm=1001.2101.3001.7020)等等
>
> 模块级别的全局对象：require、module、[exports](https://so.csdn.net/so/search?q=exports&spm=1001.2101.3001.7020)、module.exports、__dirname、__filename、Timer(clearInterval、setInterval、clearTimeout、setTimeout)等等