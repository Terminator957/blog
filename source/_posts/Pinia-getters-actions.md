---
title: Pinia-Getters,Actions
date: 2023-04-14 15:54:16
cover: https://bu.dusays.com/2023/04/13/6437c05a82509.png
top_img: https://bu.dusays.com/2022/11/16/6374528071f3b.jpeg
tags:
- Pinia
categories:
- Pinia
---

# Getters

Getter 完全等同于 Store 状态的 [计算值](https://v3.vuejs.org/guide/reactivity-computed-watchers.html#computed-values)。 它们可以用 `defineStore()` 中的 `getters` 属性定义。 他们接收“状态”作为第一个参数**以鼓励**箭头函数的使用：

```ts
/*
 * @Description: store
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-14 16:02:07
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'
export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            bikini: 'bikini',
            count: 4,
        }
    },
    // computed
    getters: {
        doubleCount(state) {// 注意：使用箭头函数不能使用this this指向已经改变指向undefined 修改值请用state
            return state.count * 2
        },
      	// 返回类型必须明确设置
        doubleCountPlusOne(): number { // getters之间可以互相调用
            return this.doubleCount + 1 
        }
    },
    // methods
    actions: {
        increment() {
            this.count++
        }
    }
})
```

然后可以直接在 store 实例上访问 getter：

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 16:04:38
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h2>
      doubleCount --- {{ store.doubleCount }}
    </h2>
    <h2>
      doubleCount plus one --- {{ store.doubleCountPlusOne }}
    </h2>
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

# Actions（支持同步异步）

## 同步 直接调用即可

```ts
/*
 * @Description: store
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-14 16:15:08
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'
export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            bikini: 'bikini',
            count: 4,
        }
    },
    // computed
    getters: {
        doubleCount(state) {
            return state.count * 2
        },
        doubleCountPlusOne(): number {
            return this.doubleCount + 1
        }
    },
    // methods
    actions: {
        increment() {
            this.count++
        },
        decrement() {
            this.count = Math.round(100 * Math.random())
        }
    }
})
```

组件内使用

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 16:20:06
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h1>
      {{ store.count }}
    </h1>
    <div>
      <button @click="decrement">decrement</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
const decrement = () => {
  store.decrement();
};
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

## 异步 可以结合async await 修饰

```ts
/*
 * @Description: store
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-14 16:34:56
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'

type Result = {
    name: string,
    age: number,
}

// 模拟接口请求
const getUserData = (): Promise<Result> => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({
                name: 'xiuji',
                age: 18,
            })
        }, 1000)
    })
}

export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            user: <Result> {}
        }
    },
    // methods
    actions: {
        async getUserInfo() {
            const res = await getUserData()
            this.user = res
        }
    }
})
```

组件中仍然通过store实例调用actions中定义的方法

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-11-20 09:14:30
 * @LastEditTime: 2023-04-14 16:35:32
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <h1>
      {{ store.user }}
    </h1>
  </div>
</template>

<script setup lang="ts">
import { useStore } from '@/store/index.ts';
const store = useStore();
store.getUserInfo();
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

**多个action互相调用getLoginInfo setName**

```ts
/*
 * @Description: store
 * @Author: xiuji
 * @Date: 2023-04-12 16:39:54
 * @LastEditTime: 2023-04-14 16:38:56
 * @LastEditors: Do not edit
 */
import { defineStore } from 'pinia'
import { StoreName } from './index-name'

type Result = {
    name: string,
    age: number,
}

// 模拟接口请求
const getUserData = (): Promise<Result> => {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve({
                name: 'xiuji',
                age: 18,
            })
        }, 1000)
    })
}

export const useStore = defineStore(StoreName.main, {
    state: () => {
        return {
            user: <Result> {},
            name: '占位',
        }
    },
    // methods
    actions: {
        async getUserInfo() {
            const res = await getUserData()
            this.user = res
            this.setUserName(res.name)
        },
        setUserName(name: string) {
            this.name = name
        }
    }
})
```

