---
title: Vue3-父子组件传参
date: 2022-08-01 16:22:12
cover: /img/vue3.jpeg
top_img: /img/galaxy.jpg
tags:
- Vue3
categories:
- Web
---

## 父子组件传参

子组件中直接使用`defineProps`  来默认接收父组件传过来的参数

```ts
type Props = {
  name:string,
  content:string
}
defineProps<Props>() // 范型中定义的属性可以直接在子组件中使用
```

`withDefault`方法定义默认props参数,withDefault方法接收两个参数，第一个参数固定defineProps<Props>(),第二个参数是个对象，定义默认参数

```ts
<script setup lang="ts">
  type Props = {
    name?:string,
    content?:string,
    list?: () => [] // 复杂数据类型需要通过函数方式return出去，否则控制台会报错
  }
  withDefault(defineProps<Props>(),{
    name:'我是默认name',
    content:'我是默认content'
  })
</script>
```

`defineEmits`事件派发，子组件向父组件传递参数,子组件中定义事件

```ts
// 子组件
<script setup lang="ts">
  import { ref } from 'vue'
  const data = ref<number>(0)
  const emit = defineEmit(['on-click'])
  emit('on-click',data)
</script>
```

父组件绑定自定义事件并定义接收参数的方法

```vue
<Card @on-click="getItem"></Card>
<script setup lang="ts">
  const getItem = (i) => {
    console.log(i,'来自子组件的参数')
  }
</script>
```

#### 
