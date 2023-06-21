---
title: Vue3-函数式编程（h函数）
date: 2023-01-11 10:07:42
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

# h函数

h接受三个参数

- type元素的类型
- propsOrChildren 数据对象, 这里主要表示(props, attrs, dom props, class 和 style)
- children子节点

h函数的多种组合方式

```tsx
// 除类型之外的所有参数都是可选的
h('div')
h('div', { id: 'foo' })
 
//属性和属性都可以在道具中使用
//Vue会自动选择正确的分配方式
h('div', { class: 'bar', innerHTML: 'hello' })
 
// props modifiers such as .prop and .attr can be added
// with '.' and `^' prefixes respectively
h('div', { '.name': 'some-name', '^width': '100' })
 
// class 和 style 可以是对象或者数组
h('div', { class: [foo, { bar }], style: { color: 'red' } })
 
// 定义事件需要加on 如 onXxx
h('div', { onClick: () => {} })
 
// 子集可以字符串
h('div', { id: 'foo' }, 'hello')
 
//如果没有props是可以省略props 的
h('div', 'hello')
h('div', [h('span', 'hello')])
 
// 子数组可以包含混合的VNode和字符串
h('div', ['hello', h('span', 'hello')])
```

使用props传递参数，使用emit派发参数

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-01-11 09:53:15
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <!-- 标签上传入参数，定义事件 -->
    <btn text="函数式编程====>h函数" @on-click="getParams"></btn>
  </div>
</template>

<script setup lang="ts">
import { h } from 'vue'
type Props = {
  text:string
}

const btn = (props:Props,ctx:any) => {
  return h('button', {
    class: ['test'],
    onClick: () => {
      ctx.emit('on-click',123) // 将参数派发出去
    }
  },
  props.text, // 接收标签上传入的参数
  )
}
// 自定义接受参数的事件
const getParams = (num:number) => {
  console.log(num);
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

定义插槽

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-01-11 09:59:48
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <btn @on-click="getParams">
      <template #default>
        h函数上定义插槽
      </template>
    </btn>
  </div>
</template>

<script setup lang="ts">
import { h } from 'vue'
type Props = {
  text?:string
}

const btn = (props:Props,ctx:any) => {
  return h('button', {
    class: ['test'],
    onClick: () => {
      ctx.emit('on-click',123) // 将参数派发出去
    }
  },
  ctx.slots.default(),
  )
}

const getParams = (num:number) => {
  console.log(num);
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

