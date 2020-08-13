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

[点击体验]()

## watch

## computed
