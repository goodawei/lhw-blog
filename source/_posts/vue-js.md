---
title: 非常coll的框架 vue-js
date: 2016-11-22 21:49:03
tags: vue
categories: vue
---

### 数据双向绑定

v-model 使用于 `input` ,`textarea` 将属性和我们的 `data`对象中的属性绑定在一起

```javascript
<!DOCTYPE html>
<html>
    <head>
        <title>vue-js</title>
        <script type="text/javascript" src="./node_modules/vue/dist/vue.js"></script>
    </head>
    <body>
        <div id="app">
            <h1>{{ message }}</h1>

            <input type="text" v-model="message">

            <pre>
                {{ $data | json }}
            </pre>
        </div>
    </body>
    <script>
        new Vue({
            el:'#app',

            data:{
                message:'hello world !!'
            }
        })
    </script>
</html>

```
<!-- more -->

### v-show 和 v-if

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue-js</title>
    <style>
        #main{border: 1px;width:800px;margin:100px auto;}
        .name{width:600px;height: 40px}
        .passwd{width:600px;height: 50px}
        .name label{display:inline-block;width: 60px}
        .passwd label{display:inline-block;width: 60px}
        .error{color:red}
    </style>
</head>
<!--v-show 和 v-if 的使用-->
<body>
    <div id="main">
        <div id="app">
            <form action="">
                <div class="name">
                    <label>用户名:</label>
                    <input type="text" name="" value="" v-model="username">
                    <span class="error" v-show="!username">请输入用户名</span>
                </div>
                <div class="passwd">
                    <label>密码:</label>
                    <input type="password" name="" value="" v-model="password">
                    <span class="error" v-show="!password">请输入密码</span>
                </div>
                <button v-show="username && password">登录</button>
            </form>
        </div>
    </div>
</body>
<script type="text/javascript" src="./node_modules/vue/dist/vue.js"></script>
<script>
    new Vue({
        el:'#app',
        data:{
            username : '',
            password : ''
        }
    })
</script>
</html>


```