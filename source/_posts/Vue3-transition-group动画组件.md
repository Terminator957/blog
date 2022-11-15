---
title: Vue3-transition-group动画组件
date: 2022-08-09 16:22:12
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## transition-group动画组件

`transition-group`用法、属性、生命周期与`transtion`完全一致

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-08-31 21:14:30
 * @LastEditTime: 2022-10-07 15:32:41
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <el-button type="primary" @click="add">add</el-button>
    <el-button type="primary" @click="pop">pop</el-button>
    <div class="group">
      <!-- transition-group包含tag属性，可在tag内新增标签将组件内div包裹起来 -->
      <transition-group enter-active-class="animate__animated animate__rotateIn"
        leave-active-class="animate__animated animate__rotateOut" tag="section">
        <div v-for="i in list" :key="i">{{i}}</div>
      </transition-group>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive } from 'vue';
import 'animate.css'
const list = reactive<number[]>([1, 2, 3, 4, 5, 6, 7])
const add = () => {
  list.push(list.length + 1)
}
const pop = () => {
  list.pop()
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

  .group {
    display: flex;
    flex-wrap: wrap;
    word-break: break-all;

    div {
      margin: 10px;
    }
  }
}
</style>
```

### 1、 列表的移动过度

`<transition-group> `组件还有一个特殊之处。除了进入和离开，它还可以为定位的改变添加动画。只需了解新增的 v-move类就可以使用这个新功能，它会应用在元素改变定位的过程中。像之前的类名一样，它的前缀可以通过 name attribute 来自定义，也可以通过 move-class attribute 手动设置

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-08-31 21:14:30
 * @LastEditTime: 2022-10-09 16:07:41
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <el-button type="primary" @click="shuffle">shuffle</el-button>
    <div class="list">
      <transition-group move-class="move">
        <div v-for="i in list" :key="i.id" class="list-box">{{ i.number }}</div>
      </transition-group>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import _ from 'lodash'
const list = ref(Array.apply(null, { length: 81 } as number[]).map((_, index) => {
  return {
    id: index,
    number: (index % 9) + 1
  }
}))

const shuffle = () => {
  list.value = _.shuffle(list.value)
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

  .list {
    display: flex;
    width: calc(45px * 10 + 9px);
    flex-wrap: wrap;

    .move {
      transition: all 1s;
    }

    &-box {
      width: 25px;
      height: 25px;
      padding: 10px;
      border: 1px solid #ccc;
      display: flex;
      justify-content: center;
      align-items: center;

    }
  }

}
</style>
```

### 2、 状态过渡

Vue 也同样可以给数字 、Svg 背景颜色等添加过度动画，以下是数字变化

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-08-31 21:14:30
 * @LastEditTime: 2022-10-11 15:53:03
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <input v-model="num.current" step="20" type="number">
    <div>
      <div>{{num.turnNumber.toFixed(0)}}</div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, watch } from 'vue';
import gsap from 'gsap';
const num = reactive({
  turnNumber: 0,
  current: 0
})

watch(() => num.current, (newVal) => {
  gsap.to(num, {
    duration: 1,
    turnNumber: newVal
  })
})
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

#### 
