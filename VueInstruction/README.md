# vue 指令

### 官方给出的指令

<table>
  <thead>
    <tr>
      <th>指令</th><th>类型</th><th>用法</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>v-text</td><td>string</td><td><!--v-text-->
      
  ``` html
    <span v-text="message"></span>
    <!--效果等同于-->
    <!--v-text 的权重高于 {{}}-->
    <span>{{message}}</span>
  ```
  
   </td></tr>
    <tr><td>v-html</td><td>string</td><td><!--v-html-->
      
  ``` html
      <!-- 富文本渲染 -->
    <div v-html="html"></div>
  ```

 </td></tr>
  <tr><td>v-show</td><td>boolean</td><td><!--v-show-->

```html
<!--show 的值会直接影响 div 在文档中是否显示(存在,改变样式让他隐藏而已)-->
<div v-show="show"></div>
```

   </td></tr>    
    <tr><td>v-if</td><td>boolean</td><td><!--v-if-->
      
  ``` html
    <!--status 的值会直接影响 div 在文档中是否存在(创建和删除节点)-->
    <div v-if="delete"></div>
  ```
  
   </td></tr>
    <tr><td>v-else-if</td><td>boolean</td><td><!--v-else-if-->
      
  ``` html
    <div v-if="flag == 1">1</div>
    <!--必须跟 v-if 或者 v-else-if 元素后面-->
    <div v-else-if="flag == 2">2</div>
  ```
  
   </td></tr>      
    <tr><td>v-else</td><td>不需要表达式</td><td><!--v-else-->
      
  ``` html
    <div v-if="flag == 1">1</div>
    <div v-else-if="flag == 2">2</div>
    <!--必须跟 v-if 或者 v-else-if 元素后面-->
    <div v-else>2</div>
  ```
  
   </td></tr>    
    <tr><td>v-for</td><td>Array | Object | Number | String</td><td><!--v-for-->
      
  ``` html
    <!--
      data = 3 
      结果会生成 3 个 div，
      value 的值分类为 1, 2, 3 
      index 的值分别为 0, 1, 2
    -->
    <div v-for="(value, index) in data">
      <span v-text="value"></span>
      <span>{{index}}</span>
    </div>
    <!--也可以这样写-->
    <div v-for="value in data">
      <span v-text="value"></span>
    </div>

    <!--
      data = "abc"
      结果会生成 data.length 个 div，
      value 的值分类为 a, b, c
      index 的值分别为 0, 1, 2
    -->
    <div v-for="(value, index) in data">
      <span v-text="value"></span>
      <span>{{index}}</span>
    </div>
    <!--也可以这样写-->
    <div v-for="value in data">
      <span v-text="value"></span>
    </div>

    <!--
      data = {name: 'dk', age: 18}
      结果会生成 data 属性个数 个 div，
      value 的值分类为 dk, 18
      key 的值分别为 name, age
    -->
    <div v-for="(value, key) in data">
      <span v-text="key"></span>
      <span>{{value}}</span>
    </div>
    <!--也可以这样写-->
    <div v-for="value in data">
      <span v-text="value"></span>
    </div>

    <!--
      data = [{name: 'dk1', age: 18}, {name: 'dk2', age: 20}]
      结果会生成 data.length 个 div，
      obj 的值分类为 data[0], data[1]
      index 的值分别为0, 1
    -->
    <div v-for="(obj, index) in data">
      <span v-text="JSON.stringify(obj)"></span>
      <span>{{index}}</span>
    </div>
    <!--也可以这样写-->
    <div v-for="obj in data">
      <span v-text="JSON.stringify(obj)"></span>
    </div>

````

 </td></tr>
  <tr><td>v-on</td><td>Function</td><td><!--v-on-->

``` html
  <!--click事件直接绑定一个方法-->
  <button v-on:click="onClick">say1</button>
  <!--缩写方式-->
  <!--click事件使用内联语句-->
  <button @click="onClick2('调用了 say2', $event)">say2</button>
