# 修饰符

## 表单修饰符

-   `.lazy`

    普通的 v-model 在输入时会实时更新，但是我们想输入完成以后再更新就会用到修饰符.lazy

    ```html
    <div id="app">
        <input type="text" v-model.lazy="value" />
        <p>{{value}}</p>
    </div>
    ```

-   `.number`

    有时候我们想表单输出的类型是 number 类型，就会用到修饰符.number

    ```html
    <div id="app">
        <input type="text" v-model.number="age" />
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                age: "",
            };
        },
        updated() {
            console.log(typeof this.age); // number
        },
    });
    ```

-   `.trim`

    我们想输入的内容前后不留空格的时候就会用到修饰符.trim

    ```html
    <div id="app">
        <input type="text" v-model.trim="name" />
        <p ref="trimp">{{name}}</p>
        <button @click="getTrim">获取</button>
    </div>
    ```

    ```js
    var app = new Vue({
        el: "#app",
        data() {
            return {
                name: "",
            };
        },
        methods: {
            getTrim() {
                console.log(this.$refs.trimp.innerHTML);
            },
        },
    });
    //效果如下图
    ```

    ![img1.png](https://i.loli.net/2020/08/14/l8xnYqRtPHSkum1.png)

    打印结果

    ![img2.jpeg](https://i.loli.net/2020/08/14/lqDvpxOFHAsCTIz.jpg)

---

## 事件修饰符

在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。，Vue.js 为 v-on 提供了事件修饰符

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doing"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doing"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即元素自身触发的事件先在此处处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doing">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doing">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doing"></a>
```

---

## 按键修饰符

监听键盘按键的时候，提供的修饰符

```html
<!-- 只有在keycode为13的时候才会触发 -->
<input v-on:keyup.13="submit" />
```

但是记住所有的 keycode 比较困难，所以 vue 还提供了别名

```html
<!-- 回车 -->
<input @keyup.enter="submit" />

<!-- tab -->
<input @keyup.tab="tab" />

<!-- 删除退格 -->
<input @keyup.delete="delete" />

<!-- esc -->
<input @keyup.esc="esc" />

<!-- 空格 -->
<input @keyup.space="space" />

<!-- 上⬆️ -->
<input @keyup.up="up" />

<!-- 下⬇️ -->
<input @keyup.down="down" />

<!-- 左⬅️ -->
<input @keyup.left="left" />

<!-- 右 -->
<input @keyup.right="right" />
```

---

## 系统修饰符

可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>

<!-- shift + P -->
<input @keyup.shift.80="doing" />

<!-- meta + U-->
<input @keyup.meta.85="doing" />
```

**请注意修饰键与常规按键不同，在和 keyup 事件一起用时，事件触发时修饰键必须处于按下状态。换句话说，只有在按住 ctrl 的情况下释放其它按键，才能触发 keyup.ctrl。而单单释放 ctrl 也不会触发事件。如果你想要这样的行为，请为 ctrl 换用 keyCode：keyup.17。**
