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

---

## 路由命名

在路由映射表中添加属性 name，用以对该路由映射规则命名，在编程式导航跳转路由时可以用 router.push({name: '名称'})

```html
<!-- 命名路由 -->
<span @click="_onClick">命名路由跳转</span>
```

```js
var routerName = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}</span>
        </div>`,
    data() {
        return {
            title: "命名路由跳转",
        };
    },
});

// 这里要加一个name属性代表他的命名
const routes = [{ path: "/Name", component: routerName, name: "routerName" }];

//跳转
var app = new Vue({
    router,
    methods: {
        _onClick() {
            this.$router.push({
                name: "routerName",
            });
        },
    },
}).$mount("#app");
```

## 重复点击相同路由报错

我们在当前路由的时候，**用编程式路由跳转**又点击了当前路由会造成如下方图片的报错,虽然不会对逻辑造成错误，但是影响美观

![img4.png](https://i.loli.net/2020/08/19/W7rimdR3HcwpgfU.png)

解决方案，加上下方代码就不会出现此类问题

```js
const originalPush = VueRouter.prototype.push;
VueRouter.prototype.push = function push(location) {
    return originalPush.call(this, location).catch((err) => err);
};
```

## 路由的模式

vue 路由有两种模式，分别是 hash 和 history

### Hash 模式

vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。 hash（#）是 URL 的锚点，代表的是网页中的一个位置，单单改变#后的部分，浏览器只会滚动到相应位置，不会重新加载网页，也就是说 hash 出现在 URL 中，但不会被包含在 http 请求中，对后端完全没有影响，因此改变 hash 不会重新加载页面；同时每一次改变#后的部分，都会在浏览器的访问历史中增加一个记录，使用”后退”按钮，就可以回到上一个位置；所以说 Hash 模式通过锚点值的改变，根据不同的值，渲染指定 DOM 位置的不同数据。hash 模式的原理是 onhashchange 事件(监测 hash 值变化)，可以在 window 对象上监听这个事件。链接会有#号跟随最后

![img5.jpg](https://i.loli.net/2020/08/19/WEP2idjFUw1XDfg.jpg)

### History 模式

由于 hash 模式会在 url 中自带#，如果不想要很丑的 hash，我们可以用路由的 history 模式，只需要在配置路由规则时，加入"mode: 'history'",这种模式充分利用了 html5 history interface 中新增的 pushState() 和 replaceState() 方法。这两个方法应用于浏览器记录栈，在当前已有的 back、forward、go 基础之上，它们提供了对历史记录修改的功能。只是当它们执行修改时，虽然改变了当前的 URL ，但浏览器不会立即向后端发送请求。

设置方法

```js
const router = new VueRouter({
    mode: "history",
    routes,
});
```

---

## $router 和 $route 的区别

我们先打印一下这两个东西分别是什么

-   `$router`

    ![img6.jpg](https://i.loli.net/2020/08/19/AgEmt7L8YUcehW2.jpg)

-   `$route`

    ![img7.jpg](https://i.loli.net/2020/08/19/2F69X3acYuNpLPl.jpg)

我们可以看到

**`$route` 是“路由信息对象”，包括 path，params，hash，query，fullPath，matched，name 等路由信息参数。**

-   `$route.path`

    字符串，对应当前路由的路径，总是解析为绝对路径，如 "/about"。

-   `$route.params`

    一个 key/value 对象，包含了 动态片段 和 全匹配片段，如果没有路由参数，就是一个空对象。

-   `$route.query`

    一个 key/value 对象，表示 URL 查询参数。

    例如，对于路径 /user?id=1，则有 `$route.query.id` 为 1，

    如果没有查询参数，则是个空对象。

-   `$route.hash`

    当前路由的 hash 值 (不带 #) ，如果没有 hash 值，则为空字符串。

-   `$route.fullPath`

    完成解析后的 URL，包含查询参数和 hash 的完整路径。

-   `$route.matched`

    数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。

-   `$route.name`

    当前路径名字

**`$router` 是“路由实例”对象，即使用 new VueRouter 创建的实例，包括了路由的跳转方法，钩子函数等。**

---

## 多层嵌套路由

多级路由嵌套 router 用到 `children` 字段，html 布局用到`<<router-view></router-view>>`

```html
<!-- 多层嵌套路由 -->
<router-link to="/floor1">我在第一层</router-link>
```

```js
//第一层 <router-view></router-view>
var floor1 = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}</span>
            <router-link to="/floor1/floor2">二楼</router-link>
            <router-view></router-view>
        </div>`,
    data() {
        return {
            title: "一楼",
        };
    },
});

var floor2 = Vue.extend({
    template: `<div style="width:100px;height:50px;">
            <span>{{title}}</span>
        </div>`,
    data() {
        return {
            title: "二楼",
        };
    },
});

const routes = [
    {
        path: "/floor1",
        component: floor1,
        children: [
            // floor2 会被渲染在 floor1 的 <router-view> 中
            {
                //这里的path不加 '/'
                path: "floor2",
                component: floor2,
            },
        ],
    },
];

var app = new Vue({
    router,
}).$mount("#app");
```

