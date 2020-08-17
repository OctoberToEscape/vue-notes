# vue 组件

## 什么是组件？

组件 (Component) 是 Vue.js 最强大的功能之一。组件可以扩展 HTML 元素，**封装可重用的代码**。在较高层面上，组件是自定义元素，Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以表现为用 is 特性进行了扩展的原生 HTML 元素，是在前端在单页面应用（SPA）上最好的一种实现方式，把所有**功能模块拆解成单独的组件**，每个组件都有**独立的作用域**，且还**可以相互通信**。

![img1.png](https://i.loli.net/2020/08/17/GvKlogTcXY67C8j.png)

## 认识单页面应用（SPA）

在传统的页面之间跳转，是通过刷新，重新渲染一个页面而实现，在渲染的过程中势必要加载外部资源文件，页面在服务器中渲染出来是通过一系列的生命周期，在这个过程中会因为网速等硬件问题直接影响页面的加载速度，为解决这一问题，前端在新的设计模式上引入了组件的概念，页面之间的跳转变成了组件之间的切换，不需要重新加载整个页面，也不用考虑页面的生命周期，换成组件的生命周期，在性能上大大的提升了。

## Vue 组件的实现

### 1.全局注册

```html
<div id="app">
    <!-- 组件的引用 -->
    <component-name />
</div>
```

```js
// 全局组件的声明  Vue.component(组件名称, {template})
Vue.component("component-name", {
    template: "<h1>全局组件</h1>",
});
```

最终渲染结果

```html
<div id="app">
    <h1>全局组件</h1>
</div>
```

---

2.局部注册

```html
<div id="element">
    <!-- 局部组件 -->
    <component-private />
</div>
```

```js
var ele = new Vue({
    el: "#element",
    //局部组件
    components: {
        "component-private": {
            template: "<h2>我是局部组件</h2>",
        },
    },
});
```

---

3.通过 `Vue.extend()`实现组件注册

```html
<div id="extend"></div>
```

```js
var ComponentExtend = Vue.extend({
    template: "<h3>{{Value}}</h3>",
    data() {
        return {
            first: "我是通过",
            function: "Vue.extend()",
            last: "实现的组件注册",
        };
    },
    computed: {
        Value() {
            return this.first + this.function + this.last;
        },
    },
});
new ComponentExtend().$mount("#extend");
```

最终渲染结果

```html
<h3>我是通过Vue.extend()实现的组件注册</h3>
```

上面又用到了实例方法 `vm.$mount()`，下面说明下它的使用方式。

---

## vm.\$mount( [elementOrSelector] )使用说明

参数：

-   `{Element | string} [elementOrSelector]`
-   `{boolean} [hydrating]`

返回值： `vue`实例自身

用法：如果 vue 在实例化的时候没有收到`el`选项，则他处于**未挂载**的状态，没有与任何 dom 有关联，可以使用 `vm.$mount()` 手动地挂载一个未挂载的实例。**_如果没有提供 elementOrSelector 参数，模板将被渲染为文档之外的的元素，并且你必须使用原生 DOM API 把它插入文档中。_**

```html
<div id="mount"></div>
```

```js
var Mount = Vue.extend({
    template: "<p>讲解vm.$mount()</p>",
});

new Mount().$mount("#mount"); //等同于下面的方法
// new Mount({ el: "#mount" });

// 或者在或者，在文档之外渲染并且随后挂载
var component = new Mount().$mount();
document.getElementById("mount").appendChild(component.$el);
```

---

## 组件是一个单独的作用域

每个组件都有自己独立的作用域

```html
<div id="alone">
    <p>{{count}}</p>
    <my-component />
</div>
```

```js
var alone = new Vue({
    el: "#alone",
    data() {
        return {
            count: 10,
        };
    },
    components: {
        "my-component": {
            template: '<button v-on:click="onClick">{{ count }}</button>',
            data() {
                return {
                    count: 0,
                };
            },
            methods: {
                onClick() {
                    this.count++;
                },
            },
        },
    },
});
```

渲染结果

```html
<div id="alone">
    <p>10</p>
    <!--
        下面button按钮每次点击都会自增 1，而 p 标签永远都是为 10
        原因为组件的作用域是单独的
    -->
    <button>0</button>
</div>
```

---

## 特殊的 HTML 标签中需要使用 is

像 `<ul>`、`<ol>`、`<table>`、`<select>` 这样的元素里允许包含的元素有限制，而另一些像 `<option>` 这样的元素只能出现在某些特定元素的内部。

```html
<div id="special">
    <select>
        <!-- 错误写法如下 -->

        <!-- 
            <privateOption />
            会被当作无效的东西过滤掉
         -->

        <!-- 正确写法 -->
        <option is="privateOption"></option>
    </select>
</div>
```

---

## 动态组件 - :is

某些场景下我们需要动态的切换不同的页面这时候，讲一个个需求封装成一个个组件，用动态组件就可以实现

```html
<div id="dynamic">
    <button @click="change">{{title}}</button>
    <br />
    <p :is="title"></p>
</div>
```

```js
//点击后根据变量的改变加载不同的组件
var dynamic = new Vue({
    el: "#dynamic",
    data: {
        title: "red",
    },
    methods: {
        change() {
            this.title = this.title == "red" ? "green" : "red";
        },
    },
    components: {
        red: {
            template: "<h1>Red</h1>",
        },
        green: {
            template: "<h2>Green</h2>",
        },
    },
});
```

---

## Prop 实现数据通信

在 Vue 中，父子组件的关系可以总结为 `prop` 向下传递，事件向上传递。父组件通过 `prop` 给子组件下发数据，子组件通过`事件`给父组件发送消息。
