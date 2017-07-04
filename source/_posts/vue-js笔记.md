---
title: vue.js笔记
date: 2017-05-01 11:29:40
tags:
---
# 每多学一点知识，就少写一行代码。

## 1、Vue.js使用vue-resource实现ajax请求
~~~html
/**
 * Created by gequn06 on 2017/3/20.
 */
import Vue from 'vue/dist/vue.common.js';
import VueResource from 'vue-resource';
Vue.use(VueResource);
new Vue({
    el: '#doctorWrap',
    data: {
        someData : null
    },
    created :function () {
        this.$http.get('/public/js/data.json').then(function(response){
            console.log(response)
            // get body data
            this.someData = response.body.sites;

        });
    }
});
~~~
<!--more-->
## 2、Vue.js引用css
~~~html
<style scoped>
  @import '../assets/hello.css';
</style>
~~~

## 3、过滤器
~~~html
<template>
  <div v-bind:id="msg | filterA | filterB">111</div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      msg: 'thanks'
    }
  },
  filters: {
    filterA: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    },
    filterB: function (value) {
      if (!value) return ''
      value = value.toString();
      return value.slice(0, 1) + value.charAt(2).toUpperCase() + value.slice(2);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
~~~
## 4、Computed Properties and Watchers
~~~html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
</script>
~~~
## 5、component lists rendered with v-for should have explicit keys
~~~html
v-for需要绑定:key

<el-select v-model="value" placeholder="请选择"  class="meeting-duration-select">
    <el-option
            v-for="(item,index) in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
    </el-option>
</el-select>
~~~