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

### 父传子

```html
<div id="prop">
    <!-- 父传子的时候通过下方方式传递，动态属性前加： -->
    <child title="组件属性" :msg="msg" />
</div>
```

```js
var prop = new Vue({
    el: "#prop",
    data() {
        return {
            msg: "-动态属性",
        };
    },
    components: {
        child: {
            template: "<h1>{{title}}{{msg}}</h1>",
            // 声明 props
            props: ["title", "msg"],
        },
    },
});
```

最终渲染结果

```html
<div id="prop">
    <h1>组件属性-动态属性</h1>
</div>
```

### prop 验证和默认值

我们可以为组件的 `prop` 指定验证规则。如果传入的数据不符合要求，Vue 会发出警告。
要指定验证规则，需要用**对象**的形式来定义。

```html
<div id="validation">
    <child
        :prop-A="propAs"
        :prop-B="propBs"
        :prop-C="propCs"
        :prop-E="propEs"
    />
</div>
```

```js
var validation = new Vue({
    el: "#validation",
    data() {
        return {
            propAs: 12,
            propBs: "我是propB",
            propCs: "我是必须传递的",
            propDs: 12,
            propEs: { obj: "is obj" },
        };
    },
    components: {
        child: {
            template: `<div>
                            <p>{{propA}}</p>
                            <p>{{propB}}</p>
                            <p>{{propC}}</p>
                            <p>{{propD}}</p>
                            <p>{{propE}}</p>
                    </div>`,
            props: {
                // 基础类型检测 (`null` 指允许任何类型)
                propA: Number,
                // 可能是多种类型
                propB: [String, Number],
                // 必传且是字符串
                propC: {
                    type: String,
                    required: true,
                },
                // 数值且有默认值
                propD: {
                    type: Number,
                    default: 100,
                },
                // 数组/对象的默认值应当由一个工厂函数返回
                propE: {
                    type: Object,
                    default() {
                        return { message: "hello" };
                    },
                },
            },
        },
    },
});
```

输出结果

```html
<div id="validation">
    <div>
        <p>12</p>
        <p>我是propB</p>
        <p>我是必须传递的</p>
        <p>100</p>
        <p>{ "obj": "is obj" }</p>
    </div>
</div>
```

类型错误的报错如下图

