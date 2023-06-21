---
title: vue-router
date: 2023-04-25 10:40:13
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web

---

# 起步

项目中安装router

<span style="color:red;">Vue3安装router4版本</span>
<span style="color:red;">Vue2安装router3版本</span>

```bash
pnpm i vue-router -S
```

项目src目录下新建router文件夹，新建index.ts

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-04-25 10:57:56
 * @LastEditTime: 2023-04-25 16:11:59
 * @LastEditors: Do not edit
 */
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'

const routes = <RouteRecordRaw[]> [
    {
        path: "/",
        name: "Home",
        component: () => import("@/layout/login/index.vue")
    },
    {
        path: '/register',
        name: 'Register',
        component: () => import('@/layout/register/index.vue')
    }
]

const router = createRouter(
    {
        history: createWebHistory(),
        routes
    }
)

export default router
```

main.ts挂载路由index.ts

```ts
import router from '@/router/index'
```

组件中通过**router-link**使用定义的路由,router-link使得 Vue Router 可以在不重新加载页面的情况下更改 URL，处理 URL 的生成以及编码。

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-07-12 16:30:05
 * @LastEditTime: 2023-05-10 13:57:05
 * @LastEditors: Do not edit
-->
<template>
 	<!--使用 router-link 组件进行导航 -->
  <!--通过传递 `to` 来指定链接 -->
  <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
  <router-link to="/">login</router-link>
  <router-link to="/register">register</router-link>
  <hr>
	<!-- 路由出口 -->
	<!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</template>

<script setup lang="ts">
// vue版本^3.2.25，使用router-link，router-view需要使用import导入
import { RouterView, RouterLink } from "vue-router";
</script>

<style lang="scss">
html,
body,
#app {
  height: 100%;
  overflow: hidden;
}
</style>
```

# 命名路由-编程式导航

## 命名路由

除了 `path` 之外，你还可以为任何路由提供 `name`。这有以下优点：

- 没有硬编码的 URL
- `params` 的自动编码/解码。
- 防止你在 url 中出现打字错误。
- 绕过路径排序（如显示一个）

router-link使用name属性进行跳转需要改变，变为对象并且有对应的name

```vue
<router-link :to="{ name: 'Register' }">register</router-link>
```

## 编程式导航

除了使用 `<router-link>` 创建 a 标签来定义导航链接，还可以借助 router 的实例方法，通过编写代码来实现。

1. 字符串模式

   ```ts
   import { useRouter } from 'vue-router'
   const router = useRouter()
   
   const toPage = () => {
     router.push('/')
   }
   ```

2. 对象模式

   ```ts
   import { useRouter } from 'vue-router'
   const router = useRouter()
    
   const toPage = () => {
     router.push({
       path: '/'
     })
   }
   ```

3. 命名式路由模式

   ```ts
   import { useRouter } from 'vue-router'
   const router = useRouter()
    
   const toPage = () => {
     router.push({
       name: 'Home'
     })
   }
   ```

## a标签跳转

直接通过a href也可以跳转但是会刷新页面

```html
 <a href="/">toLogin</a>
```

# 历史记录

## replace替换当前位置

采用replace进行页面的跳转会同样也会创建渲染新的Vue组件，但是在history中其不会重复保存记录，而是替换原有的vue组件。它的作用类似于 `router.push`，唯一不同的是，它在导航时不会向 history 添加新记录，正如它的名字所暗示的那样——它取代了当前的条目。

声明式：

```vue
<router-link :to="..." replace>
```

编程式:

也可以直接在传递给 `router.push` 的 `routeLocation` 中增加一个属性 `replace: true` ：

```ts
import { useRouter } from 'vue-router'
const router = useRouter()

router.push({ path: '/home', replace: true })
// 相当于
router.replace({ path: '/home' })
```

## 横跨历史

该方法采用一个整数作为参数，表示在历史堆栈中前进或后退多少步，类似于 `window.history.go(n)`。

```ts
import { useRouter } from 'vue-router'
const router = useRouter()

// 向前移动一条记录，与 router.forward() 相同
router.go(1)

// 返回一条记录，与 router.back() 相同
router.go(-1)

// 前进 3 条记录
router.go(3)

// 如果没有那么多记录，静默失败
router.go(-100)
router.go(100)
```

# 路由传参

## query路由传参

query传参必须配合path使用，编程式导航，使用router push或者repalce的时候，改为对象形式新增query必须传入一个对象。

```ts
import { useRouter } from 'vue-router'
const router = useRouter()
router.push({
  path:'/register',
  query:{
    id:1,
    type:'change'
  }
})
```

