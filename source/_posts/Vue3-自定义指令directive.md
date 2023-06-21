---
title: Vue3-自定义指令directive
date: 2022-11-20 10:58:14
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

# Vue3-自定义指令directive

## 1、Vue3指令的钩子函数

- created 元素初始化的时候
- beforeMount 指令绑定到元素后调用 只调用一次
- mounted 元素插入父级dom调用
- beforeUpdate 元素被更新之前调用
- update 这个周期方法被移除 改用updated
- beforeUnmount 在元素被移除前调用
- unmounted 指令被移除后调用 只调用一次

## 2、在setup内定义局部指令

Tips：必须以 `vNameOfDirective` 的形式来命名本地自定义指令，以使得它们可以直接在模板中使用。

```vue
<template>
  <button @click="show = !show">开关{{show}} ----- {{title}}</button>
  <Dialog  v-move-directive="{background:'green',flag:show}"></Dialog>
</template>

<script setup lang="ts">
  	 
const vMoveDirective: Directive = {
  created: () => {
    console.log("初始化====>");
  },
  beforeMount(...args: Array<any>) {
    // 在元素上做些操作
    console.log("初始化一次=======>");
  },
  mounted(el: any, dir: DirectiveBinding<Value>) {
    el.style.background = dir.value.background;
    console.log("初始化========>");
  },
  beforeUpdate() {
    console.log("更新之前");
  },
  updated() {
    console.log("更新结束");
  },
  beforeUnmount(...args: Array<any>) {
    console.log(args);
    console.log("======>卸载之前");
  },
  unmounted(...args: Array<any>) {
    console.log(args);
    console.log("======>卸载完成");
  },
};
</script>
```

## 3、生命周期钩子参数详解

第一个 el  当前绑定的DOM 元素

第二个 binding

instance：使用指令的组件实例。
value：传递给指令的值。例如，在 v-my-directive="1 + 1" 中，该值为 2。
oldValue：先前的值，仅在 beforeUpdate 和 updated 中可用。无论值是否有更改都可用。
arg：传递给指令的参数(如果有的话)。例如在 v-my-directive:foo 中，arg 为 "foo"。
modifiers：包含修饰符(如果有的话) 的对象。例如在 v-my-directive.foo.bar 中，修饰符对象为 {foo: true，bar: true}。
dir：一个对象，在注册指令时作为参数传递。

第三个 当前元素的虚拟DOM 也就是Vnode

第四个 prevNode 上一个虚拟节点，仅在 `beforeUpdate` 和 `updated` 钩子中可用 

## 4、自定义拖拽指令案例

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 22022-11-20 09:14:30
 * @LastEditTime: 2022-11-20 11:52:21
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    <div v-move class="box">
      <div class="header">title</div>
      内容
    </div>
  </div>
</template>

<script setup lang="ts">
import { Directive, DirectiveBinding } from 'vue';
const vMove: Directive<any, void> = (el: HTMLElement, bingding: DirectiveBinding) => {
  // 获取要移动的元素
  const moveElement: HTMLDivElement = el.firstElementChild as HTMLDivElement // 通过断言给元素赋予类型
  // 鼠标按下后为要移动的元素添加移动事件
  const mouseDown = (e: MouseEvent) => {
    let X = e.clientX - el.offsetLeft
    let Y = e.clientY - el.offsetTop

    const move = (e: MouseEvent) => {
      el.style.left = e.clientX - X + 'px'
      el.style.top = e.clientY - Y + 'px'
    }
    document.addEventListener('mousemove', move)
    // 鼠标抬起停止移动元素
    document.addEventListener('mouseup', () => {
      document.removeEventListener('mousemove', move)
    })
  }
  // 鼠标按下
  moveElement.addEventListener('mousedown', mouseDown)
}
</script>

<style lang="scss" scoped>
.content {
  position: relative;
  flex: 1;
  height: 100%;
  margin: 20px;
  border: 1px solid #ccc;
  overflow: auto;

  .box {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 200px;
    height: 200px;
    border: 1px solid #999;

    .header {
      height: 20px;
      padding: 10px;
      background: yellow;
      cursor: move;
    }
  }
}
</style>
```

