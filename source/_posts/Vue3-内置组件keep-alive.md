---
title: Vue3-内置组件keep-alive
date: 2022-08-07 16:22:12
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

## 内置组件keep-alive

### 1、 通过路由配置keep-alive判断是否缓存

```ts
import { createRouter, createWebHashHistory } from 'vue-router';
import Home from '../views/Home.vue';//路由预加载
 
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,//路由预加载
    meta: { 
      keepAlive:true, //是否需要缓存
     }
  },
  {
    path: '/about',
    name: 'About',
    component: () => import('@/views/About.vue'),//路由懒加载
    meta: { 
      keepAlive:false, ///是否需要缓存
     }
  }
]
 
const router = createRouter({
  history: createWebHashHistory(),
  routes
})
 
export default router
```

### 2、 作为组件使用

有时候我们不希望组件被重新渲染影响使用体验；或者处于性能考虑，避免多次重复渲染降低性能。而是希望组件可以缓存下来,维持当前的状态。这时候就需要用到`keep-alive`组件。

include 和 exclude 允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示。include和exclude，include是绑定需要缓存的组件，并且要和组件名一致；exclude绑定则是不需要缓存的组件。当两个属性同时使用时exclude权重大于include

```vue
<!-- 缓存A组件，不缓存B组件 -->
<keep-alive :include="'A'" :exclude="'B'">
	<A v-if="show"></A>
  <B v-else></B>
</keep-alive>
```

keep-alive还有一个`max`属性，代表缓存组件最大数量，一旦这个数字达到了，在新实例被创建之前，已缓存组件中最久没有被访问的实例会被销毁掉。

```vue
<keep-alive :max="10">
  <component :is="Component"></component>
</keep-alive>
```

组件中使用keep-alive后生命周期会有变化：

- 初次进入时： `onMounted`> `onActivated`
- 退出后触发 `onDeactivated`
- 再次进入只触发`onActivated`

#### 
