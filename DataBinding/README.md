# 一些 vue 的学习过程

## vue 的双向数据绑定原理绑定原理

### vue2.0 原理

```html
<div class="vue2.0">
    <h3>vue2.0</h3>
    <span class="span1"></span><br />
    <input type="text" oninput="changeValue(this.value)" />
</div>
```

```js
var dom1 = document.querySelector(".span1");
var data = {};
const changeValue = (value) => {
    data.value = value;
};
Object.defineProperty(data, "value", {
    get() {
        return value;
    },
    set(newValue) {
        dom1.innerHTML = newValue;
    },
});
```

### vue3.0 原理

```html
<div class="vue2.0">
    <h3>vue3.0</h3>
    <span class="span2"></span><br />
    <input type="text" oninput="changeValues(this.value)" />
</div>
```

```js
var dom2 = document.querySelector(".span2");
var obj = {};
const changeValues = (value) => {
    newObj[0] = value;
};
var newObj = new Proxy(obj, {
    get(target, key) {
        return target[key];
    },
    set(target, key, newValue) {
        target[key] = newValue;
        dom2.innerHTML = target[key];
    },
});
```
