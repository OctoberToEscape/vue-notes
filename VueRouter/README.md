# vue 路由

通过 URL 映射到对应的功能实现，Vue 的路由使用要先引入 vue-router.js,传统的页面跳转会刷新页面，但是路由跳转不会造成页面的刷新

## 引入相关的路由 js

```html
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
```

## 定义 view 即路由组件

```html
<div id="app">
    <p>
        <!-- 使用 router-link 组件来导航. -->
        <!-- 通过传入 `to` 属性指定链接. -->
        <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
        <router-link to="/">主页</router-link>
        <router-link to="/about">关于我们</router-link>
        <router-link to="/mine">我的</router-link>

        <!-- 编程式导航 -->
    </p>
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view style="margin-top: 20px;"></router-view>
</div>
```

```js
// 定义组件
var Main = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}</span>
        </div>`,
    data() {
        return {
            title: "主页",
        };
    },
});
var About = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}</span>
        </div>`,
    data() {
        return {
            title: "关于我们",
        };
    },
});
var Mine = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}</span>
        </div>`,
    data() {
        return {
            title: "我的",
        };
    },
});

// 声明路由规则
const routes = [
    { path: "/", component: Main },
    { path: "/about", component: About },
    { path: "/mine", component: Mine },
];

// 创建VueRouter实例
const router = new VueRouter({
    routes,
});

// 将创建的VueRouter实例传入创建的Vue实例中
var app = new Vue({
    router,
}).$mount("#app");
```

## 路由参数

### params 传参数

```html
<router-link to="/about/1">关于我们</router-link>
```

```js
var About = Vue.extend({
    // 利用$route.params.xxx拿到参数
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}{{$route.params.id}}</span>
        </div>`,
    data() {
        return {
            title: "关于我们",
        };
    },
});

const routes = [
    //这个路由要加上：xxx
    { path: "/about/:id", component: About },
];
```

![img1.jpeg](https://i.loli.net/2020/08/18/fhxX7EoWtBMAs5N.jpg)

### query 传参数

```html
<router-link :to="{path:'/mine',query:{name:'heqi',age:22}}">我的</router-link>
```

```js
// 通过$route.query.xxx拿到参数
var Mine = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}{{$route.query.name}}{{$route.query.age}}</span>
        </div>`,
    data() {
        return {
            title: "我的",
        };
    },
});

// 这里不需要变更
const routes = [{ path: "/mine", component: About }];
```

![img2.jpeg](https://i.loli.net/2020/08/18/ykIBbGw6WsNHLed.jpg)

### params 与 query 的区别

-   `刷新页面`

    params 传递的参数**会**消失

    query 传递的参数**不会**消失

-   `显示`

    params 相当于 post 请求，参数**不会**拼接到链接后方

    query 相当于 get 请求，参数**会**拼接到链接后方

-   `router 配置`

    params **需要**在 router 内进行参数配置

    query **不需要**

---

## 编程式导航

```html
<!-- 编程式导航 -->
<span @click="onClick">编程式跳转</span>
```

```js
var JavaScript = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}{{$route.query.name}}</span>
        </div>`,
    data() {
        return {
            title: "编程式导航",
        };
    },
});
const routes = [{ path: "/js", component: JavaScript }];

var app = new Vue({
    router,
    methods: {
        onClick() {
            this.$router.push({
                path: "/js",
                query: {
                    name: "javascript",
                },
            });
        },
    },
}).$mount("#app");
```

跳转如下图

![img3.jpeg](https://i.loli.net/2020/08/18/hqI2zNrjSD6HCde.jpg)

编程式导航的几种方式

-   `this.$router.push()` 方法其实和`<router-link :to="...">`是等同的。

-   `this.$router.go(数值)` 常用于前进后退

-   `this.$router.replace()` `push` 方法会向 history 栈添加一个新的记录，而 `replace`方法是替换当前的页面，常用于做 404 页面 或者 token 实效替换到登录页面