````

   </td></tr> 
       <tr><td>v-bind</td><td>Object</td><td><!--v-bind-->
      
  ``` html
    <img v-bind:src="url" />
    <!--缩写方式-->
    <img :src="url" />
  ```
  
   </td></tr>    
      <tr><td>v-model</td><td>表单元素的值</td><td><!--v-model-->
      
  ``` html
    <!--仅限于表单元素，双向绑定-->
    <input type="text" v-model="msg"/>
  ```
  
   </td></tr>  
      <tr><td>v-pre</td><td>不需要表达式</td><td><!--v-pre-->
      
  ``` html
    <!--{{}} 不编译，当字符串输出-->
    <span v-pre>{{mess}}</span>
    <!-- 输出 {{mess}}-->
  ```
  
   </td></tr>  
      <tr><td>v-cloak</td><td>不需要表达式</td><td><!--v-cloak-->
      
  ``` html
    <!--
      mess = 'hello world'
      span 还没被 vue 解析的时候会显示 {{mess}}
      解析后会显示 hello world
      用于解决这两个转换的过程不友好的显示
      尤其是在页面加载过慢的情况很容易出现这种情况
    -->
    <span v-cloak>{{mess}}</span>
  ```
  
   </td></tr>     
      <tr><td>v-once</td><td>不需要表达式</td><td><!--v-once-->
      
  ``` html
    <!--内容只解释一次，当改变 mess 时不会再次映射到 span-->
    <span v-once>{{mess}}</span>
  ```
  
   </td></tr>              
  </tbody>
</table>

---

### 自定义指令

在某些情况下，官方提供的指令可能满足不了需求，所以需要自定义一些指令来满足于需求

> 全局指令

```html
<input type="text" v-public />
```

```js
//全局指令
Vue.directive("public", (el) => {
    el.value = "我是自定义指令";
});
```

> 局部指令

```html
<input type="text" v-private />
```

```js
var app = new Vue({
    el: "#app",
    directives: {
        //局部自定义指令
        private(el) {
            el.style.background = "red";
        },
    },
});
```

> 钩子函数

钩子函数可以理解成是指令的生命周期

```html
<input type="text" v-model="text" v-demo="{color:'red'}" />
```

```js
Vue.directive("demo", {
    //被绑定元素插入父节点时调用
    /*
     * @element:使用指令的元素
     * @binding:使用指令的属性对象
     * @vnode:整个 Vue 实例
     */
    inserted(element, binding, vnode) {
        console.log("inserted");
    },
    //只调用一次，指令第一次绑定到元素时调用，
    //用这个钩子函数可以定义一个在绑定时执行一次的初始化动作
    //先于 inserted 触发
    bind(element, binding, vnode) {
        console.log("bind");
        element.style.color = binding.value.color;
    },
    //被绑定元素所在的模板更新时调用，而不论绑定值是否变化
    update(element, binding, vnode) {
        console.log("update");
        element.style.color = "#ccc";
    },
    //被绑定元素所在模板完成一次更新周期时调用。
    componentUpdated(element, binding, vnode) {
        console.log("componentUpdated");
        setTimeout(() => {
            element.style.color = "yellow";
        }, 2000);
    },
});
//触发顺序如下图
```

![img1.jpg](https://i.loli.net/2020/08/12/Ud6ZpF3IRrBWChg.jpg)

### \$set()的介绍

当实例对象 data 先设置好了结构，后期想添加新的属性是 不会自动绑定到视图当中，所以调用 \$set(原对象，新属性名，属性值) 进行绑定到视图当中

```html
<div id="app">
    <span style="color: blueviolet;" @click="handleSet">
        点我插入数据
    </span>
    <div v-cloak>{{info.age}}</div>
</div>
```

```js
var app = new Vue({
    el: "#app",
    data() {
        return {
            info: {},
        };
    },
    methods: {
        handleSet() {
            this.$set(this.info, "age", 23);
        },
    },
});
```
