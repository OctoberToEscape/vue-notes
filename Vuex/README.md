# vuex

## 什么是 vuex

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用**集中式存储管理**应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

其实最简单理解为，在我们写 Vue 组件中，一个页面多个组件之间想要通信数据，那你可以使用 Vuex

-   Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式

-   Vuex 状态管理 === 管理组件数据流动 === **全局**数据管理

-   Vue 的全局数据池，在这里它存放着大量的复用或者公有的数据，然后可以分发给组件

-   Vue 双向数据绑定的 MV 框架，数据驱动(区别节点驱动)，模块化和组件化，所以管理各组件和模块之间数据的流向至关重要

-   Vuex 是一个前端非持久化的数据库中心，Vuex 其实是 Vue 的重要选配，一般小型不怎么用，大型项目运用比较多，所以页面刷新，Vuex 数据池会重置

---

## 安装

```
//vue-cli的情况下
npm install vuex --save

//可以用script标签引入
<script src="https://unpkg.com/vuex@3.0.1/dist/vuex.js"></script>
```

## 使用

### 初始化 store 下 index.js 中的内容

```js
import Vue from "vue";
import Vuex from "vuex";

//挂载Vuex
Vue.use(Vuex);

//创建VueX对象
const store = new Vuex.Store({
    state: {
        //存放的键值对就是所要管理的状态
        name: "heqi",
    },
});

export default store;
```

### 挂载到 main.js 中

```js
// 引入仓库
import store from "./store/index.js";
// Root容器
new Vue({
    store,
    render: (h) => h(App),
}).$mount("#app");
```

---

## vuex 的五大概念

-   State 池 (数据源)

    我们来看一个简单的 state 定义全局变量的例子

    ```js
    //这样这个count就是全局的一个状态变量了
    const store = new Vuex.Store({
        state: {
            count: 0,
        },
    });
    ```

    那么我们怎么在 vue 组件中拿到这个全局定义的状态变量呢，就得看 getter 的属性了

