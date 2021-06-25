# Vue.js快速入门

## 简单介绍

> **Vue** 只关注视图，在MVVM (Model, View, Viewmodel) 中处于Viewmodel，实现了view（视图）和 model（数据）的分离。
>
> Vue.js 的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统
>
> 所有东西都是**响应式的**，当model 发生变化的时候，view 也会响应的更新，view 的更新也会反映到model上，数据实现双向绑定。
>
> 组件化应用构建，为了可复用，将页面拆分成一个个组件，然后按需动态地进行拼装

![Component Tree](https://cn.vuejs.org/images/components.png)

## 模板语法

1. 创建一个**dom**节点，供**Vue**渲染使用，本次使用的即是下面的**div**节点

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <meta http-equiv="X-UA-Compatible" content="ie=edge">
       <style type="text/css">
           .demo {
               font-family: sans-serif;
               border: 1px solid #eee;
               border-radius: 2px;
               padding: 20px 30px;
               margin-top: 1em;
               margin-bottom: 40px;
               user-select: none;
               overflow-x: auto;
           }
       </style>
       <title>HelloWorld</title>
   </head>
   
   <body>
       <div id="hello-vue" class="demo">
           {{ message }}
         </div>
   
       <script src="https://unpkg.com/vue@next"></script>  <!-- vue 路径-->
       <script>
        const HelloVueApp = {
           data() {
               return {
               message: 'Hello Vue!!'
               }
           }
        }
   
        Vue.createApp(HelloVueApp).mount('#hello-vue')
       </script>
   </body>
   
   </html>
   ```

2. 采用简洁的**模板语法**来声明式地将数据渲染进 DOM 的系统

   ```javascript
   # 使用vue
   const HelloVueApp = {
       data() {
           return {
               message: 'Hello Vue!!'
           }
       }
   }
   
   Vue.createApp(HelloVueApp).mount('#hello-vue')
   
   # 另一种写法(vue2.0)
   const vm = new Vue({
           el: '#hello-vue',
           data: {message: 'Hello Vue!!'},
           methods: {}
   });
   ```

## vue 指令

1. `v-bind` attribute 被称为**指令**。指令带有前缀 `v-`，以表示它们是 Vue 提供的特殊 attribute,它们会在渲染的 DOM 上应用特殊的响应式行为.

    ```vue
    <div id="app-2">
      <span v-bind:title="message">
        鼠标悬停几秒钟查看此处动态绑定的提示信息！
      </span>
    </div>
    ```

    `v-bind:title='message'`详解:

    `v-bind`vue指令,用于将vue实例的数据,绑定到dom的属性中去

    `:title`被绑定的属性

    `='message'`vue实例的数据项的key,(一般在data属性或者data()函数中返回)

2. `v-if`,为真显示dom,为假则不显示,控制切换一个元素

    ```vue
    <div id="app-3">
      <p v-if="seen">现在你看到我了</p>
    </div>
    ```

    这个例子演示了我们不仅可以把数据绑定到 DOM 文本或 attribute，还可以绑定到 DOM **结构**。此外，Vue 也提供一个强大的过渡效果系统，可以在 Vue 插入/更新/移除元素时自动应用[过渡效果](https://cn.vuejs.org/v2/guide/transitions.html)。

3. `v-for` 指令可以绑定数组的数据来渲染一个项目列表

    ```vue
    <div id="app-4">
      <ol>
        <li v-for="todo in todos">
          {{ todo.text }}
        </li>
      </ol>
    </div>
    
    var app4 = new Vue({
      el: '#app-4',
      data: {
        todos: [
          { text: '学习 JavaScript' },
          { text: '学习 Vue' },
          { text: '整个牛项目' }
        ]
      }
    })
    ```

4. `v-on` 指令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法

    ```vue
    <div id="app-5">
      <p>{{ message }}</p>
      <button v-on:click="reverseMessage">反转消息</button>
    </div>
    
    var app5 = new Vue({
      el: '#app-5',
      data: {
        message: 'Hello Vue.js!'
      },
      methods: {
        reverseMessage: function () {
          this.message = this.message.split('').reverse().join('')
        }
      }
    })
    ```

    `v-on:click="reverseMessage"`把click事件绑定到响应函数上

    注意在 `reverseMessage` 方法中，我们更新了应用的状态，但没有触碰 DOM——所有的 DOM 操作都由 Vue 来处理，你编写的代码只需要关注逻辑层面即可。

5. `v-model` 指令，它能轻松实现表单输入和应用状态之间的双向绑定

    ```vue
    <div id="app-6">
      <p>{{ message }}</p>
      <input v-model="message">
    </div>
    
    <script>
    	var app6 = new Vue({
      el: '#app-6',
      data: {
        message: 'Hello Vue!'
      }
    })
    </script>
    ```

    
