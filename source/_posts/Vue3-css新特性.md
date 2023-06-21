---
title: Vue3-css新特性
date: 2022-12-13 15:18:50
cover: /img/vue3.jpeg
top_img: https://bu.dusays.com/2022/07/07/62c6f3d6351af.jpg
tags:
- Vue3
categories:
- Web
---

# 1、css深度选择器

### scoped原理:

vue中的scoped 通过在DOM结构以及css样式上加唯一不重复的标记:data-v-hash的方式，以保证唯一（而这个工作是由过PostCSS转译实现的），达到样式私有化模块化的目的。

总结一下scoped三条渲染规则：

1. 给HTML的DOM节点加一个不重复data属性(形如：data-v-123)来表示他的唯一性
2. 在每句css选择器的末尾（编译后的生成的css语句）加一个当前组件的data属性选择器（如[data-v-123]）来私有化样式
3. 如果组件内部包含有其他组件，只会给其他组件的最外层标签加上当前组件的data属性

### 保留scoped属性，使用深度选择器，代码如下:

```css
<!-- 写法1 使用::v-deep -->
<style lang="scss" scoped>
  ::v-deep .el-input__inner{
    background: yellowgreen;
  }
</style>
<!-- 写法2 使用>>> 操作符-->
<style scoped>
>>>.el-input__inner{
  background: yellowgreen;
}
</style>
<!-- 写法3 使用/deep/ -->
<style scoped>
 /deep/.el-input__inner{
  background: yellowgreen;
}
</style>
<!-- 写法4 使用:deep(<inner-selector>) -->
<style lang="scss" scoped>
  :deep(.el-input__inner){
    background: yellowgreen;
  }
</style>
```

### 使用深度选择器后代码被编译成如下形式：

```css
[data-v-e44d851a] .el-input__inner {
    background: yellowgreen;
}
```

### Ps:

- 写法1 和写法4，都支持sass预处理器。但是在新的vue3.0 单文件规范中，如果你还是使用写法1，会碰到如下警告:

  ```js
  [@vue/compiler-sfc] ::v-deep usage as a combinator has been deprecated. Use :deep(<inner-selector>) instead.
  ```

- 写法1在vue3.0中已经被弃用了，在开发vue3.0项目的时候，还是使用写法4，写法4在语义上也更有助于理解。

- 写法1和写法3，主要是不支持sass预处理器的解析，且`>>>`操作符存在浏览器兼容性问题

# 2、:slotted(inner-selector)插槽选择器

### 子组件定义插槽

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-09-07 17:49:20
 * @LastEditTime: 2022-09-14 11:21:58
 * @LastEditors: Do not edit
-->
<template>
    <div class="slot">
        <header class="header">
            <!-- slot标签相当于一个占位符 -->
            <slot name="header"></slot>
        </header>
        <main class="main">
            <!-- slot标签相当于一个占位符 -->
            <div>
                <div v-for="i in 100">
                    <slot :data="i"></slot>
                </div>
            </div>
        </main>
        <footer class="footer">
            <!-- slot标签相当于一个占位符 -->
            <slot name="footer"></slot>
        </footer>

    </div>
</template>

<script setup lang="ts">

</script>

<style lang="scss" scoped>
.header {
    height: 200px;
    background-color: aqua;
}

.main {
    height: 300px;
    background-color: bisque;
}

.footer {
    height: 200px;
    background-color: cadetblue;
}
</style>
```

### 父组件使用该子组件

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 22022-11-20 09:14:30
 * @LastEditTime: 2022-12-13 15:09:49
 * @LastEditors: Do not edit
-->
<template>
  <div class="content">
    下面是插槽
    <Slot>
      <template #header>
        <div class="test_change">2222</div>
      </template>
    </Slot>
  </div>
</template>

<script setup lang="ts">
import Slot from '../../components/Slot/index.vue'
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

### 子组件中修改插槽class的颜色

```css
<style lang="less" scoped>
  .test_change {
    color:greenyellow;
  }
</style>
```

未能修改,作用域样式不会影响到 `<slot/>` 渲染出来的内容，因为它们被认为是父组件所持有并传递进来的。

解决方案：

```css
<style lang="less" scoped>
  :slotted(.test_change) {
    color:greenyellow;
  }