-   Getter 查 (获取数据源的数据)

    我们定义 2 个组件,分别为 page1，page2

    ```html
    <div id="app">
        <page1></page1>
        <page2></page2>
    </div>
    ```

    -   方法 1，直接拿(不推荐)

        ```js
        var app = new Vue({
            el: "#app",
            store,
            components: {
                page1: {
                    template: `<div>
                                    <h1>组件1</h1>
                                    <p>{{val}}</p>
                                </div>
                            `,
                    computed: {
                        val() {
                            return store.state.count;
                        },
                    },
                },
                page2: {
                    template: `<div>
                                    <h1>组件2</h1>
                                    <p>{{val}}</p>
                                </div>
                            `,
                    computed: {
                        val() {
                            return store.state.count;
                        },
                    },
                },
            },
        });
        ```

        需要注意的是,如果把 `store.state.count`放到`data`中，那么`count`变化的时候是**不会**主动触发页面刷新的，当然我们也不能`<div>{{store.state.count}}</div>`来拿到属性的，因为模版中是无法访问 store 对象的。通常是通过**计算属性 computed**来拿到 store 的属性

    -   方法 2，通过 `getter` 来拿到属性（推荐）

        Getter 会暴露为 store.getters 对象，你可以以属性的形式访问这些值，注意：这里的 [moment](http://momentjs.cn/) 是一个第三方日期处理类库，使用之前需要导入。

        ```js
        //比如我们定义一个时间的全局变量
        const store = new Vuex.Store({
            state: {
                date: new Date(),
            },
            getters: {
                //fm是传递的参数，根据fm来展示不同的结构
                weekDate: (state) => (fm) => {
                    return moment(state.date).format(fm ? fm : "dddd");
                },
            },
        });
        ```

        这个时候我们拿这个 date 的时候

        ```js
        var app = new Vue({
            el: "#app",
            store,
            components: {
                page1: {
                    template: `<div>
                                    <h1>组件1</h1>
                                    <p>{{weekDate}}</p>
                                </div>
                            `,
                    computed: {
                        weekDate() {
                            return store.getters.weekDate();
                        },
                    },
                },
                page2: {
                    template: `<div>
                                    <h1>组件2</h1>
                                    <p>{{weekDate}}</p>
                                </div>
                            `,
                    computed: {
                        weekDate() {
                            return store.getters.weekDate("MM Do YY");
                        },
                    },
                },
            },
        });
        //这样我们就通过getter在2个组件中访问到同一个全局变量，而且通过传参展示不同的结构，如下图
        ```

        ![img1.png](https://i.loli.net/2020/08/24/ou5iUD7Npg9mdOy.png)

-   Mutation 改 (**真正修改**的动作，是**同步**操作)

    只有`Mutation`才能真正的**修改** state 里的数值。

    还是刚才的例子，我们加上几个按钮。

    store 代码

    ```js
    const store = new Vuex.Store({
        state: {
            count: 0,
            date: new Date(),
            person: {
                name: "heqi",
                age: 24,
            },
        },
        //唯一能修改state里的数值的方法
        mutations: {
            //规则：方法名通常是全大写形式
            //可以传递两个参数，第一个参数state，第二个参数接受值，通常是对象形式

            ADD_COUNT: (state, val) => {
                state.count += val.num;
            },

            REDUCE_COUNT: (state, val) => {
                state.count -= val ? val.num : 1;
            },

            MULTIPLICATION_COUNT: (state, val) => {
                if (state.count === 0) {
                    alert("0 * 任何数都是0");
                    return false;
                }
                state.count *= val.num;
            },

            CHANGE_PERSON: (state) => {
                // 添加新属性
                Vue.set(state.person, "sex", "男");
                // 或者 state.person = { ...state.person, sex: "男" };

                // 改变原有属性
                state.person.age = 100;
            },
        },
    });
    ```

    组件调用代码

    ```js
    var app = new Vue({
        el: "#app",
        store,
        components: {
            page1: {
                template: `<div>
                                <h1>组件1</h1>
                                <p>{{val}}</p>
                                <p>{{person}}</p>
                                <button @click="handleAdd">点击加</button>
                                <button @click="handleReduce">点击减</button>
                                <button @click="addSex">点击加性别改年龄</button>
                            </div>
                        `,
                computed: {
                    val() {
                        return store.state.count;
                    },
                    person() {
                        return store.state.person;
                    },
                },
                methods: {
                    //调用修改store里的值，通常2种方法
                    /*
                     * 1.把载荷和type分开提交
                        store.commit('store定义的方法', {
                            传递的参数: ===>  键名:键值
                        })
                        
                        2.整个对象都作为载荷传给 mutation 函数
                        store.commit({
                            type: 'store定义的方法名',
                            传递的参数: ===>  键名:键值
                        })
                     */
                    handleAdd() {
                        store.commit("ADD_COUNT", { num: 10 });
                    },
                    handleReduce() {
                        store.commit("REDUCE_COUNT", { num: 14 });
                    },
                    addSex() {
                        store.commit("CHANGE_PERSON");
                    },
                },
            },
            page2: {
                template: `<div>
                                <h1>组件2</h1>
                                <p>{{val}}</p>
                                <button @click="handleAdd">点击加</button>
                                <button @click="handleReduce">点击减</button>
                            </div>
                        `,
                computed: {
                    val() {
                        return store.state.count;
                    },
                },
                methods: {
                    handleAdd() {
                        store.commit("ADD_COUNT", { num: 3 });
                    },
                    handleReduce() {
                        store.commit("REDUCE_COUNT");
                    },
                },
            },
        },
    });
    ```

-   Action 触发修改行为(多配合**异步**使用)

    Action 类似于 mutation，不同在于：

    -   1、Action 提交的是 mutation，而不是直接变更状态。

    -   2、Action 可以**包含任意异步**操作。

    我们来看一个例子

    store 的写法

    ```js
    const store = new Vuex.Store({
        state: {
            count: 0,
        },
        mutations: {
            MULTIPLICATION_COUNT: (state, val) => {
                if (state.count === 0) {
                    alert("0 * 任何数都是0");
                    return false;
                }
                state.count *= val.num;
            },

        },
        actions: {
            //actions提交的是mutations
            /*
             * 写法：事件名:({commit},'接受传递的参数') => {
                        commit('mutations里面的事件名','传递的参数')
                    }
             */
            MULTIPLICATION_COUNT: ({ commit }, val) => {
                // 可执行异步操作
                setTimeout(() => {
                    commit("MULTIPLICATION_COUNT", val);
                }, 1000);
            },
        },
    ```

    组件内的分发写法

    ```js
    var app = new Vue({
        el: "#app",
        store,
        components: {
            page1: {
                template: `<div>
                                <h1>组件1</h1>
                                <p>{{val}}</p>
                                <button @click="handleMultiplication">点击乘</button>
                            </div>
                        `,
                computed: {
                    val() {
                        return store.state.count;
                    },
                },
                methods: {
                    handleMultiplication() {
                        /*
                         * 写法： store.dispatch('actions里的事件名',传递的参数 ===> {键名:键值})
                         */
                        store.dispatch("MULTIPLICATION_COUNT", { num: 3 });
                    },
                },
            },
        },
    });
    ```

-   Module 可以拥有多个数据源(数据池)

---

## vuex 的辅助函数

### mapState

mapState 工具函数会将 store 中的 state 映射到局部计算属性中。

```js
// 想获得store里的某个属性
var app = new Vue({
    el: "#app",
    computed: {
        // 常规写法
        val() {
            return store.state.count;
        },
        // 运用辅助函数写法

        // 写法1
        // 映射 this.count 到 this.$store.state.count
        ...Vuex.mapState(["count"]), //这里的模版 <div>{{count}}</div>
        // 写法2
        ...Vuex.mapState({
            val: (state) => state.count, //这里的模版 <div>{{val}}</div>
        }),
        // 写法三
        // 这里的 count 等同于 (state) => state.count
        ...Vuex.mapState({
            num: "count", //这里的模版 <div>{{num}}</div>
        }),
    },
});
```

### mapGetters

mapGetters 工具函数会将 store 中的 getter 映射到局部计算属性中,他和 mapState 很相似

getters 中的方法

```js
const store = new Vuex.Store({
    state: {
        person: {
            name: "heqi",
            age: 24,
        },
    },
    getters: {
        getPerson: (state) => {
            return state.person;
        },
    },
});
```

```js
var app = new Vue({
    el: "#app",
    store,
    components: {
        page1: {
            template: `<div>
                            <h1>组件1</h1>
                            <p>{{val}}</p>
                            <p>{{person}}</p>
                        </div>
                    `,
            computed: {
                // 常规写法
                person() {
                    return store.state.person;
                },
                // 使用辅助函数写法
                // 方法一
                ...Vuex.mapGetters(["getPerson"]) //这里的模版 <div>{{getPerson}}</div>
                // 方法2
                ...Vuex.mapGetters({
                    person: "getPerson", //这里的模版 <div>{{person}}</div>
                }),
            },
        },
    },
});
```

### mapMutations

mapMutations 是 vuex 的 mutation 的辅助函数，用于在组件中映射 mutation 内的方法，以便在该组件中直接使用 mutation 里的方法 (说白了，就是一语法糖)

```js
var app = new Vue({
    el: "#app",
    store,
    components: {
        page1: {
            template: `<div>
                            <h1>组件1</h1>
                            <p>{{val}}</p>
                            <button @click="handleAdd">点击加</button>
                        </div>
                    `,
            computed: {
                ...Vuex.mapState({
                    val: (state) => state.count,
                }),
            },
            methods: {
                // 第一步通过...mapMutations([方法名])讲方法引入
                ...Vuex.mapMutations(["ADD_COUNT"]),
                handleAdd() {
                    // 辅助方法调取此方法，穿参则在 方法后(参数)
                    this.ADD_COUNT({ num: 10 });
                    // 常规方法
                    store.commit("ADD_COUNT", { num: 10 });
                },
            },
        },
    },
});
```

### mapActions

mapActions 工具函数会将 store 中的 dispatch 方法映射到组件的 methods 中。和 mapGetters 也类似。

```js
var app = new Vue({
    el: "#app",
    store,
    components: {
        page1: {
            template: `<div>
                            <h1>组件1</h1>
                            <p>{{val}}</p>
                            <button @click="handleMultiplication">点击乘</button>
                        </div>
                    `,
            computed: {
                ...Vuex.mapState({
                    val: (state) => state.count,
                }),
            },
            methods: {
                // 第一步通过...mapActions([方法名])讲方法引入
                ...Vuex.mapActions(["MULTIPLICATION_COUNT"]),
                handleMultiplication() {
                    // 辅助方法调取此方法，穿参则在 方法后(参数)
                    this.MULTIPLICATION_COUNT({ num: 4 });
                    // 常规写法
                    store.dispatch("MULTIPLICATION_COUNT", { num: 3 });
                },
            },
        },
    },
});
```

[代码源码体验地址](https://github.com/OctoberToEscape/vue-notes/tree/master/Vuex/index.html)
