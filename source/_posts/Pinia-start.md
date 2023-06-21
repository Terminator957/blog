---
title: Pinia-start
date: 2023-04-13 15:55:05
cover: https://bu.dusays.com/2023/04/13/6437c05a82509.png
top_img: https://bu.dusays.com/2022/11/16/6374528071f3b.jpeg
tags:
- Pinia
categories:
- Pinia
---

# 为什么要使用 Pinia

Pinia 是 Vue 的存储库，它允许您跨组件/页面共享状态。 如果您熟悉 Composition API，您可能会认为您已经可以通过一个简单的 `export const state = reactive({})`. 这对于单页应用程序来说是正确的，但如果它是服务器端呈现的，**会使您的应用程序暴露于安全漏洞**。 但即使在小型单页应用程序中，您也可以从使用 Pinia 中获得很多好处：

- dev-tools 支持
  - 跟踪动作、突变的时间线
  - Store 出现在使用它们的组件中
  - time travel 和 更容易的调试
- 热模块更换
  - 在不重新加载页面的情况下修改您的 Store
  - 在开发时保持任何现有状态
- 插件：使用插件扩展 Pinia 功能
- 为 JS 用户提供适当的 TypeScript 支持或 **autocompletion**
- 服务器端渲染支持

# 安装

```bash
pnpm i pinia
```

main.ts中引入pinia，将pinia挂载到vue实例上

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-07-12 16:30:05
 * @LastEditTime: 2023-04-12 16:27:41
 * @LastEditors: Do not edit
 */
import { createApp } from 'vue'
import { createPinia } from 'pinia' // 导入pinia
import App from './App.vue'
import './assets/reset.scss'

const store = createPinia() // 创建一个 pinia（根存储）
// 导出app
export const app = createApp(App)

//Typescript 注册 由于必须要扩展ComponentCustomProperties类型才能获取类型提示
declare module '@vue/runtime-core' {
    export interface ComponentCustomProperties {
        EventBus: typeof EventBus
    }
}
// 通过.use(store)将store传递给应用程序
app.use(store).mount('#app')
```

# 起步

在src下新增store目录，新建index.ts，index-name.ts

**index-name.ts**

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-04-12 16:40:04
 * @LastEditTime: 2023-04-13 16:13:53
 * @LastEditors: Do not edit
 */
// 枚举一个命名空间
export const enum StoreName {
    main = 'main'
}
```

**index.ts**

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-13 16:11:38
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'
export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            count: 0
        }
    },
    // computed
    getters: {

    },
    // methods
    actions: {
        increment() {
            this.count++
        }
    }
})
```

# 使用

在组件中引入定义的store

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-13 16:18:49
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    {{ store.count }}
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
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

