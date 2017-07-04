---
title: VUE项目搭建
date: 2017-06-29 22:38:21
tags:
---
# 备忘！ 
### vue项目搭建流程<!-- more -->

#### node -v   (查看node的版本，基于4.0以上)。

#### npm install -g vue-cli   (下载脚手架)。

#### vue   (查看是否成功)。

#### vue list (查看vue的组件列表)。

#### vue init webpack PorjectName   (基于webpack环境搭建名字为PorjectName的项目)。

~~~
? Project name (sell)
? Project name sell
? Project description (A Vue.js project) sell app
? Project description sell app
? Author (1348168417 <1348168417@qq.com>)
? Author 1348168417 <1348168417@qq.com>
? Vue build standalone
? Install vue-router? (Y/n) y
? Install vue-router? Yes
? Use ESLint to lint your code? (Y/n)
? Use ESLint to lint your code? Yes
? Pick an ESLint preset (Use arrow keys)
? Pick an ESLint preset Standard
? Setup unit tests with Karma + Mocha? (Y/n) n
? Setup unit tests with Karma + Mocha? No
? Setup e2e tests with Nightwatch? (Y/n) n
? Setup e2e tests with Nightwatch? No

   vue-cli · Generated "sell".

   To get started:

     cd sell
     npm install
     npm run dev

   Documentation can be found at https://vuejs-templates.github.io/webpack
ls
~~~

#### ls   (查看当前目录，可发现有PorjectName)。

#### cnpm install   (下载依赖)。

#### npm run dev   (启动端口默认8080)。