</style>
```

# 3、:global(inner-selector)全局选择器

通常都是通过新建一个不加scoped属性的style标签修改全局样式，现在有更**优雅**的方案——:global()

```css
<style lang="less" scoped>
  :global(div){
      color:red
  }
</style>
```

# 4、:v-bind(inner-selector)动态css

单文件组件的 `<style>` 标签可以通过 `v-bind` 这一 CSS 函数将 CSS 的值关联到动态的组件状态上：

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-09-07 17:49:20
 * @LastEditTime: 2022-12-13 16:00:08
 * @LastEditors: Do not edit
-->
<template>
    <div class="slot">
        <header class="header">
            <!-- slot标签相当于一个占位符 -->
            <slot name="header"></slot>
        </header>
        <main class="main">
            <!-- slot标签相当于一个占位符 -->
            <div>
                <div v-for="i in 100">
                    <slot :data="i"></slot>
                </div>
            </div>
        </main>
        <footer class="footer">
            <!-- slot标签相当于一个占位符 -->
            <slot name="footer"></slot>
        </footer>

    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
const changeColor = ref<string>('aqua')
</script>

<style lang="scss" scoped>
.header {
    height: 200px;
    background-color: v-bind(changeColor); // 使用变量动态修改css样式
}

.main {
    height: 300px;
    background-color: bisque;
}

.footer {
    height: 200px;
    background-color: cadetblue;
}
</style>
```

如果是对象形式的变量，v-bind需要加引号

```vue
<!--
 * @Description: 
 * @Author: xiuji
 * @Date: 2022-09-07 17:49:20
 * @LastEditTime: 2022-12-27 10:13:28
 * @LastEditors: Do not edit
-->
<template>
    <div class="slot">
        <header class="header">
            <!-- slot标签相当于一个占位符 -->
            <slot name="header"></slot>
        </header>
        <main class="main">
            <!-- slot标签相当于一个占位符 -->
            <div>
                <div v-for="i in 100">
                    <slot :data="i"></slot>
                </div>
            </div>
        </main>
        <footer class="footer">
            <!-- slot标签相当于一个占位符 -->
            <slot name="footer"></slot>
        </footer>

    </div>
</template>

<script setup lang="ts">
import { ref,reactive } from 'vue';
const changeColor = ref<string>('aqua')
type obj = {
    color:string
}
const objColor = reactive<obj>({
    color: 'bisque'
})

</script>

<style lang="scss" scoped>
.header {
    height: 200px;
    background-color: v-bind(changeColor); // 使用变量动态修改css样式
}

.main {
    height: 300px;
    background-color: v-bind('objColor?.color'); // 对象形式的变量，v-bind需要加引号
}

.footer {
    height: 200px;
    background-color: cadetblue;
}
</style>
```

# 5、css `module`

`<style module>`标签会被编译为CSS Modules并且将生成的CSS类作为$style对象的键暴露给组件

```vue
<template>
    <div :class="$style.red">
        test css module
    </div>
</template>
 
<style module>
.red {
    color: red;
    font-size: 20px;
}
</style>
```

自定义注入名称，多个可用数组，可以通过module attribute一个参数来自定义注入的类对象的property键

```vue
<template>
    <div :class="[xj.red,xj.border]">
        test moudule attribute
    </div>
</template>
 
<style module="xj">
.red {
    color: red;
    font-size: 20px;
}
.border{
    border: 1px solid #ccc;
}
</style>
```

与组合式API一同使用

注入的类可以通过 [useCssModule](https://v3.cn.vuejs.org/api/global-api.html#usecssmodule) API 在 `setup()` 和 `<script setup>` 中使用。对于使用了自定义注入名称的 `<style module>` 模块，`useCssModule` 接收一个对应的 `module` attribute 值作为第一个参数

```vue
<template>
    <div :class="[xj.red,xj.border]">
        小满是个弟弟
    </div>
</template>
 
 
<script setup lang="ts">
import { useCssModule } from 'vue'
const css = useCssModule('xj')
</script>
 
<style module="xj">
.red {
    color: red;
    font-size: 20px;
}
.border{
    border: 1px solid #ccc;
}
</style>
```

一般TSX 和 render函数使用场景居多