![img2.png](https://i.loli.net/2020/08/17/ofInd43vEYKh5eF.png)

必传项不传的报错如下图

![img3.png](https://i.loli.net/2020/08/17/qtGD26LrCE1iaBu.png)

---

### 组件的自定义事件

`prop` 是父传子，子传父通过`$emit()`

```html
<div id="emit">
    <p>{{num}}</p>
    <!-- @handle 表示 子组件传递出去的事件名 -->
    <child v-on:handle="onClick"></child>
</div>
```

```js
var emit = new Vue({
    el: "#emit",
    data() {
        return {
            num: 0,
        };
    },
    methods: {
        onClick(val) {
            console.log(val); //打印子组件通过$emit()传递过来的值
            if (val > 3) this.num++;
        },
    },
    components: {
        child: {
            template: `<button @click="_onClick">点击传递事件-{{total}}</button>`,
            data() {
                return {
                    total: 0,
                };
            },
            methods: {
                _onClick() {
                    this.total++;
                    // 子组件通过$emit("传递出去的事件名"，传递的数值)
                    this.$emit("handle", this.total);
                },
            },
        },
    },
});
```

## 使用插槽 slot 分发内容

Vue 组件默认是覆盖渲染，为了解决这一问题，Vue 提出了 slot 分发内容

### 单个插槽

```html
<div id="slot">
    <child>
        <h1>java</h1>
        <h1>php</h1>
    </child>
</div>
```

```js
var slot = new Vue({
    el: "#slot",
    components: {
        child: {
            template: `<div>
                    <h1>javascript</h1>
                    <slot></slot>
                </div>`,
        },
    },
});
```

最终渲染结果

```html
<div>
    <h1>javascript</h1>
    <!-- 
        如果在组件定义的 template 当中没有 <slot></slot>，那么下面两个 h1 标签将不会存在
        换句话说就是 <slot></slot> = <h1>java</h1><h1>php</h1>
        <slot></slot>可以放到 <h1>javascript</h1> 上面进行位置调换
     -->
    <h1>java</h1>
    <h1>php</h1>
</div>
```

### 具名插槽

```html
<div id="nameslot">
    <child>
        <p>123</p>
        <p>456</p>
        <p slot="slotname">具名插槽</p>
    </child>
</div>
```

```js
var nameSlot = new Vue({
    el: "#nameslot",
    components: {
        child: {
            template: ` <div>
                            <slot name="slotname"></slot>
                            <h1>Tom</h1>
                            <slot></slot>
                        </div>`,
        },
    },
});
```

最终渲染结果

```html
<div>
    <!--<slot name="slotname"></slot> = <p slot="slotname">具名插槽</p>-->
    <p>具名插槽</p>
    <h1>Tom</h1>
    <!-- 其它没有 slot 属性的子元素将全部分发到 <slot></slot> 标签 -->
    <p>123</p>
    <p>456</p>
</div>
```

### 作用域插槽

作用域插槽是一种特殊类型的插槽，用作一个 (能被传递数据的) 可重用模板，来代替已经渲染好的元素。在子组件中，只需将数据传递到插槽，就像你将 prop 传递给组件一样，说白了就是**获取本组件的数据！**

```html
<!-- 父组件 -->
<div id="scope">
    <child>
        <!-- 
            语法：v-slot:default="随意取的名字" ，default可省略，简写为v-slot="随意取的名字" -->
        <template v-slot="slotProps">
            <h1>{{slotProps.list.text}}</h1>
        </template>
    </child>
</div>
```

```js
var scope = new Vue({
    el: "#scope",
    components: {
        child: {
            // 绑定到元素上的特性（v-bind:list="item"）被称为插槽 prop。现在在父级作用域中，我们可以使用带值的 v-slot 来定义我们提供的插槽 prop 的名字。

            // 绑定语法：v-bind:随意起的名字="需要传递的子组件数据"

            template: `<ul>
                            <li v-for="(item,index) in list" :key="index.id">
                                <slot v-bind:list="item"></slot>
                            </li>
                        </ul>`,
            data() {
                return {
                    list: [
                        {
                            id: 1,
                            text: "javascript",
                        },
                        {
                            id: 2,
                            text: "php",
                        },
                        {
                            id: 3,
                            text: "java",
                        },
                    ],
                };
            },
        },
    },
});
```

最终渲染结果

```html
<ul>
    <li><h1>javascript</h1></li>
    <li><h1>php</h1></li>
    <li><h1>java</h1></li>
</ul>
```

### 具名插槽的作用域插槽

与匿名插槽同理，只需要把 default 替换成插槽的 name 值即可。

```html
<!-- 父组件 -->
<div id="scope">
    <child>
        <!-- 
            语法：v-slot:具名的名字="随意取的名字" -->
        <template v-slot:list="nameProps">
            <h1>{{nameProps.list.id}}</h1>
        </template>
    </child>
</div>
```

```js
var scope = new Vue({
    el: "#scope",
    components: {
        child: {
            template: `<ul>
                            <li v-for="(item,index) in list" :key="index.id">
                                <slot name="list" v-bind:list="item"></slot>
                            </li>
                        </ul>`,
            data() {
                return {
                    list: [
                        {
                            id: 1,
                            text: "javascript",
                        },
                        {
                            id: 2,
                            text: "php",
                        },
                        {
                            id: 3,
                            text: "java",
                        },
                    ],
                };
            },
        },
    },
});
```

最终渲染结果

```html
<ul>
    <li><h1>1</h1></li>
    <li><h1>2</h1></li>
    <li><h1>3</h1></li>
</ul>
```

### 解构插槽 Prop

v-slot 的值实际上可以是任何能够作为函数定义中的参数的 JavaScript 表达式。所以在支持的环境下 (单文件组件或现代浏览器)，你也可以使用 ES2015 解构来传入具体的插槽 prop。

```
语法：v-slot="{ xxx }"
```

我们来看一下结构插槽

```html
<div id="deconstruction">
    <child>
        <!-- 解构 v-slot="{子组件定义的名字}"" -->
        <template v-slot="{ users }">
            <h1>{{users.name}}</h1>
        </template>
    </child>
</div>
```

```js
var deconstruction = new Vue({
    el: "#deconstruction",
    components: {
        child: {
            template: `<div>
                    <slot v-bind:users="user"></slot>
                </div>`,
            data() {
                return {
                    user: {
                        name: "heqi",
                        age: 24,
                    },
                };
            },
        },
    },
});
```

最终渲染结果

```html
<div><h1>heqi</h1></div>
```

相对于传统的作用域插槽，优势在于父组件不用重新自定义名称在通过对象的方式点出来，则直接沿用子组件的名称选择其键名

我们也可以给重命名

```html
<div id="deconstruction">
    <child>
        <!-- 解构 v-slot="{子组件定义的名字 : 需要重命名的名字}"" -->
        <template v-slot="{ users : person }">
            <!-- 如果用之前的名字会报错如下图，需要改成重命名后的名字 -->
            <!-- <h1>{{users.name}}</h1> -->
            <h1>{{person.name}}</h1>
        </template>
    </child>
</div>
```

![img4.png](https://i.loli.net/2020/08/18/aTYoSXczbZ5qx3L.png)
