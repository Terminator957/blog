---
title: Pinia-storeParams
date: 2023-04-14 15:27:35
cover: https://bu.dusays.com/2023/04/13/6437c05a82509.png
top_img: https://bu.dusays.com/2022/11/16/6374528071f3b.jpeg
tags:
- Pinia
categories:
- Pinia
---

请注意，`store` 是一个用`reactive` 包裹的对象，这意味着不需要在getter 之后写`.value`，但是，就像`setup` 中的`props` 一样，**我们不能对其进行解构**：

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:44:58
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      <!-- {{ store.bikini }}--{{ store.count }} -->
      {{ count }}
    </h2>
    <button @click="addCount">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
// ❌ 这不起作用，因为它会破坏响应式
// 这和从 props 解构是一样的
let { count } = store;
// 此时count已经不是响应式的了
const addCount = () => {
  count++;
  console.log('count', count);
};
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

为了从 Store 中提取属性同时保持其响应式，您需要使用`storeToRefs()`。 它将为任何响应式属性创建 refs。详情参见[Pinia](https://pinia.web3doc.top/core-concepts/#%E4%BD%BF%E7%94%A8-store)官方文档

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:43:45
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      <!-- {{ store.bikini }}--{{ store.count }} -->
      {{ count }}
    </h2>
    <button @click="addCount">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { storeToRefs } from 'pinia';
import { useStore } from '@/store/index.ts';
const store = useStore();
// `count`是响应式引用
const { count } = storeToRefs(store);
const addCount = () => {
  console.log('count', count);
  count.value++
};
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

storeToRefs其原理跟toRefs 一样的给里面的数据包裹一层toRef

通过toRaw使store变回原始数据防止重复代理

循环store 通过 isRef isReactive 判断 如果是[响应式](https://so.csdn.net/so/search?q=响应式&spm=1001.2101.3001.7020)对象直接拷贝一份给refs 对象 将其原始对象包裹toRef 使其变为响应式对象 

![](https://bu.dusays.com/2023/04/14/643905ee2d946.png)
