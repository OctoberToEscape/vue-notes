# Vue 实例化后的方法

## 事件处理器 methods

元素可以通过 v-on:事件 或者 @事件 进行绑定事件，事件中的 this 默认指向实例 vue

-   普通的事件处理

    ```html
    <div id="app">
        <button @click="onClick">点击事件</button>
        <span>{{num}}</span>
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                num: 0,
            };
        },
        methods: {
            onClick() {
                console.log(this);
                //Vue {_uid: 0, _isVue: true, $options: {…}, _renderProxy: Proxy, _self: Vue, …}
                this.num++;
            },
        },
    });
    ```

-   在 js 的事件中默认会有个 `event` 对象，Vue 在事件上对 event 对象进行继承二次封装，改名为 \$event，在事件当中默认传过去

    ```html
    <div id="app">
        <button @click="onClicEvent($event)" num="1">点击处理event</button>
        <span>{{num_event}}</span>
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                num_event: 0,
            };
        },
        methods: {
            onClicEvent(e) {
                var _num = e.target.getAttribute("num") * 1;
                this.num_event += _num;
                e.target.setAttribute("num", this.num_event);
            },
        },
    });
    ```

-   点击事件中传递参数

    ```html
    <div id="app">
        <button @click="onClickVal(val,$event)">
            点击处理val
        </button>
        <span>{{val}}</span>
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                val: 0,
            };
        },
        methods: {
            onClickVal(val, e) {
                if (this.val < 5) this.val++;
                else {
                    this.val = "到头了";
                    e.target.setAttribute("disabled", true);
                }
            },
        },
    });
    ```

---

## watch(监听)

Vue 提供了对单个属性的监听器，当该属性发生改变的时候，自动触发，当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的，但是此项使用不当会影响性能，所以慎用。

-   普通监听

    ```html
    <div id="app">
        <input type="text" v-model="watchVal" />
        <p>旧的值:{{ov}}</p>
        <p>新的值:{{nv}}</p>
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                watchVal: 1,
                ov: null,
                nv: null,
            };
        },
        watch: {
            watchVal(nv, ov) {
                this.ov = ov;
                this.nv = nv;
            },
        },
    });
    ```

-   当然监听到变化可以做一些事情

    ```html
    <div id="app">
        <input type="text" v-model="watchMethods" />
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                watchMethods: 1,
            };
        },
        methods: {
            alert() {
                alert("监听到变化触发了我");
            },
        },
        watch: {
            watchMethods(v) {
                if (v || v === "") this.alert();
            },
        },
    });
    ```

-   深度监听(deep)

    watch 里面还有一个属性 deep，默认值是 false，代表是否深度监听，比如我们 data 里有一个 obj 属性

    ```html
    <div id="app">
        <p>a的值:{{obj.a}}</p>
        <button @click="obj.a = 2">改变a得值</button>
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                obj: { a: 1 },
            };
        },
        watch: {
            //方法1,运用deep
            obj: {
                handler() {
                    console.log("deep,a的值监听到变化了");
                },
                deep: true,
            },
            //方法2,字符串形式监听
            "obj.a": {
                handler() {
                    console.log("字符串形式监听,a的值监听到变化了");
                },
                //deep:true 此处的deep可以不写
            },
        },
    });
    ```

-   immediate 属性

    一句话很好理解，申明了后立即执行 handle 方法，但是如果想着要在什么情境下使用，什么样的需求需要使用。举个例子,有个需求，点击这个按钮，出现一个列表，列表的数据是后端数据渲染的

    ```html
    <div id="app">
        <button @click="show = true">点我出列表</button>
        <div v-if="show">
            <span
                style="background: #ccc; margin-right: 10px;"
                v-for="item in list"
            >
                {{item}}
            </span>
        </div>
    </div>
    ```

    > 常规做法

    在 create 生命周期调取接口改变 list

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                list: [],
                show: false,
            };
        },
        created() {
            this.init();
        },
        methods: {
            init() {
                this.list = [1, 2, 3];
            },
        },
    });
    ```

    > immediate 写法

    这样就会在初始化立即执行。

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                list: [],
                show: false,
            };
        },
        methods: {
            init() {
                this.list = [1, 2, 3];
            },
        },
        watch: {
            show: {
                handler: "init",
                immediate: true,
            },
        },
    });
    ```

---

## computed(计算属性)

`computed` 主要是针对 `data` 的属性进行操作，`this` 的指针默认指向实例 `vue 实例`。模版中放入太多声明式逻辑会让模板臃肿尤其在页面大量使用复杂的逻辑表达式处理数据，会对页面的可维护性造成很大的影响，而 `computed` 的设计初衷也正是用于解决此类问题。

```html
<div id="app">
    <!-- 计算属性写法 -->
    <p>{{Person}}</p>
    <!-- 常规写法 -->
    <p>
        我今年{{person.age}}, 名字叫做{{person.name}}, 性别{{person.gender ?
        '男':'女'}}
    </p>
</div>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            person: {
                name: "heqi",
                age: 24,
                gender: true,
            },
        };
    },
    computed: {
        Person() {
            return `我今年${this.person.age},名字叫做${this.person.name},性别${
                this.person.gender ? "男" : "女"
            }`;
        },
    },
});

// 两种结果为下图
```

![img1.png](https://i.loli.net/2020/08/14/vUeGIKqi5HEulcp.png)

---

## computed 的 get 和 set 属性

### get()属性

当我们在 V 层调用 {{Person}} 的时候会自动触发 Person.get(),上面的写法也可以这样写

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            person: {
                name: "heqi",
                age: 24,
                gender: true,
            },
        };
    },
    computed: {
        Person: {
            get() {
                return `我今年${this.person.age},名字叫做${
                    this.person.name
                },性别${this.person.gender ? "男" : "女"}`;
            },
        },
    },
});
```

### set()属性

setter 的逻辑也是一样，当改变对应的属性时，会自动触发 set 方法

```html
<div id="app">
    <!--调用了 fullName.get()-->
    <p>{{fullName}}</p>
    <input type="text" v-model="newName" />
    <!--changeName 事件改变了 fullName 的值，所以会自动触发 fullName.set()-->
    <input type="button" value="changeName" @click="changeName" />
</div>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            firstName: "heqi",
            lastName: "javascript",
            newName: "",
        };
    },
    methods: {
        changeName() {
            this.fullName = this.newName;
        },
    },
    computed: {
        fullName: {
            get() {
                return this.firstName + "." + this.lastName;
            },
            set(newValue) {
                this.firstName = newValue;
            },
        },
    },
});
```

---

## computed 和 watch 的区别

-   computed 创建**新**的属性， watch 监听 data **已有**的属性
-   compute 会产生依赖缓存
-   当 watch 监听 computed 时，watch 在这种情况下无效，仅会触发 computed.setter

    ```html
    <div id="app">
        <input type="text" v-model="a" />
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {};
        },
        watch: {
            a() {
                //不会触发
                console.log("监听到point改变了");
            },
        },
        computed: {
            a: {
                get() {
                    return "前端";
                },
                set(newValue) {
                    //会触发这里
                    console.log("set val", newValue);
                },
            },
        },
    });
    ```
