---
layout: post
title: 'reactCSS模块化和引入AntDesign'
date: 2021-02-03 04:25:03 +0800
categories: 所学
tags: react
---

先吐槽一下。上个破班，闲嘛闲的要死，人都要烂掉。又加上等着过年，不知未来在何方哈哈。如今不缺粮草，又不愁冷暖。只是要满足自己对美好品质生活的渴望，最终用自己的方式来真正创造实现人生价值。

不负能量了。最近有在使用 electron + react 自己开发一个客户端应用。首先整个项目是通过 electron-boilerplate 模板来创建的，using Electron, React and Typescript。地址：https://github.com/sindresorhus/electron-boilerplate

ps: 当想在 github 搜学习资源时，可以检索 awesome + [theme] 比如 awesome JavaScript, awesome Vue, etc.

基于这个模板创建的项目,其中 react 的 webpack 配置文件是 webpack/react.webpack.js ，最初好像都没配置 css-loader，更别提什么 css 模块化了...幸好有点 webpack 基础和之前也比较系统的了解学习了 react。

css 模块化：随着 react、vue 等基于模块化框架的普及，我们通常会将页面拆分为多个小组件，然后将多个组件拼接组成最终程序呈现的页面。但是如果页面中两个组件使用了相同的类名，后者的样式会把前者的覆盖掉，造成样式的命名冲突。所以就出现了 css 模块化的概念。vue 是我们在组件中写样式的时候加上 scoped 就好了，但是 react 通常要自己进行配置。CSS 模块化使得我们可以向 import js 一样来引用我们的 css 代码，代码中的每一个类名都是引入对象的一个属性。通过这种方式，在使用时明确指定所引用的 css 样式。在打包的时候自动地将类名转换为 hash 值，完全杜绝 css 类名冲突的问题。

css 命名规范 BEM：BEM，块 block，元素 element，修饰符 modifier。由 Yandex 团队提出的一种前端命名方法论，使得 css 类对其他开发者来说更加透明且更有意义。

{% highlight css %}

/* 块通常是指 Web 应用开发中的组件或模块.每个块在逻辑和功能上都是相互独立的*/

.block{}

/* 元素是块中的组成部分.元素不能离开块使用,且 BEM 不推荐在元素中嵌套其他元素*/

.block__element{}

/* 修饰符是用来定义块或元素的外观和行为.同样的块在应用不同的修饰符之后,会有不同的外观*/

.block--modifier{}

{% endhighlight %}

饿了么团队开源的 element-ui 组件库的 css 类命名采用的就是此规范。而蚂蚁金服的 ant-design 好像并没有哈哈。之后自己也将一直遵守这样的规范，逐渐养成自己的一整套规范！开发就是要规范化，争取不写垃圾代码。

<div align=center>
  <img src="{{site.baseurl}}/assets/res/02020101.jpg" width="800" alt="elementUICSS命名"/>
</div>

配置 css 模块化：

1. 首先 npm install --save-dev style-loader css-loader

   - style-loader 是通过 JS 脚本创建一个 style 标签，里面包含样式。但是并不能单独使用，因为它不负责解析 css 之前的依赖关系，所以还需安装 css-loader。
   - webpack 使用 js 写的，运行在 node 环境下，打包的时候只会处理 js 之间的依赖关系。所以处理 css 文件需要引入 css-loader 来识别，通过特定的语法规则进行内容转换最后导出。

2. 修改 webpack 配置项，这里的 webpack 版本是 4.42.1。
   {% highlight js %}
   module.exports = {
   module: {
   rules: [
   ​ {
   ​ test: /\.css$/,
   ​ use: [
   ​ 'style-loader',
   ​ // 开启模块化并自定义 hash 名称
   ​ {loader:'css-loader',options:{modules:{localIdentName:'[path][name]\_\_[local]--[hash:base64:5]'}}},
   ​ ],
   ​ // 排除 node_modules 文件夹
   ​ exclude: path.resolve(rootPath,'node_modules'),
   },
   ]
   }
   {% endhighlight %}

3. 定义 css 文件。
   {% highlight css %}
   .className {
   color: green;
   }
   /_ 编写全局样式 _/
   :global(.className) {
   color: red;
   }
   {% endhighlight %}

4. 当然还可以引入 less-loader 等 css 预处理器，同样开启模块化。这边给出我在项目中的配置。

   {% highlight js %}
   module.exports = {
   module: {
   rules: [
   ​ {
   ​ // CSS 全局处理
   ​ test: /\.css$/,
   ​ use: [
   ​ 'style-loader',
   ​ 'css-loader'
   ​ ],
   ​ exclude: path.resolve(rootPath,'node_modules'),

   },
   {
   ​ // less 模块化处理
   ​ test: /\.less$/,
   ​ exclude: path.resolve(rootPath,'node_modules'),
   ​ use: [
   ​ 'style-loader',
   ​ {loader:'css-loader',options:{modules:{localIdentName:'[local]-[hash:5]'}}},
   ​ 'less-loader'
   ​ ]
   }
   ]
   }
   {% endhighlight %}

最后引入 AntDesign。项目地址https://ant-design.gitee.io/docs/react/introduce-cn。

首先 npm i antd -S 安装，然后在项目入口文件中**引入 antd 样式 import '~antd/dist/antd.css'**。当然前提是你的 webpack 支持解析 css 文件。

如果优雅的使用该组件库并结合 ts 写 react 项目，可以参考https://github.com/ant-design/ant-design-pro

css 的发展也应该进行到了可以使用 js 语言写 css，同样可以实现 css 模块化，比较好的方案是 styled-components。不过我暂时没法接受这样的写法，，感觉开发效率也不高。

本篇博客想记录的就是这些吧，写的有点匆忙。不过通过写博客，对自己学习的帮助真的很大！同时要强迫自己继续坚持和养成写博客的好习惯。

原文地址：<a href="https://zzfd.github.io/2021/02/02/reactCSS模块化和引入AntDesign">reactCSS 模块化和引入 AntDesign</a>

参考资料：如有侵权，请告知，将第一时间删除部分内容。

https://zhuanlan.zhihu.com/p/100133524

https://blog.csdn.net/wu_xianqiang/article/details/104560613

https://github.com/ant-design/ant-design-pro

https://github.com/sindresorhus/electron-boilerplate
