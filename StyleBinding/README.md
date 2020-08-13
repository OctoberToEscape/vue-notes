# class 与 style 绑定

## 绑定 class 名

### 对象的语法

-   可以通过 v-bind:class 传一个对象实现动态改变 class 名

    ```js
    //data
    var app = new Vue({
        el: "#app",
        data() {
            return {
                //定义在模版内的是定义的变量名
                isActive: true,
                hasError: false,
                //定义在模版外的对象里的键名是class名
                classObject: {
                    active: true,
                    coexistence: false,
                },
            };
        },
    });
    ```

    ```html
    <!-- 表明给div动态添加class名为active，如果isActive为真则class名为active，否则class名不存在 -->

    <div v-bind:class="{ active: isActive }">我是active的</div>
    <div v-bind:class="{ active: !isActive }">我是普通的</div>
    ```

    渲染结果:

    ```html
    <div class="active">我是active的</div>
    <div>我是普通的</div>
    ```

-   v-bind:class 指令也可以与普通的 class attribute 共存。

    > 写法 1,**内联定义`在`模板里**

    ```html
    <div
        class="static"
        v-bind:class="{ active: isActive, 'coexistence': !hasError }"
    >
        我是定义在模版里共存的
    </div>
    ```

    渲染结果:

    ```html
    <div class="static active coexistence">我是定义在模版里共存的</div>
    ```

    > 写法 2,**内联定义`不在`模板里**

    ```html
    <div class="static" v-bind:class="classObject">我是定义在模版外共存的</div>
    ```

    渲染结果:

    ```html
    <div class="static active">我是定义在模版外共存的</div>
    ```

-   通过计算属性绑定 class 名

    ```html
    <div v-bind:class="computedClass">我是通过计算属性绑定的class名</div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                isActive: true,
                hasNull: null,
                hasError: false,
            };
        },
        computed: {
            computedClass() {
                return {
                    active: this.isActive && !this.hasError,
                    coexistence: this.hasNull === null,
                };
            },
        },
    });
    ```

    渲染结果:

    ```html
    <div class="active coexistence">我是通过计算属性绑定的class</div>
    ```

---

### 数组语法