---

## 路由的重定向

在定义路由的时候，可以通过添加`redirect`参数，重定向到另外一个页面。应用场景：比如一个二级路由，你点击以及路由的时候你要默认展示二级路由的首页

```js
var router = new VueRouter({
    routes: [
        {
            path: "/student",
            component: study,
            //利用redirect重定向，进到student路由就重定向到了student下的index路由
            redirect: "/student/index",
            children: [
                {
                    path: "index",
                    component: index,
                    name: "student",
                },
            ],
        },
    ],
});
```

---

## 路由的别名

在定义路由的时候，可以通过添加`alias`参数，表示该 url 的别名，以后也可以通过这个别名来访问到这个组件。

```js
var router = new VueRouter({
    routes: [
        {
            path: "/",
            component: home,
            alias: "/home",
        },
    ],
});

//跳转路由的时候

this.$router.push({ path: "/" }); //等同于下面的
this.$router.push({ path: "/home" });
```

---

## 路由守卫

**_类别_**

-   全局守卫：`beforeEach`

-   后置守卫：`afterEach`

-   全局解析守卫：`beforeResolve`

-   路由独享守卫：`beforeEnter`

-   组件内路由守卫：

    `beforeRouteEnter`

    `beforeRouteUpdate`

    `beforeRouteLeave`

<br />

**_路由导航解析流程:_**

路由导航开始

在当前路由调用 `beforeRouterLeave`

调用 `beforeEach`

在重用组件中调用 `beforeRouteUpdate`

调用 `beforeEnter`

解析异步路由组件

在被激活的路由组件里调用 `beforeRouteEnter`

调用 `beforeResolve`

导航被确认

调用全局的 `afterEach`

触发 DOM 更新

用创建好的实例调用 `beforeRouteEnter` 中传递给 `next` 的函数

<br />

### 全局守卫 `beforeEach`

常用于全局的 token 登录判断，比如在一个后台管理系统中，token 实效了，还有权限问题会常用到

```js
router.beforeEach((to, from, next) => {
    // doing
});
```

**_参数解析_**

-   to: 即将要**进入**的目标路由对象

-   from: 当前导航正要**离开**的路由

-   next:: **一定要调用该方法**来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

-   next():直接进 to 所指路由

-   next(false) :中断当前路由

-   next('routename') :跳转指定路由

-   next('error') :跳转错误路由

<br />

给大家写一个全局判断 token 的例子：

```js
router.beforeEach((to, from, next) => {
    if (
        //如果我要去登录，注册，修改密码，服务协议等页面
        to.path === "/login" ||
        to.name === "register" ||
        to.name === "changePassword" ||
        to.name === "clause"
    ) {
        // 通行让他去
        next();
    } else {
        // 否则其他页面
        let token = localStorage.getItem("Authorization");
        // token如果是null或者是空
        if (token === null || token === "") {
            // 让他去指定的登录页面
            next("/login");
        } else {
            // 否则放行
            next();
        }
    }
});
```

