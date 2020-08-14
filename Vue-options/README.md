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
            list: {
                handler: "init",
                immediate: true,
            },
        },
    });
    ```

---

## computed(计算属性)
