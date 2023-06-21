---
title: Vue3-动态组件
date: 2022-08-03 16:22:12
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

## 动态组件

动态组件常用来做tab切换

```vue
<template>
  <div class="content">
    <div class="content-tabs">
      <div v-for="(i, k) in tabList" :key="k" class="content-tabs-box" @click="changeTab(i)">
        {{ i.name }}
      </div>
    </div>
    <component :is="current.comName"></component>
  </div>
</template>

<script setup lang="ts">
import { reactive, markRaw } from 'vue';
import A from '../content/components/A.vue'
import B from '../content/components/B.vue'
import C from '../content/components/C.vue'
type Tabs = {
  name: string,
  comName: any
}

type Com = Pick<Tabs, 'comName'> // Pick从已有范型中选取使用
// proxy代理，reactive会进行响应式数据代理，局部组件本身也会进行响应式组件代理，vue会提示影响性能，使用markRaw取消proxy代理
const tabList = reactive<Tabs[]>([{
  name: '我是A',
  comName: markRaw(A)
}, {
  name: '我是B',
  comName: markRaw(B)
}, {
  name: '我是C',
  comName: markRaw(C)
}])

let current = reactive<Com>({
  comName: tabList[0].comName
})

const changeTab = (i: Com) => {
  current.comName = i.comName
}
</script>

<style lang="scss" scoped>
.content {
  flex: 1;
  height: 100%;
  margin: 20px;
  border: 1px solid #ccc;
  overflow: auto;

  &-tabs {
    display: flex;

    &-box {
      padding: 10px;
      border: 1px solid #ccc;
    }
  }

  &-box {
    padding: 10px;
    border: 1px solid #ccc;
  }
}
</style>
```

#### 