### 后置守卫`afterEach`

和 全局守卫`beforeEach` 不同的是 `afterEach` 不接收第三个参数 `next` 函数，也**不会改变导航本身**

```js
router.afterEach((to, from) => {
    //这里没有next()
    //doing
});
```

### 全局解析守卫`beforeResolve`

用的不是很多

```js
router.beforeResolve((to, from, next) => {
    console.log("解析");
    //doing
});
```

### 路由独享守卫`beforeEnter`

给某个路由单独使用的，本质上和后面的组件内钩子是一样的。都是特指的某个路由。不同的是，这里的一般定义在 router 当中，而不是在组件内

```js
var router = new VueRouter({
    routes: [
        {
            path: "/dashboard",
            component: dashboard,
            // 共给这个路由独享的守卫
            beforeEnter: (to, from, next) => {
                //doing
            },
            beforeLeave: (to, from, next) => {
                //doing
            },
        },
    ],
});
```

### 组件内路由守卫

用于组件内部的钩子,于其他不同的是，这个是写到组件里的，跟 methods 同级

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            title: "hello",
        };
    },
    beforeRouteEnter(to, from, next) {
        console.log("todo before enter");
        console.log(this); // 这里获取不到上下文 指的是windows
        next((vm) => {
            // next里面有一个回到函数可以获取到上下文，把请求到的数据塞到vue对象中
            console.log(vm); // vue实例化的对象
        });
    },
    //  同一个组件，param不同的是触发,常用与同一个组件当传入不通参数时，展示不同的数据
    beforeRouteUpdate(to, from, next) {
        console.log("todo update enter");
        next();
    },
    //  该组件离开跳转到另外的组件时触发该钩子,常应用于用户表单，当用户填了一部分内容，需要提醒用户是否离开页面
    beforeRouteLeave(to, from, next) {
        console.log("todo leave enter");
        next();
    },
});
```

---

## 路由懒加载

在单页应用中，如果没有应用懒加载，运用 webpack 打包后的文件将会异常的大，造成进入首页时，需要加载的内容过多，延时过长，不利于用户体验，运用懒加载可以将页面进行划分，按需加载页面，可以分担首页所承担的加载压力，减少加载用时。

```js
//正常路由
const routes = [
    { path: "/", component: "路径1" },
    { path: "/about", component: "路径2" },
    { path: "/mine", component: "路径3" },
];
```

-   1.使用 vue 的异步组件技术，可以实现按需加载，`resolve => require([URL], resolve)`

    **_但是，这种情况下 ，一个组件生成一个 js 文件。_**

    ```js
    // 写法
    const routes = [
        { path: "/", component: (resolve) => require(["路径1"], resolve) },
        { path: "/about", component: (resolve) => require(["路径2"], resolve) },
        { path: "/mine", component: (resolve) => require(["路径3"], resolve) },
    ];
    ```

-   2.vue-router 配置路由，使用 webpack 的 require.ensure 技术，也可以实现按需加载。 `resolve => require.ensure([ ], ()=>{resolve(require())})`

    **_这种情况下，多个路由指定相同的 chunkName，会合并打包成一个 js 文件。_**

    ```js
    //写法
    const routes = [
        {
            path: "/",
            component: (resolve) =>
                require.ensure(["路径1"], () => {
                    resolve(require("路径1"));
                }),
        },
        {
            path: "/about",
            component: (resolve) =>
                require.ensure(["路径3"], () => {
                    resolve(require("路径2"));
                }),
        },
        {
            path: "/mine",
            component: (resolve) =>
                require.ensure(["路径3"], () => {
                    resolve(require("路径3"));
                }),
        },
    ];
    ```

-   3.推荐使用这种方式(需要 webpack > 2.4)。`() => import(URL)`

    ```js
    //写法
    const routes = [
        { path: "/", component: () => import("路径1") },
        { path: "/about", component: () => import("路径2") },
        { path: "/mine", component: () => import("路径3") },
    ];
    ```
