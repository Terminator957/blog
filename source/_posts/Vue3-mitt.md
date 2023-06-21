---
title: Vue3-mitt
date: 2022-11-10 15:51:10
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

# Mitt

在vue3中$on，$off 和 $once 实例方法已被移除，组件实例不再实现事件触发接口，EventBus无法使用了，但可以使用Mitt库代替EventBus

## 1、安装mitt

```bash
pnpm install mitt -S
```

## 2、main.ts全局挂载mitt库

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-07-12 16:30:05
 * @LastEditTime: 2022-11-10 16:12:19
 * @LastEditors: Do not edit
 */
import { createApp } from 'vue'
import App from './App.vue'
import Mitt from 'mitt'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import './assets/reset.scss'
import Card from './components/Card/index.vue'
const mit = Mitt()
// TypeScript注册，必须拓展comComponentCustomProperties类型才能获得类型提示
declare module 'vue' {
    export interface comComponentCustomProperties {
        $Bus: typeof mit
    }
}
const app = createApp(App)
// Vue3全局挂载API
app.config.globalProperties.$Bus = mit
app.use(ElementPlus).component('Card', Card).mount('#app')

```

## 3、组件中使用

mitt库使用emit派发事件，on方法添加事件，off方法移除事件，clear方法清空所有

### A组件派发

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-24 16:08:13
 * @LastEditTime: 2022-11-11 15:55:56
 * @LastEditors: Do not edit
-->
<template>
    <div class="A">
        <h1>A</h1>
        <button @click="change">change</button>
    </div>
</template>

<script setup lang="ts">
import { getCurrentInstance } from 'vue'
const instance = getCurrentInstance()
let aValue = false
const change = () => {
    aValue = !aValue
    instance?.proxy?.EventBus.emit('on-click', aValue)
    instance?.proxy?.EventBus.emit('on-test', aValue)
}
</script>

<style scoped>
.A {
    width: 200px;
    height: 200px;
    background-color: aquamarine;
}
</style>
```

## B组件接收

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-24 16:08:19
 * @LastEditTime: 2022-11-11 15:48:08
 * @LastEditors: Do not edit
-->
<template>
    <div class="B">
        <h1>B</h1>
        <div>{{ flag }}</div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { getCurrentInstance } from 'vue'
const instance = getCurrentInstance()
let flag = ref(false)
instance?.proxy?.EventBus.on('on-click', (params: any) => {
    console.log(params, '=======>B');
    flag.value = params
})
</script>

<style scoped>
.B {
    width: 200px;
    height: 200px;
    background-color: greenyellow;
}
</style>
```

## 监听所有事件（ on("*") ）

```ts
instance?.proxy?.$Bus.on('*',(type,num)=>{
    console.log(type,num,'===========>B')
})
```

## 移除监听事件（off）

```ts
const Fn = (num: any) => {
    console.log(num, '===========>B')
}
instance?.proxy?.$Bus.on('on-num',Fn)//listen
instance?.proxy?.$Bus.off('on-num',Fn)//unListen
```

## 清空所有监听（clear）

```ts
instance?.proxy?.$Bus.all.clear()
```