接收路由参数

```ts
import { useRoute } from 'vue-router'
const route = useRoute()
console.log(route.query) // { id:1, type:'change' }
```

## params路由传参

params传参必须配合name使用，parmas传递参数的时候，不会在地址栏展示。

```ts
import { useRouter } from 'vue-router'
const router = useRouter()

router.push({
    name:'你路由中的name',
    params:'是一个对象'
})
```

接收路由参数

```ts
import { useRoute } from 'vue-router'
const route = useRoute()

console.log(route.params) // { id:1, type:'change' }
```

params传参是通过内存来传递参数的，所以刷新页面参数肯定会丢失。可以通过动态路由传递参数来解决

## 动态路由传参

很多时候，需要将给定匹配模式的路由映射到同一个组件。例如，可能有一个 `User` 组件，它应该对所有用户进行渲染，但用户 ID 不同。在 Vue Router 中，可以在路径中使用一个动态字段来实现，称之为 *路径参数* ：

```ts
const routes = [
  // 动态字段以冒号开始
  { path: '/users/:id', component: User },
]
```

现在像 `/users/123` 和 `/users/321` 这样的 URL 都会映射到同一个路由。

*路径参数* 用冒号 `:` 表示。当一个路由被匹配时，它的 *params* 的值将在每个组件中以 `$route.params` 的形式暴露出来。因此，可以通过更新 `User` 的模板来呈现当前的用户 ID：

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>',
}
```

你可以在同一个路由中设置有多个 *路径参数*，它们会映射到 `$route.params` 上的相应字段。例如：

| 匹配模式                       | 匹配路径                 | $route.params                            |
| ------------------------------ | ------------------------ | ---------------------------------------- |
| /users/:username               | /users/eduardo           | `{ username: 'eduardo' }`                |
| /users/:username/posts/:postId | /users/eduardo/posts/123 | `{ username: 'eduardo', postId: '123' }` |

<span style="color:red;">Path与Params区别</span>

1. query 传参配置的是 path，而 params 传参配置的是name，在 params中配置 path 无效
2. query 在路由配置不需要设置参数，而 params 必须设置
3. query 传递的参数会显示在地址栏中
4. [params传参](https://so.csdn.net/so/search?q=params传参&spm=1001.2101.3001.7020)刷新会无效，但是 query 会保存传递过来的值，刷新不变 

# 嵌套路由

一些应用程序的 UI 由多层嵌套的组件组成。在这种情况下，URL 的片段通常对应于特定的嵌套组件结构，例如：

```
/user/johnny/profile                     /user/johnny/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

通过 Vue Router，可以使用嵌套路由配置来表达这种关系。

```html
<div id="app">
  <router-view></router-view>
</div>
```

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>',
}

// 这些都会传递给 `createRouter`
const routes = [{ path: '/user/:id', component: User }]
```

这里的 `<router-view>` 是一个顶层的 `router-view`。它渲染顶层路由匹配的组件。同样地，一个被渲染的组件也可以包含自己嵌套的 `<router-view>`。例如，可以在 `User` 组件的模板内添加一个 `<router-view>`：

```js
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `,
}
```

要将组件渲染到这个嵌套的 `router-view` 中，需要在路由中配置 `children`：

```js
const routes = [
  {
    path: '/user/:id',
    component: User,
    children: [
      {
        // 当 /user/:id/profile 匹配成功
        // UserProfile 将被渲染到 User 的 <router-view> 内部
        path: 'profile',
        component: UserProfile,
      },
      {
        // 当 /user/:id/posts 匹配成功
        // UserPosts 将被渲染到 User 的 <router-view> 内部
        path: 'posts',
        component: UserPosts,
      },
    ],
  },
]
```

**注意，以 `/` 开头的嵌套路径将被视为根路径。这允许你利用组件嵌套，而不必使用嵌套的 URL。**

如你所见，`children` 配置只是另一个路由数组，就像 `routes` 本身一样。因此，你可以根据自己的需要，不断地嵌套视图。

## 嵌套的命名路由

```js
const routes = [
  {
    path: '/user/:id',
    // 父路由不定义名称
    component: User,
    // 请注意，只有子路由具有名称
    children: [{ path: '', name: 'user', component: UserHome }],
  },
]
```

这将确保导航到 `/user/:id` 时始终显示嵌套路由。

在一些场景中，你可能希望导航到命名路由而不导航到嵌套路由。例如，你想导航 `/user/:id` 而不显示嵌套路由。那样的话，你还可以**命名父路由**，但请注意**重新加载页面将始终显示嵌套的子路由**，因为它被视为指向路径`/users/:id` 的导航，而不是命名路由：

