# vue 创建

## vue 的实例

每一个 Vue 应用都是通过 Vue 函数创建一个新的实例开始的

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
    </head>
    <body>
        <div id="app"></div>
    </body>
</html>
```

```js
var app = new Vue({
    // 选项
});
```

## 实例中的数据

```js
//生成一个新的vue实例
var app = new Vue({
    el: "#app", // 节点的名称
    data() {
        //数据
        return {
            message: "hello world",
            isOk: false,
            url: "./image/img1.jpg",
            html: "<p>大家好</p>",
        };
    },
});
```

## 模版语法

根据上面的代码,我们创建了一个 vue 的实例对象，并且有一条 data 数据是 message，我们在 vue 中将怎么讲数据放到节点中呢

### 插值

最常见的数据绑定就是文本插值，即双大括号

#### 文本

```html
<!-- 传统 -->
<span>消息:hello world</span>
<!-- vue -->
<span>消息:{{message}}</span>
```

上面代码中 无论何时，绑定的数据对象上 message property 发生了改变，插值处的内容都会更新。
但是传统的写法,就要通过 js 的 innerHTML 方法改变视图。

#### 使用 javascript 表达式

```html
<span>消息:{{message + "!!"}}</span>
<span>消息:{{isOk ? 'ok' : 'notgood'}}</span>
<span>消息:{{ message.split('').reverse().join('')}}</span>
<!-- 效果见下图 -->
```

![img1.jpg](https://i.loli.net/2020/08/12/oZ9ursQSqRCglvH.jpg)

#### 属性

属性 元素的任意属性（包含自定义属性）都可以和对象绑定 :属性名(或者 v-bind:属性名)="对象"

```html
<img :src="url" alt="" />
<!-- 等同于 -->
<img v-bind:src="url" alt="" />
```

#### 纯 html

纯 HTML {{}} 这种形式最终会被解释成文本，如果要想输入 HTML 结构，使用要用到 v-html="对象"

```html
<div v-html="html"></div>
```
