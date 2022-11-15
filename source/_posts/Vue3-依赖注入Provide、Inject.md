---
title: Vue3-依赖注入Provide、Inject
date: 2022-08-10 16:22:12
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## 依赖注入Provide / Inject

通常，当我们需要从父组件向子组件传递数据时，我们使用 props。想象一下这样的结构：有一些深度嵌套的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。

官网的解释很让人疑惑，那我翻译下这几句话：

provide 可以在祖先组件中指定我们想要提供给后代组件的数据或方法，而在任何后代组件中，我们都可以使用 inject 来接收 provide 提供的数据或方法。 

![](Vue3-依赖注入Provide、Inject/provide:inject.png)

#### 爷爷级组件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-08-31 21:14:30
 * @LastEditTime: 2022-10-21 15:28:43
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h1>爷爷级</h1>
    <label>
      <input type="radio" v-model="colorVal" value="red" name="color">
      红色
    </label>
    <label>
      <input type="radio" v-model="colorVal" value="blue" name="color">
      蓝色
    </label>
    <label>
      <input type="radio" v-model="colorVal" value="pink" name="color">
      粉色
    </label>
    <div class="box"></div>
    <provideFather></provideFather>
  </div>
</template>

<script setup lang="ts">
import { ref, provide, readonly } from 'vue'
import provideFather from './components/provideFather.vue'
const colorVal = ref<string>('red')
// 使用readonly阻止深层组件对响应式参数的修改
provide('color', readonly(colorVal)) // 向provide中注入值
</script>

<style lang="scss" scoped>
.content {
  position: relative;
  flex: 1;
  height: 100%;
  margin: 20px;
  border: 1px solid #ccc;
  overflow: auto;

  .box {
    width: 50px;
    height: 50px;
    background-color: v-bind(colorVal);
  }
}
</style>
```

#### 父级组件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-11 16:10:03
 * @LastEditTime: 2022-10-11 16:55:39
 * @LastEditors: Do not edit
-->
<template>
    <div class="provideFather">
        <h1>父级</h1>
        <div class="box"></div>
        <provideSon></provideSon>
    </div>
</template>

<script setup lang="ts">
import { inject, Ref } from 'vue'
import provideSon from './provideSon.vue'
const color = inject<Ref<string>>('color')

</script>

<style scoped lang="scss">
.provideFather {
    .box {
        width: 50px;
        height: 50px;
        background-color: v-bind(color);
    }

}
</style>
```

#### 孙子级组件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-10-11 16:10:26
 * @LastEditTime: 2022-10-21 16:44:58
 * @LastEditors: Do not edit
-->
<template>
    <div class="provideSon">
        <h1>儿子</h1>
        <div>
            <button @click="change">change color</button>
        </div>
        <div class="box"></div>
    </div>
</template>

<script setup lang="ts">
import { inject } from 'vue'
import type { Ref } from 'vue'
const color = inject<Ref<string>>('color')
// 深层次子组件中可以直接修改provide注入的值
const change = () => {
    // color?.value = 'green' // 可选链操作符不能够被赋值
    color!.value = 'green' // 非空类型断言一般都是使用!叹号表示。表示可以确定某个标识符是有值的，跳过ts在编译阶段对它的检测
}
</script>

<style scoped lang="scss">
.provideFather {
    .box {
        width: 50px;
        height: 50px;
        background-color: v-bind(color);
    }

}
</style>
```

**使用Provide、Inject可以简化深层组件之间参数传递的步骤**