```js
const routes = [
  {
    path: '/user/:id',
    name: 'user-parent',
    component: User,
    children: [{ path: '', name: 'user', component: UserHome }],
  },
]
```

# 命名视图

想同时 (同级) 展示多个视图，而不是嵌套展示，例如创建一个布局，有 `sidebar` (侧导航) 和 `main` (主内容) 两个视图，这个时候命名视图就派上用场了。可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 `router-view` 没有设置名字，那么默认为 `default`。

```html
<router-view class="view left-sidebar" name="AAA"></router-view>
<router-view class="view main-content"></router-view>
<router-view class="view right-sidebar" name="BBB"></router-view>
```

一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 `components` 配置 (带上 **s**)：

```js
import AAA from"@/layout/AAA.vue"
import BBB from"@/layout/BBB.vue"
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/',
      components: {
        default: Home,
        AAA,
        BBB,
      },
    },
  ],
})
```

# 重定向和别名

重定向通过 `routes` 配置来完成，下面例子是从 `/home` 重定向到 `/`：

```js
const routes = [{ path: '/home', redirect: '/' }]
```

重定向的目标也可以是一个命名的路由：

```js
const routes = [{ path: '/home', redirect: { name: 'homepage' } }]
```

甚至是一个方法，动态返回重定向目标：

```js
const routes = [
  {
    // /search/screens -> /search?q=screens
    path: '/search/:searchText',
    redirect: to => {
      // 方法接收目标路由作为参数
      // return 重定向的字符串路径/路径对象
      return { path: '/search', query: { q: to.params.searchText } }
    },
  },
  {
    path: '/search',
    // ...
  },
]
```

请注意，**[导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)并没有应用在跳转路由上，而仅仅应用在其目标上**。在上面的例子中，在 `/home` 路由中添加 `beforeEnter` 守卫不会有任何效果。

