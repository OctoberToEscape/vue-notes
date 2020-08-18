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
