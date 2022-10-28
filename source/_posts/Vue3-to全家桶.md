---
title: Vue3-to全家桶
date: 2022-07-27 16:22:12
sticky: 3 # 设置文章置顶，越大优先级越高
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## to全家桶

`toRef`将原始对象中的属性转化为响应式的

```ts
const obj = {
  foo: 1,
  doo: 2
}

const state = toRef(obj, 'foo')

const change = () => {
  state.value++
  console.log('-----原始对象', obj);
  console.log('-----引用对象', state);
}
```

`toRefs`可以帮我们批量创建ref对象主要是方便我们解构使用

```ts
const obj = reactive({
  foo: 1,
  doo: 2
})

let { foo, doo } = toRefs(obj)

const change = () => {
  foo.value++
  doo.value++
  console.log('-----响应式对象', obj);
}
```

`toRaw`将响应式对象转为普通对象
