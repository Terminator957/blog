---
title: Pinia-$reset、$subscribe
date: 2023-04-17 10:30:13
cover: https://bu.dusays.com/2023/04/13/6437c05a82509.png
top_img: https://bu.dusays.com/2022/11/16/6374528071f3b.jpeg
tags:
- Pinia
categories:
- Pinia
---

# $reset重置

重置store到初始状态

```ts
state: () => {
        return {
            user: <Result> {},
            name: '占位',
        }
    },
```

组件中修改name的状态

```vue
const changeName = () => {
	store.name = '用户名'
}
store.$reset()
```

调用`$reset`可以将state中的所有参数重置回原始状态

# $subscribe订阅

类似于Vuex 的abscribe 只要有state 的变化就会走这个函数

```ts
store.$subscribe((args,state)=>{
   console.log(args,state);
})
```

返回值：

![](https://bu.dusays.com/2023/04/17/643cb2ba66705.png)

在组件卸载之后继续调用请设置第二个参数：

```ts
store.$subscribe((args, state) => {
  console.log(args, state);
}, {
  detached: true,//表示组件卸载之后继续调用
  deep:true,//表示监听对象的所有属性
  immediate:true//表示立即执行一次回调函数
})
```

# $Actions

actions被调用就会走这个函数

```ts
store.$onAction((args) => {
  console.log(args);
},
true//组件卸载之后继续调用
);
```
