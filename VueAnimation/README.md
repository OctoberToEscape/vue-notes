# vue 动画与过渡

组件的切换有一种生硬的隐藏显示感觉，为了更好的用户体验，让组件切换时淡出淡入，Vue 提供了专门的组件 transition

## 过渡效果的场景

-   条件渲染 (使用 v-if)
-   条件展示 (使用 v-show)
-   动态组件
-   组件根节点

## 过渡的状态 class 名

-   enter： 定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。

-   enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除

-   enter-to： 定义进入过渡的结束状态

-   leave：定义离开过渡的开始状态

-   leave-active：定义过渡的状态。在元素整个过渡过程中作用，在离开过渡被触发后立即生效

-   leave-to：定义离开过渡的结束状态

官方图解：

![图解](https://cn.vuejs.org/images/transition.png)

每个状态在使用的时候都是在 CSS 中使用，结合组件 transition 的 name 属性。如 `<transition name="fade"></transition>`，对应的是 fade- 加上每个状态：fade-enter。

### CSS 过渡

```css
/*初始状态*/
.fade-enter {
    opacity: 0;
}
/*已经准备就绪*/
.fade-enter-active {
    transition: all 0.5s;
}
/*已经消失*/
.fade-leave-active {
    opacity: 0;
    transition: all 0.5s;
}
```

```html
<div id="app">
    <button @click="show = !show">{{show ? "隐藏" : '显示'}}</button>
    <br />
    <transition name="fade">
        <p v-show="show">我是一行字</p>
    </transition>
</div>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            show: true,
        };
    },
});
```

### CSS 动画

```css
.animation-enter-active {
    animation: animation-in 1s;
}

.animation-leave-active {
    animation: animation-out 1s;
}
@keyframes animation-in {
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}

@keyframes animation-out {
    from {
        opacity: 1;
    }
    to {
        opacity: 0;
    }
}
```

```html
<div id="app">
    <button @click="show = !show">{{show ? "隐藏" : '显示'}}</button>
    <br />
    <transition name="animation">
        <p v-if="show">我是CSS动画属性</p>
    </transition>
</div>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            show: true,
        };
    },
});
```

---

## 初始化加载渲染的过渡

第一次加载时的过渡效果，使用到组件 transition 的属性: `appear` , `appear-class` , `appear-active-class`。

```css
.first-enter {
    opacity: 0;
}
.first-enter-active {
    transition: all 3s;
}
```

```html
<div id="app">
    <transition
        appear
        appear-class="first-enter"
        appear-active-class="first-enter-active"
    >
        <p>我是初次渲染过渡</p>
    </transition>
</div>
```

```js
var app = new Vue({
    el: "#app",
});
```

---

## 多个元素的过渡效果

同时生效的进入和离开的过渡不能满足所有要求，所以 Vue 提供了 过渡模式：

in-out：新元素先进行过渡，完成之后当前元素过渡离开。

out-in：当前元素先进行过渡，完成之后新元素过渡进入。

利用 mode 属性 , 以及 key

```css
/*初始状态*/
.fade-enter {
    opacity: 0;
}
/*已经准备就绪*/
.fade-enter-active {
    transition: all 0.5s;
}
/*已经消失*/
.fade-leave-active {
    opacity: 0;
    transition: all 0.5s;
}
```

```html
<fieldset>
    <legend><h3>mode = in-out</h3></legend>
    <div>
        <button @click="black = !black">
            {{black ? "write" : 'black'}}
        </button>
        <br />
        <transition name="fade" mode="in-out">
            <p v-if="black" style="background: #000; color: #fff;" key="black">
                black
            </p>
            <p v-else key="write">write</p>
        </transition>
    </div>
</fieldset>
<fieldset>
    <legend><h3>mode = out-in</h3></legend>
    <div>
        <button @click="flag = flag == 1 ? 2 : flag == 2 ? 3 : 1">
            {{flag == 1 ? 'green' : flag == 2 ? 'yellw' : 'red'}}
        </button>
        <br />
        <transition name="fade" mode="out-in">
            <p v-if="flag == 1" style="background: red;" key="red">
                红
            </p>
            <p v-else-if="flag == 2" style="background: green;" key="green">
                绿
            </p>
            <p v-else style="background: yellow;" key="yellow">
                黄
            </p>
        </transition>
    </div>
</fieldset>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            black: true,
            flag: 1,
        };
    },
});
```

[以上的代码地址 ](https://github.com/OctoberToEscape/vue-notes/tree/master/VueAnimation/index.html)

---

## 列表(v-for)的过渡效果

v-for 生成列表过渡效果要使用组件 `transition-group`，组件提供属性 `tag`表示该组件将会渲染成对应的 DOM 节点，其它的使用和 transition 一样。

```css
* {
    list-style: none;
}
li {
    width: 300px;
    margin-bottom: 5px;
    padding: 10px 20px;
    background-color: #ccc;
}

.list-enter {
    opacity: 0;
    transform: translateX(300px);
}
.list-enter-active {
    transition: all 0.5s;
}

.list-leave-active {
    transition: all 0.5s;
    opacity: 0;
    transform: translateX(-300px);
}
```

```html
<div id="app">
    <p>
        <input type="button" value="AddItem" @click="addItem" />
        <input type="button" value="RemoveItem" @click="removeItem" />
    </p>
    <transition-group tag="ul" name="list">
        <li v-for="(item, index) in items" :key="item">
            Item {{index}}
        </li>
    </transition-group>
</div>
```

```js
var app = new Vue({
    el: "#app",
    data: {
        items: [1, 2, 3],
    },
    methods: {
        randomIndex() {
            return parseInt(this.items.length * Math.random());
        },
        addItem() {
            this.items.splice(this.randomIndex(), 0, this.items.length + 1);
        },
        removeItem() {
            this.items.splice(this.randomIndex(), 1);
        },
    },
});
```

[以上的代码地址 ](https://github.com/OctoberToEscape/vue-notes/tree/master/VueAnimation/list.html)

---

## 过渡效果钩子函数

除了用 CSS 过渡的动画来实现 vue 的组件过渡，还可以用 JavaScript 的钩子函数来实现，在钩子函数中直接操作 DOM。我们可以在属性中声明以下钩子：

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document</title>
    </head>
    <body>
        <div id="app">
            <transition
                v-on:before-enter="beforeEnter"
                v-on:enter="enter"
                v-on:after-enter="afterEnter"
                v-on:enter-cancelled="enterCancelled"
                v-on:before-leave="beforeLeave"
                v-on:leave="leave"
                v-on:after-leave="afterLeave"
                v-on:leave-cancelled="leaveCancelled"
            >
            </transition>
        </div>
    </body>
</html>
<script>
    var app = new Vue({
        el: "#app",
        methods: {
            // 过渡进入
            // 设置过渡进入之前的组件状态
            beforeEnter(el) {
                // ...
            },
            // 设置过渡进入完成时的组件状态
            enter(el, done) {
                // ...
                done();
            },
            // 设置过渡进入完成之后的组件状态
            afterEnter(el) {
                // ...
            },
            enterCancelled(el) {
                // ...
            },
            // 过渡离开
            // 设置过渡离开之前的组件状态
            beforeLeave(el) {
                // ...
            },
            // 设置过渡离开完成时地组件状态
            leave(el, done) {
                // ...
                done();
            },
            // 设置过渡离开完成之后的组件状态
            afterLeave(el) {
                // ...
            },
            // leaveCancelled 只用于 v-show 中
            leaveCancelled(el) {
                // ...
            },
        },
    });
</script>
```