在写 `redirect` 的时候，可以省略 `component` 配置，因为它从来没有被直接访问过，所以没有组件要渲染。唯一的例外是[嵌套路由](https://router.vuejs.org/zh/guide/essentials/nested-routes.html)：如果一个路由记录有 `children` 和 `redirect` 属性，它也应该有 `component` 属性。

## 相对重定向

可以重定向到相对位置：

```js
const routes = [
  {
    // 将总是把/users/123/posts重定向到/users/123/profile。
    path: '/users/:id/posts',
    redirect: to => {
      // 该函数接收目标路由作为参数
      // 相对位置不以`/`开头
      // 或 { path: 'profile'}
      return 'profile'
    },
  },
]
```

## 别名alias

**将 `/` 别名为 `/home`，意味着当用户访问 `/home` 时，URL 仍然是 `/home`，但会被匹配为用户正在访问 `/`。**

```js
const routes = [{ path: '/', component: Homepage, alias: '/home' }]
```

通过别名，你可以自由地将 UI 结构映射到一个任意的 URL，而不受配置的嵌套结构的限制。使别名以 `/` 开头，以使嵌套路径中的路径成为绝对路径。你甚至可以将两者结合起来，用一个数组提供多个别名:

```js
const routes = [
  {
    path: '/users',
    component: UsersLayout,
    children: [
      // 访问下列 3 个 URL时都 呈现 UserList
      // - /users
      // - /users/list
      // - /people
      { path: '', component: UserList, alias: ['/people', 'list'] },
    ],
  },
]
```

如果路由有参数，请确保在任何绝对别名中包含它们：

```js
const routes = [
  {
    path: '/users/:id',
    component: UsersByIdLayout,
    children: [
      // 访问下列 3 个 URL 呈现 UserDetails
      // - /users/24
      // - /users/24/profile
      // - /24
      { path: 'profile', component: UserDetails, alias: ['/:id', ''] },
    ],
  },
]
```

# 导航守卫

vue-router 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。这里有很多方式植入路由导航中：全局的，单个路由独享的，或者组件级的。

## 全局前置守卫

使用 `router.beforeEach` 注册一个全局前置守卫：

```js
const router = createRouter({ ... })

router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消导航
  return false
})
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于**等待中**。

每个守卫方法接收三个参数：

```js
to: Route， 即将要进入的目标 路由对象；
from: Route，当前导航正要离开的路由；
next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。
next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。
```

案例：权限判断

```js
const whileList = ['/']
 
router.beforeEach((to, from, next) => {
    let token = localStorage.getItem('token')
    //白名单 有值 或者登陆过存储了token信息可以跳转 否则就去登录页面
    if (whileList.includes(to.path) || token) {
        next()
    } else {
        next({
            path:'/'
        })
    }
})
```

如果遇到了意料之外的情况，可能会抛出一个 `Error`。这会取消导航并且调用 [`router.onError()`](https://router.vuejs.org/zh/api/#onerror) 注册过的回调。

如果什么都没有，`undefined` 或返回 `true`，**则导航是有效的**，并调用下一个导航守卫

以上所有都同 **`async` 函数** 和 Promise 工作方式一样：

```js
router.beforeEach(async (to, from) => {
  // canUserAccess() 返回 `true` 或 `false`
  const canAccess = await canUserAccess(to)
  if (!canAccess) return '/login'
})
```

## 全局路由后置钩子

全局后置钩子，然而和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身：

它们对于分析、更改页面标题、声明页面等辅助功能以及许多其他事情都很有用。

使用场景一般可以用来做loadingBar

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-06-21 09:36:39
 * @LastEditTime: 2023-06-21 14:33:28
 * @LastEditors: Do not edit
-->
<template>
    <div class="loading_bar">
        <div class="bar" ref="bar">
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
let process = ref<number>(1);
let bar = ref<HTMLElement>();
let timer = ref<number>(0);
const startLoading = () => {
    let dom = bar.value as HTMLElement;
    process.value = 1;
    timer.value = window.requestAnimationFrame(function go() {
        if (process.value < 90) {
            process.value += 1;
            dom.style.width = process.value + '%';
            dom.style.transition = 'width 0.5s';
            timer.value = window.requestAnimationFrame(go); // 递归调用go方法
        } else {
            process.value = 1;
            window.cancelAnimationFrame(timer.value);
        }
    });
};

const endLoading = () => {
    let dom = bar.value as HTMLElement;

    window.requestAnimationFrame(() => {
        process.value = 100;
        dom.style.width = process.value + '%';
        dom.style.opacity = '0';
        dom.style.transition = 'opacity 1.5s';
    });
};

defineExpose({
    startLoading,
    endLoading
});
</script>

<style scoped lang="scss">
.loading_bar {
    position: fixed;
    top: 0;
    width: 100%;
    height: 2px;

    .bar {
        height: inherit; // 继承父元素的高度
        width: 0;
        background-color: #409eff;
    }
}
</style>
```

在前置守卫中调用组件的startLoading方法，后置守卫中调用endLoading方法

```ts
import { createVNode, render } from 'vue'
import router from '@/router/index'
import ProcessBar from '@/components/loadingBar/index.vue'

const LoadingBar = createVNode(ProcessBar)
render(LoadingBar, document.body)

router.beforeEach((to, from, next) => {
    if (whiteList.includes(to.path)) {
        LoadingBar.component?.exposed?.startLoading()
        next()
    }
})

router.afterEach((to, from) => {
    LoadingBar.component?.exposed?.endLoading()
})
```

# 路由元信息

通过接收属性对象的`meta`属性来实现过渡名称、谁可以访问路由等功能，并且它可以在路由地址和导航守卫上都被访问到。

使用路由元信息可以在路由中附加自定义的数据，例如：

- 权限校验标识。
- 路由组件的过渡名称。
- 路由组件持久化缓存 (keep-alive) 的相关配置。
- 标题名称

可以在**导航守卫**或者是**路由对象**中访问路由的元信息数据。

```ts
import { createRouter, createWebHistory, RouteRecordRaw } from 'vue-router'

// 使用TS扩展 如果不使用扩展 title将会是unknow 类型
declare module 'vue-router' {
    interface RouteMeta {
        title: string
    }
}

const routes = <RouteRecordRaw[]> [
    {
        path: "/",
        name: "Home",
        component: () => import("@/layout/login/index.vue"),
        meta: {
            title: '登录'
        }
    },
    {
        path: '/register',
        name: 'Register',
        component: () => import('@/layout/register/index.vue'),
        meta: {
            title: '注册'
        }
    }
]

const router = createRouter(
    {
        history: createWebHistory(),
        routes
    }
)

export default router
```

路由前置守卫中使用路由元信息修改页面标题

```ts
const whiteList = ['/', '/register']

router.beforeEach((to, from, next) => {
    LoadingBar.component?.exposed?.startLoading()
    document.title = to.meta.title
    if (whiteList.includes(to.path)) {
        next()
    }
})
```

# 路由过渡动效

想要在你的路径组件上使用转场，并对导航进行动画处理，你需要使用 [v-slot API](https://router.vuejs.org/zh/api/#router-view-s-v-slot)：

```html
<router-view #default="{ route, Component }">
    <transition :enter-active-class="`animate__animated ${route.meta.transitionName}`">
      <component :is="Component"></component>
    </transition>
  </router-view>
```

## 单个路由的过渡

上面的用法会对所有的路由使用相同的过渡。如果你想让每个路由的组件有不同的过渡，你可以将[元信息](https://router.vuejs.org/zh/guide/advanced/meta.html)和动态的 `name` 结合在一起，放在`<transitionName>` 上：

```js
const routes = <RouteRecordRaw[]> [
    {
        path: "/",
        name: "Home",
        component: () => import("@/layout/login/index.vue"),
        meta: {
            title: '登录',
            transitionName: 'animate__wobble'
        }
    },
    {
        path: '/register',
        name: 'Register',
        component: () => import('@/layout/register/index.vue'),
        meta: {
            title: '注册',
            transitionName: 'animate__bounce'
        }
    }
]
```

## 基于路由的动态过渡

也可以根据目标路由和当前路由之间的关系，动态地确定使用的过渡。使用和刚才非常相似的片段：

```html
<!-- 使用动态过渡名称 -->
<router-view v-slot="{ Component, route }">
  <transition :enter-active-class="route.meta.transitionName">
    <component :is="Component" />
  </transition>
</router-view>
```

可以添加一个 [after navigation hook](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#全局后置钩子)，根据路径的深度动态添加信息到 `meta` 字段。

```js
router.afterEach((to, from) => {
  const toDepth = to.path.split('/').length
  const fromDepth = from.path.split('/').length
  to.meta.transitionName = toDepth < fromDepth ? 'animate__wobble' : 'animate__bounce'
})
```

## 强制在复用的视图之间进行过渡

Vue 可能会自动复用看起来相似的组件，从而忽略了任何过渡。幸运的是，可以[添加一个 `key` 属性](https://v3.vuejs.org/api/special-attributes.html#key)来强制过渡。这也允许你在相同路由上使用不同的参数触发过渡：

```vue
<router-view v-slot="{ Component, route }">
  <transition :enter-active-class="route.meta.transitionName">
    <component :is="Component" :key="route.path"/>
  </transition>
</router-view>
```

# 滚动行为

使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。 vue-router 能做到，而且更好，它让你可以自定义路由切换时页面如何滚动。

**注意: 这个功能只在支持 history.pushState 的浏览器中可用。**

当创建一个 Router 实例，你可以提供一个 `scrollBehavior` 方法：

```js
const router = createRouter({
  history: createWebHashHistory(),
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return 期望滚动到哪个的位置
  }
})
```

`scrollBehavior` 函数接收 `to`和` from` 路由对象。第三个参数 `savedPosition`，只有当这是一个 `popstate` 导航时才可用（由浏览器的后退/前进按钮触发）。

该函数可以返回一个 [`ScrollToOptions`](https://developer.mozilla.org/en-US/docs/Web/API/ScrollToOptions) 位置对象:

```js
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    // 始终滚动到顶部
    return { top: 0 }
  },
})
```

你也可以通过 `el` 传递一个 CSS 选择器或一个 DOM 元素。在这种情况下，`top` 和 `left` 将被视为该元素的相对偏移量。

```js
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    // 始终在元素 #main 上方滚动 10px
    return {
      // 也可以这么写
      // el: document.getElementById('main'),
      el: '#main',
      top: -10,
    }
  },
})
```

如果返回一个 falsy 的值，或者是一个空对象，那么不会发生滚动。

返回 `savedPosition`，在按下 后退/前进 按钮时，就会像浏览器的原生表现那样：

```js
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else {
      return { top: 0 }
    }
  },
})
```

如果你要模拟 “滚动到锚点” 的行为：

```js
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return {
        el: to.hash,
      }
    }
  },
})
```

如果你的浏览器支持[滚动行为](https://developer.mozilla.org/en-US/docs/Web/API/ScrollToOptions/behavior)，你可以让它变得更流畅：

```js
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return {
        el: to.hash,
        behavior: 'smooth',
      }
    }
  }
})
```

## 延迟滚动

有时候，需要在页面中滚动之前稍作等待。例如，当处理过渡时，希望等待过渡结束后再滚动。要做到这一点，你可以返回一个 Promise，它可以返回所需的位置描述符。下面是一个例子，在滚动前等待 500ms:

```js
const router = createRouter({
  scrollBehavior(to, from, savedPosition) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({ left: 0, top: 0 })
      }, 500)
    })
  },
})
```

