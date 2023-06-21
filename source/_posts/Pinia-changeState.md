---
title: Pinia-changeState
date: 2023-04-13 17:12:55
cover: https://bu.dusays.com/2023/04/13/6437c05a82509.png
top_img: https://bu.dusays.com/2022/11/16/6374528071f3b.jpeg
tags:
- Pinia
categories:
- Pinia
---

# State 是允许直接修改值的

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:02:36
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    {{ store.count }}
    <!-- store中参数已经是响应式数据 -->
    <button @click="store.count++">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
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

# 批量修改State的值

可以调用 `$patch` 方法。 它允许您使用部分“state”对象同时应用多个更改：

```ts
/*
 * @Description: store
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-14 15:09:05
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'
export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            bikini: 'bikini',
            count: 0,
        }
    }
})
```

组件中修改

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:11:33
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      {{ store.bikini }}--{{ store.count }}
    </h2>
    <button @click="addCount">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
const addCount = () => {
  store.$patch({
    count: store.count + 1,
    bikini: 'bikini1'
  })
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

# 批量修改函数形式

**推荐使用函数形式 可以自定义修改逻辑**

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:15:52
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      {{ store.bikini }}--{{ store.count }}
    </h2>
    <button @click="addCount">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
const addCount = () => {
  store.$patch((state) => {
    state.count++;
    store.bikini = 'bikini swim-suits';
  })
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

# 通过原始对象修改整个实例

可以通过`$state`将store的属性设置为新对象来替换store的整个状态

缺点就是必须修改整个对象的所有属性

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:18:33
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      {{ store.bikini }}--{{ store.count }}
    </h2>
    <button @click="addCount">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
const addCount = () => {
  store.$state = {
    count: store.count + 1,
    bikini: 'bikini beauty',
  }
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

# 通过actions修改

定义actions，在actions中直接使用this就可以指到state里面的值

```ts
/*
 * @Description: store
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-14 15:09:05
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'
export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            bikini: 'bikini',
            count: 0,
        }
    },
    // methods
    actions: {
        increment() {
            this.count++
        }
    }
})
```

组件中直接调用actions里定义的方法

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 15:21:00
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      {{ store.bikini }}--{{ store.count }}
    </h2>
    <button @click="addCount">addCount</button>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
// 直接调用store中action里定义的方法
const addCount = () => {
  store.increment();
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

