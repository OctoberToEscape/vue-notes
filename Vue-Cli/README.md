# vue-cli

## 介绍 vue-cli

-   Vue CLI 是一个基于 Vue.js 进行快速开发的完整系统。有三个组件：

-   CLI：@vue/cli 全局安装的 npm 包，提供了终端里的 vue 命令（如：vue create 、vue serve 、vue ui 等命令）

-   CLI 服务：@vue/cli-service 是一个开发环境依赖。构建于 webpack 和 webpack-dev-server 之上（提供 如：serve、build 和 inspect 命令）

-   CLI 插件：给 Vue 项目提供可选功能的 npm 包 （如： Babel/TypeScript 转译、ESLint 集成、unit 和 e2e 测试 等）

## 安装

-   node.js 版本

    Vue CLI 需要 Node.js 8.9 或更高版本 (推荐 8.11.0+)。你可以使用 nvm 或 nvm-windows 在同一台电脑中管理多个 Node 版本。

    检查 node 版本

    ```
    终端输入 node -v  出现下图则是你的版本号 v12.16.1
    ```

    ![img1.png](https://i.loli.net/2020/08/18/RZaPkhL4TpnNDQ8.png)

    如果没有 node 去官网下载， [node 下载地址](http://nodejs.cn/download/)

-   安装 Vue CLI

    如果全局安装过旧版本的 vue-cli(1.x 或 2.x)要先卸载它，否则跳过此步

    ```
    npm uninstall vue-cli -g
    ```

    安装@vue/cli（Vue CLI 3 的包名称由 vue-cli 改成了 @vue/cli）

    ```
    npm install -g @vue/cli
    ```

    可以检测一下 vue 安装后的版本

    ```
    vue -V  出现下图就是你的vue版本
    ```

    ![img2.png](https://i.loli.net/2020/08/18/bFovTInRHkuED12.png)

## 通过指令创建一个 vue-cli 的项目

```
vue create 你的项目名字
```

出现该图

![img3.jpeg](https://i.loli.net/2020/08/18/FhtGC5VriZlLbo8.jpg)

上方图片绿色方框里的提示是有更新的 vue-cli 出现，可以选择更新，在这里我们逃过此步骤，有想更新的按照提示的指令敲入即可

下方出现两个选项

-   default

    默认设置（直接 enter）非常适合快速创建一个新项目的原型，没有带任何辅助功能的 npm 包

-   Manually select features

    自定义配置（按方向键 ↓）是我们所需要的面向生产的项目，提供可选功能的 npm 包

在这里我们选择第二个选项，会出现下图

![img4.jpeg](https://i.loli.net/2020/08/18/k9VY4ONWETGRB5x.jpg)

```js
Babel   //转码器，可以将ES6代码转为ES5代码，从而在现有环境执行。
TypeScript  // TypeScript是一个JavaScript（后缀.js）的超集（后缀.ts）包含并扩展JavaScript 的语法，需要被编译输出为 JavaScript在浏览器运行，目前较少人再用
Progressive Web App (PWA) Support   // 渐进式Web应用程序
Router  // vue-router（vue路由）
Vuex    // vuex（vue的状态管理模式）
CSS Pre-processors  // CSS 预处理器（如：less、sass）
Linter / Formatter  // 代码风格检查和格式化（如：ESlint）
Unit Testing    // 单元测试（unit tests）
E2E Testing // e2e（end to end） 测试
```

选择你想要用到的 npm 按空格即可，在这里我选择 router,vuex,CSS Pre-processors,Progressive Web App (PWA) Support，将会出现下方图片

![img5.png](https://i.loli.net/2020/08/18/bvesJORyrToQBNY.png)

这是在询问你是否使用 history 路由模式，是 y，否 n，在这里我选择是

Vue-Router 利用了浏览器自身的 hash 模式和 history 模式的特性来实现前端路由（通过调用浏览器提供的接口）

选择完路由模式之后然后出现下方的图片

![img6.png](https://i.loli.net/2020/08/18/1gBFJp8WQnNCH2r.png)

询问你 css 预处理器选择哪一种，主要为 css 解决浏览器兼容、简化 CSS 代码 等问题，我选择是第二种，然后出现下图

![img7.png](https://i.loli.net/2020/08/18/toyKU68gkljHsCL.png)

是在询问你，提供一个插件化的 javascript 代码检测工具，你选择哪一种，我选择以后一种，会出现下图

![img8.png](https://i.loli.net/2020/08/18/kgICJi6aDQ2xzhm.png)

询问你什么时候进行代码的检测

-   lint on save

    在保存的时候进行检测

-   lint and fix on commit

    在你提交代码的时候进行检测

通常选择第一个，之后会出现下图

![img9.png](https://i.loli.net/2020/08/18/U3nIKHCJALR6DEG.png)

询问你，如何存放配置

-   In dedicated config files

    每一项配置都将生成一个对应的文件

-   In package.json

    将配置都写到 package.json 文件中

根据你的喜好来选择就好，这里我选择第一个，将会出现下图

![img10.png](https://i.loli.net/2020/08/18/cZTASJx2djsyzWK.png)

是否将这次的配置保存成默认的配置，以后就不用一个个选择了，都默认选择这个，根据自己喜好就成（y:记录本次配置，然后需要你起个名; n：不记录本次配置，回车等待后，出现下图则表示创建完成

![img11.png](https://i.loli.net/2020/08/18/fhayHmg2AVDPdYN.png)

这个时候我们进入到刚刚创建的 project 里面敲入下面指令运行

```
npm run serve
```

出现下方图片则表示运行成功了

![img12.jpeg](https://i.loli.net/2020/08/18/wI5VjSNOpCZzbiQ.jpg)

我们随便选择一个链接就进入到刚刚创建的项目之中了

![img13.jpeg](https://i.loli.net/2020/08/18/T567quIBgGSNE8x.jpg)

## 项目结构

项目结构如下图

![img14.png](https://i.loli.net/2020/08/18/PU2S9Bcx4EQfgFl.png)

## 项目的打包

输入下方指令

```
npm run build
```

则会出现一个 dist 文件夹

![img15.png](https://i.loli.net/2020/08/18/RG6gkQP8UaMcWCS.png)

这时候我们部署的就是 这个 dist 文件

以上就是用指令创建一个 vue-cli 的项目即打包

## 通过可视化 ui 创建

当然我们也可以通过可视化 ui 去创建一个项目,敲下方指令，浏览器会弹出一个可视化页面，在这里就不做介绍了

```
vue ui
```
