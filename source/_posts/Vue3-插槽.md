---
title: Vue3-插槽
date: 2022-08-04 16:22:12
sticky: 11 # 设置文章置顶，越大优先级越高
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## 插槽

子组件中通过`slot`标签定义插槽插入的位置,slot标签相当于一个占位符；

插槽分为匿名插槽、具名插槽、动态插槽、作用域插槽

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-09-07 17:49:20
 * @LastEditTime: 2022-09-12 16:06:36
 * @LastEditors: Do not edit
-->
<template>
    <div class="slot">
        <header class="header">
            <!-- slot标签相当于一个占位符 -->
            <slot name="header"></slot> // 具名插槽
        </header>
        <main class="main">
            <!-- slot标签相当于一个占位符 -->
            <slot></slot> // 匿名插槽
        </main>
        <footer class="footer">
            <!-- slot标签相当于一个占位符 -->
            <slot name="footer"></slot>
        </footer>

    </div>
</template>

<script setup lang="ts">

</script>

<style lang="scss" scoped>
.header {
    height: 200px;
    background-color: aqua;
}

.main {
    height: 300px;
    background-color: bisque;
}

.footer {
    height: 200px;
    background-color: cadetblue;
}
</style>
```

父组件中使用插槽：

1.引入组件

2.使用插槽:在template标签中编写需要插入组件的内容即可

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-08-31 21:14:30
 * @LastEditTime: 2022-09-12 16:06:03
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <Slot>
    	<template #[name]> // 动态插槽，插槽可以是一个变量名
        <div>
          我被插入了上面
        </div>
      </template>
      <template #default> // 匿名插槽，可用v-slot或#default
        <div>
          我被插入了中间
        </div>
      </template>
      <template #footer> // 具名插槽，可用#{name}或v-slot：name
        <div>
          我被插入了下面
        </div>
      </template>
    </Slot>
  </div>
</template>

<script setup lang="ts">
import Slot from '../../components/Slot/index.vue'
import { ref } from 'vue'
const name = ref('header')
const changeSlot = () => {
  name.value = 'footer'
}
</script>
```

作用域插槽

子组件动态绑定参数，派发给父组件的slot去使用

```vue
<main class="main">
  <!-- slot标签相当于一个占位符 -->
  <div>
    <div v-for="(i,k) in 100" :key="k">
      <slot :data="i" :length="k"></slot>
    </div>
  </div>
</main>
```

父组件通过结构方式取值，可传多个参数

```vue
<template #default="{data,length}">
	<div>
    {{data}}---{{length}}
  </div>
</template>
```

#### 
