---
title: Vue3-自定义hooks
date: 2022-11-20 12:02:59
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

# 自定义hooks

## 1、Mixins的不足

vue2中通过Mixins将这些多个相同的逻辑抽离出来，各个组件只需要引入mixins，就能实现一次写代码，多组件受益的效果。

弊端就是 会涉及到覆盖的问题：组件中定义的data，methods，filters会覆盖mixins中相同命名的data，methods，filters。

变量来源不明确（隐式传入），不利于阅读，使代码变的难以维护

## 2、Vue3的自定义hooks

- Vue3的hooks函数相当于Vue2的mixin，不同在于hooks是函数
- Vue3的hooks函数可以帮我们提高代码的复用性

图片转base64hook：

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 12:16:07
 * @LastEditTime: 2022-11-20 12:41:51
 * @LastEditors: Do not edit
 */
import { onMounted } from 'vue'
type Options = {
    el: string
}

export default function (options: Options): Promise<{ baseUrl: string }> {
    return new Promise((resolve, reject) => {
        onMounted(() => {
            let img: HTMLImageElement = document.querySelector(options.el) as HTMLImageElement
            // 图片加载完成后再进行转化
            img.onload = () => {
                resolve({
                    baseUrl: toBase(img)
                })
            }
        })

        const toBase = (el: HTMLImageElement): string => {
            const canvas: HTMLCanvasElement = document.createElement('canvas')
            // 初始化canvas
            const ctx = canvas.getContext('2d') as CanvasRenderingContext2D
            canvas.width = el.width
            canvas.height = el.height
            ctx?.drawImage(el, 0, 0, canvas.width, canvas.height)
            return canvas.toDataURL('image/jpg')
        }
    })
}
```

组件中使用：

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 22022-11-20 09:14:30
 * @LastEditTime: 2022-11-20 12:46:28
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <img id="avar" src="../../assets/logo.png" width="350" height="350">
  </div>
</template>

<script setup lang="ts">
import useBase64 from '../../hooks'
useBase64({
  el: '#avar'
}).then(res => {
  console.log(res); // 拿到转换结果
})
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
