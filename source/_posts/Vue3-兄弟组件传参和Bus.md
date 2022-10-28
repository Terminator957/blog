---
title: Vue3-兄弟组件传参和Bus
date: 2022-10-24 16:15:51
sticky: 18 # 设置文章置顶，越大优先级越高
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## Vue3-兄弟组件传参和Bus

### 1、通过父组件作为传输参数的媒介

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

### 2、Event Bus

