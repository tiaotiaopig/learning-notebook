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
   
   # 另一种写法
   const vm = new Vue({
           el: '#hello-vue',
           data: {message: 'Hello Vue!!'},
           methods: {}
   });
   ```

## vue 指令

