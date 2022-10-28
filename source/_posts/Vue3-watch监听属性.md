---
title: Vue3-watch监听属性
date: 2022-07-29 16:22:12
sticky: 5 # 设置文章置顶，越大优先级越高
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## watch监听属性

`watch`一共三个参数

第一个参数：监听的数据源；

```ts
let st = ref({
  nav: {
    tag: {
      name: 'test'
    }
  }
})

watch(st,(newVal,oldVal) => { // 第一个参数可为数组，同时监听多个参数
  console.log('新的', newVal);
  console.log('旧的', oldVal);
})
```

第二个参数：回调函数（newVal,oldVal）newVal变化后的数据，oldVal变化前的数据；

第三个参数：

```ts
{
	immediate:true, // 是否立刻调用一次
  deep:true // 是否开启深度监听
}
```

监听复杂数据类型的响应式数据

```vue
<input type="text" v-model="msg.name">
<input type="text" v-model="msg.foot">

let msg = reactive({
  name: '小汪',
  foot: '鞋子'
})

watch(msg,(newVal,oldVal) => {
  console.log('新的', newVal);
  console.log('旧的', oldVal);
})

// 监听复杂数据类型中的单一属性,第一个参数传入一个回调，回调中返回要监听的属性
watch(() => msg.name,(newVal,oldVal) => {
  console.log('新的', newVal);
  console.log('旧的', oldVal);
})
```

#### 
