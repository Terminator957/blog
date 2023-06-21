---
title: Vue3-vue3插件编写及挂载
date: 2022-11-29 15:48:24
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

# 插件编写及挂载

在使用createApp()初始化Vue应用程序后，可以通过调用use()方法将插件添加到你的应用程序中

如下实现一个Loading插件

## Loading.vue

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-29 15:54:13
 * @LastEditTime: 2022-11-29 17:06:29
 * @LastEditors: Do not edit
-->
<template>
    <div>
        <div class="loading" v-if="showLoading">加载中...</div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
// 定义控制组件展示的开关
let showLoading = ref(false)

const show = () => { showLoading.value = true }
const hide = () => { showLoading.value = false }

// 对外暴露当前组件的属性和方法
defineExpose({
    showLoading,
    show,
    hide
})
</script>

<style lang="scss" scoped>
.loading {
    width: 100vw;
    height: 100vh;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #999;
    text-align: center;
    line-height: 100vh;
}
</style>
```

## Loading.ts

```vue
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-29 15:54:22
 * @LastEditTime: 2022-11-29 17:08:42
 * @LastEditors: Do not edit
 */
import { createVNode, render, VNode, App } from "vue";
import LoadingVue from "./index.vue";

export default {
    install(app: App) {
        // createVNode vue提供的底层方法 可以给我们组件创建一个虚拟DOM 也就是Vnode
        const Vnode: VNode = createVNode(LoadingVue)
        // render把Vnode挂载到指定节点
        render(Vnode, document.body)
        // 全局挂载当前组件方法
        app.config.globalProperties.$loading = {
            show: () => Vnode.component?.exposed?.show(),
            hide: () => Vnode.component?.exposed?.hide(),
        }
    }
}
```

## Main.ts

```vue
type Lod = {
    show: () => void,
    hide: () => void
}

app.use(LoadingVue)
```

## 使用插件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 22022-11-20 09:14:30
 * @LastEditTime: 2022-11-29 17:08:46
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
  </div>
</template>

<script setup lang="ts">
// 使用loading组件
import { getCurrentInstance } from 'vue'
// 通过getCurrentInstance()获取组件实例，调用其上挂载的插件方法
const loading = getCurrentInstance()
loading?.proxy?.$loading.show()
setTimeout(() => {
  loading?.proxy?.$loading.hide()
}, 3000);
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

