---
title: Vue3-兄弟组件传参和Bus
date: 2022-10-24 16:15:51
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

# Vue3-兄弟组件传参和Bus

## 1、通过父组件作为传输参数的媒介

父组件通过props向子组件传输自另一个子组件中接收到的参数,操作麻烦，无法直接通信，只能充当桥梁

父组件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-08-31 21:14:30
 * @LastEditTime: 2022-10-24 17:29:06
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <SA @on-click="getParams"></SA>
    <SB :params="params"></SB>
  </div>
</template>

<script setup lang="ts">
import SA from './components/SA.vue'
import SB from '@/layout/content/components/SB.vue'
import { ref } from 'vue'
let params = ref(false)
const getParams = (item: boolean) => {
  params.value = item
}

</script>

<style lang="scss" scoped>
.content {
  position: relative;
  flex: 1;
  height: 100%;
  margin: 20px;
  border: 1px solid #ccc;
  overflow: auto;

}
</style>
```

子组件A

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-24 16:08:13
 * @LastEditTime: 2022-10-24 17:29:49
 * @LastEditors: Do not edit
-->
<template>
    <div class="A">
        <h1>A</h1>
        <button @click="change">change</button>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
let aValue = false
const emit = defineEmits(['on-click'])
const change = () => {
    aValue = !aValue
    emit('on-click', aValue)
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

子组件B

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-24 16:08:19
 * @LastEditTime: 2022-10-24 17:29:14
 * @LastEditors: Do not edit
-->
<template>
    <div class="B">
        <h1>B</h1>
        <div>{{ params}}</div>
    </div>
</template>

<script setup lang="ts">
type Props = {
    params?: boolean
}

defineProps<Props>()
</script>

<style scoped>
.B {
    width: 200px;
    height: 200px;
    background-color: greenyellow;
}
</style>
```

此种方式可以实现兄弟组件传参，但是非常繁琐

## 2、Event Bus

通过自定义事件中心简化传参

```ts
/*
 * @Description: 自定义全局事件中心
 * @Author: xiuji
 * @Date: 2022-11-10 14:50:51
 * @LastEditTime: 2022-11-10 15:25:47
 * @LastEditors: Do not edit
 */

type busClass = {
    on: (name: string, Fn: Function) => void
    emit: (name: string) => void
}

type paramsKey = string | number | symbol

type List = {
    [key: paramsKey]: Array<Function> // 使用对象签名的方式规定类型——对象签名(Object[name])
}

class Bus implements busClass {
    list: List
    constructor() {
        this.list = {}
    }
    on(name: string, callback: Function) {
        // this.list[name]是存在list中数组形式的方法集合，已有直接使用，否则初始化为一个空数组
        let fn: Array<Function> = this.list[name] || []
        fn.push(callback)
        this.list[name] = fn
    }
    emit(name: string, ...args: Array<any>) {
        let eventName: Array<Function> = this.list[name]
        eventName.forEach(fn => {
            fn.apply(this, args)
        })
    }
}

export default new Bus()
```

子组件A导入Bus并通过emit派发事件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-24 16:08:13
 * @LastEditTime: 2022-11-10 15:45:23
 * @LastEditors: Do not edit
-->
<template>
    <div class="A">
        <h1>A</h1>
        <button @click="change">change</button>
    </div>
</template>

<script setup lang="ts">
import Bus from '../../../bus'
let aValue = false
const change = () => {
    aValue = !aValue
    Bus.emit('on-click', aValue)
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

子组件B导入Bus并通过on收事件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-24 16:08:19
 * @LastEditTime: 2022-11-10 15:37:56
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
import Bus from '../../../bus'
let flag = ref(false)
Bus.on('on-click', (params: boolean) => {
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

如此可以省去父组件在兄弟组件传参中的桥梁作用
