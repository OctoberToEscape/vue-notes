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

-   v-bind:class="[]"，数组元素可以为表达式，也可以为字符串，如果是字符串则直接输出为样式名

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                isActive: true,
                class1: "arrClass",
                class2: "coexistence",
            };
        },
    });
    ```

    ```css
    .active {
        color: red;
    }
    .coexistence {
        font-size: 24px;
    }
    .arrClass {
        background: chartreuse;
    }
    .class3 {
        text-decoration: underline;
    }
    ```

    ```html
    <div v-bind:class="[class1,class2,'class3',isActive ? 'arrClass':'']">
        我是数组形式
    </div>
    ```

    渲染结果：

    ```html
    <div class="arrClass coexistence class3 arrClass">
        我是数组形式
    </div>
    ```

-   不过，当有多个条件 class 时这样写有些繁琐。所以在数组语法中也可以使用对象语法

    ```html
    <div
        v-bind:class="[{active : isActive ,coexistence : !isActive} , 'class3']"
    >
        我是数组包含对象
    </div>
    ```

    渲染结果：

    ```html
    <div class="active class3">
        我是数组包含对象
    </div>
    ```

---

## 绑定 style 样式

v-bind:style 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 或短横线分隔 ('course-item'，记得用引号括起来) 来命名

-   普通绑定

    ```html
    <div v-bind:style="{color : activeColor , fontSize : activeFont + 'px'}">
        我是绑定style
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                activeColor: "yellow",
                activeFont: 36,
            };
        },
    });
    ```

    渲染结果：

    ```html
    <div style="color: yellow; font-size: 36px;">
        我是绑定style
    </div>
    ```

-   绑定一个对象

    ```html
    <div v-bind:style="special">
        我是绑定一个对象
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                special: {
                    color: "blue",
                    fontSize: "26px",
                },
            };
        },
    });
    ```

    渲染结果：

    ```html
    <div style="color: blue; font-size: 26px;">
        我是绑定一个对象
    </div>
    ```

-   多重值

    可以为 style 绑定中的 property 提供一个包含多个值的数组，常用于提供多个带前缀的值

    ```html
    <!-- 这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 display: flex。 -->

    <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }">
        <div>123</div>
        <div>456</div>
    </div>
    ```

    渲染结果：

    ```html
    <div style="display: flex;">
        <div>123</div>
        <div>456</div>
    </div>
    ```
