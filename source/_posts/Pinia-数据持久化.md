---
title: Pinia-数据持久化
date: 2023-04-18 10:29:18
cover: https://bu.dusays.com/2023/04/13/6437c05a82509.png
top_img: https://bu.dusays.com/2022/11/16/6374528071f3b.jpeg
tags:
- Pinia
categories:
- Pinia
---

**Pinia和Vuex都有刷新页面状态会丢失的问题**

Pinia通过自定义一个pinia插件实现数据持久化

```ts
/*
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-07-12 16:30:05
 * @LastEditTime: 2023-04-19 16:24:15
 * @LastEditors: Do not edit
 */
import { createApp, toRaw } from 'vue'
import { createPinia, PiniaPluginContext } from 'pinia'
const store = createPinia()

// 定义兜底变量
const __storageKey = 'pinia'

type Options = {
    key: string
}
// 存储
const setStorage = (key: string, value: any) => {
    localStorage.setItem(key, JSON.stringify(value))
}
// 获取
const getStorage = (key: string) => {
    const value = localStorage.getItem(key)
    return value ? JSON.parse(value) : null
}

const PiniaPlugin = (options: Options) => {
  // 使用函数柯里化分别接收用户参数和pinia参数
    return (content: PiniaPluginContext) => {
        const { store } = content
        const data = getStorage(`${options?.key ?? __storageKey}-${store.$id}`)
        if (data) {
            //返回值覆盖pinia 原始值
            return {
                ...data
            }
        } else {
            store.$subscribe(() => {
                // toRaw 用于获取原始值
                setStorage(`${options?.key ?? __storageKey}-${store.$id}`, toRaw(store.$state))
            })
        }
    }
}

// 注册插件
store.use(PiniaPlugin({ key: 'pinia' }))
```

