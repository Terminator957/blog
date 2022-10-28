---
title: Vue3-ref全家桶
date: 2022-07-25 16:22:12
sticky: 1 # 设置文章置顶，越大优先级越高
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## ref全家桶

vue3通过ref将view中的数据绑定为响应式数据

### 1、Ref,ref

被ref包装之后需要.value 来进行赋值

```ts
import { ref, Ref } from 'vue'
写法1
let msg = ref<string>('红色')
const change = () => {
   msg.value = '绿色'
   console.log(msg);
}
写法2 Ref范型
let msg: Ref<string> = ref('大红色')
const change = () => {
  msg.value = '大绿色'
  console.log(msg);
}
```

### 2、isRef

判断是不是一个ref对象

```ts
let msg: Ref<string | number> = ref('大红色')
let num: number = 123
const change = () => {
  msg.value = '大绿色'
  console.log(isRef(msg)); // true
  console.log(isRef(num)); // false
}
```

### 3、shallowRef和triggerRef

创建一个跟踪自身.value变化的ref，但不会使其指也变成响应式的,可以和triggerRef配合使用，节省性能

triggerRef强制更新页面DOM

```ts
let msg = shallowRef({
  name: '红色'
})

const change = () => {
  msg.value.name = '绿色'
  triggerRef(msg)
}
```

### 4、customRef

`customRef` 是个工厂函数要求我们返回一个对象 并且实现 get 和 set

```ts
function myRef<T>(value: T) {
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return value
      },
      set(newVal: T) {
        console.log('set');
        value = newVal
        trigger()
      }
    }
  })
}
let msg = myRef('红色')
const change = () => {
  msg.value = '绿色'
}
```

#### 
