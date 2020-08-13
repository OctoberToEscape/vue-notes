# 生命周期

每一个框架都有自己的生命周期,我们来看一下 vue 创建到结束都经历了什么。Vue 实例有一个完整的生命周期，也就是说从开始创建、初始化数据、编译模板、挂在 DOM、渲染-更新-渲染、卸载等一系列过程，我们成为 Vue 实例的生命周期，钩子就是在某个阶段给你一个做某些处理的机会。

## 图解

[官方给出的图的地址](https://cn.vuejs.org/images/lifecycle.png)

我在此的基础上进行的修改

![img1.png](https://i.loli.net/2020/08/13/DMO1BQS9vuUWRTw.png)

---

## 文字解析每个生命周期

### beforeCreate(创建前)

new Vue()之后，这个时候，数据还没有挂载呢，只是一个空壳。数据观测和事件配置之前被调用，此时组件的选项对象还未创建，el 和 data 并未初始化，因此**无法访问** `methods， data， computed`等上的方法和数据。

### created (创建后）

这一步是创建完成之后调用的，这里**完成了**对 `data` 数据的初始化，但是 `el` 的初始化还**没有完成**,因此\$el 属性是不可见的。这是一个常用的生命周期,可以调用 `methosd` 中的方法,获取 `computed` 计算属性值，可以更改 `data` 数据等，但是在这里更新数据**不会触发**`updated`生命周期。

### beforeMount(挂载前)

这是挂在之前调用的生命周期,即调用虚拟 Dom,该生命周期完成了以下配置：模版编译，将打他里的数据生成 html，完成了 el 和 data 的初始化，**注意的是这一切都是虚拟 Dom，并不是真是的，还没有挂载到 html 上，如果此时打印 dom 节点会出现 undefined**，在这里也可以更改数据，同样**不会触发**`updated`

### mounted(挂载后)

此时，组件已经出现在页面中，数据、真实 dom 都已经处理好了,事件都已经挂载好了,mounted 只会执行一次，可以在此生命周期进行 dom 操作或者请求数据等。

### beforeUpdate(更新前)

在数据更新之前被调用，发生在虚拟 DOM 重新渲染和打补丁之前，可以在该钩子中进一步地更改状态，**不会触发附加地重渲染过程** **_这里不能更改数据，否则会陷入死循环_**

### updated(更新后)

调用时，组件 DOM 已经更新，所以可以执行依赖于 DOM 的操作，**_这里也不能更改数据，否则会陷入死循环_**，**该钩子在服务器端渲染期间不被调用**

### beforeDestroy(销毁前)

销毁前还可以用 this 获取到实例，一般在这个生命周期做一些善后，比如：清除定时器，卸载监听事件，清除指令绑定等

### destroyed(销毁后)

组件的数据绑定、监听...都被清除了,只剩下 dom 空壳，这里也可以善后

---

## 代码解析生命周期

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>vue生命周期</title>
    </head>
    <body>
        <div id="app">
            <input type="button" value="修改msg" @click="msg='No'" />
            <h3 ref="message">{{ msg }}</h3>
        </div>
    </body>
</html>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            msg: "ok",
            timer: null,
            num: 0,
        };
    },
    methods: {
        show() {
            console.log("执行了方法show");
        },
    },
    beforeCreate() {
        console.log(this.msg); // undefined
        console.log(this.$refs.message); // undefined
        this.show(); // 报错 Error in beforeCreate hook: "TypeError: this.show is not a function"
        console.log(
            "beforeCreate,这个时候，数据和el还没有挂载呢，只是一个空壳"
        );
    },
    created() {
        this.timer = setInterval(() => {}, 1000);
        console.log(this.msg); // ok
        console.log(this.$refs.message); // undefined
        this.show(); // 执行了方法show
        console.log(this.$el); // undefined
        console.log(
            "created,这个时候已经可以使用到数据，也可以更改数据,在这里更改数据不会触发updated函数"
        );
        console.log(
            "在这里可以在渲染前倒数第二次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做初始数据的获取"
        );
        console.log(
            "接下来开始找实例或者组件对应的模板，编译模板为虚拟dom放入到render函数中准备渲染"
        );
    },
    beforeMount() {
        console.log(this.$refs.message); // undefined
        console.log(this.$el); // 如下
        /*
            <div id="app">
                <input type="button" value="修改msg" @click="msg='No'" />
                <h3 class="mssage">{{ msg }}</h3>
            </div>
        */
        console.log(
            "beforeMount：虚拟dom已经创建完成，马上就要渲染,在这里也可以更改数据，不会触发updated"
        );
        console.log(
            "在这里可以在渲染前最后一次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做初始数据的获取"
        );
        console.log("接下来开始render，渲染出真实dom");
    },
    mounted() {
        console.log(this.$refs.message); // 存在
        console.log(
            "mounted：此时，真实已经出现在页面中，数据、真实dom都已经处理好了,事件都已经挂载好了"
        );
        console.log("可以在这里操作真实dom等事情,比如el.addEventListener等;");
    },
    beforeUpdate() {
        console.log("界面上元素的内容：" + this.$refs.message.innerText); // ok
        console.log("data 中的 msg 数据是：" + this.msg); // no
        //当执行 beforeUpdate 的时候，页面中的显示的数据，还是旧的，此时 data 数据是最新的，页面尚未和 最新的数据保持同步
        console.log(
            "然后vue的虚拟dom机制会重新构建虚拟dom与上一次的虚拟dom树利用diff算法进行对比之后重新渲染"
        );
        console.log(
            "beforeUpdate 的时候，页面中的显示的数据，还是旧的，此时 data 数据是最新的，页面尚未和 最新的数据保持同步"
        );
    },
    updated() {
        console.log("界面上元素的内容：" + this.$refs.message.innerText); // no
        console.log("data 中的 msg 数据是：" + this.msg); // no
        //updated 事件执行的时候，页面和 data 数据已经保持同步了，都是最新的
        console.log("updated:数据已经更改完成，dom也重新render完成");
    },
    beforeDestroy() {
        console.log(
            "beforeDestory:一般在这里善后:清除计时器、清除非指令绑定的事件等等..."
        );
        clearInterval(this.timer);
        console.log(this.timer); // null
    },
    destroyed() {
        console.log(
            "destroyed:组件的数据绑定、监听...都去掉了,只剩下dom空壳，这里也可以善后"
        );
    },
});
```
