---
title: Vue3-computed计算属性
date: 2022-07-28 16:22:12
sticky: 4 # 设置文章置顶，越大优先级越高
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## computed计算属性

当依赖的属性的值发生变化的时候，才会触发他的更改，如果依赖的值，不发生变化的时候，使用的是缓存中的属性值 

```ts
let $total = ref<number>(0) //定义一个number类型的响应式数据，默认值为0

$total = computed<number>(() => { // computed返回值为number类型
  return obj.reduce((prev, next) => {
    return prev + (next.num * next.price)
  }, 0)
})
```

#### 
