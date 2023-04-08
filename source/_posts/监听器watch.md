---
title: 监听器
date: 2023-04-04
tags:
- 监听器
categories:
- [vue]


---



监听器用于监听响应式数据。当响应式数据发生了改变可以进行相应的操作。

### 监听ref

下面代码作用：当每次num的值改变时就打印一下新的值

```vue
<template>
  <div id="d">
    num
    <button @click="num++">{{num}}</button>
  </div>
</template>

<script setup>
import {ref,reactive, watch} from 'vue'
const num=ref(0);
const obj=reactive({});

watch(num,(newX)=>{
  console.log('新的值为',newX);
})
</script>
<style></style>
```

### 监听reactive对象

不能直接监听响应式对象的属性值

```js
const obj = reactive({ count: 0 })
// 错误，因为 watch() 得到的参数是一个 number
watch(obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```

要用一个返回该属性的getter函数

```js
// 提供一个 getter 函数
watch(
  () => obj.count,
  (count) => {
    console.log(`count is: ${count}`)
  }
)
```

### 深层监听

当给watch传入一个响应式对象，会自动创建一个深层的监听器，对象内所有的属性发生改变都会触发回调函数。

```vue
<template>
  <div id="d">
    <button @click="obj.count++">{{obj.count}}</button>
    <!-- 数值发生改变也会触发 -->
  </div>
</template>

<script setup>
import {ref,reactive, watch} from 'vue'
const obj = reactive({ count: 0,name: '' })
watch(obj, (newValue,oldValue) => {
  console.log(newValue,oldValue)
})
obj.name='jack'; //名字发生改变会触发
</script>
<style></style>
```

obj里面的属性发生了改变都会触发watch监听器。

当obj.name改变时触发了watch，这时打印newValue和oldValue，这两个是一样的，他们都是obj对象。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20230330105321145.png)

其实我们也可以手动的把watch变为深层监听，加多一个deep

```js
watch(value,()=>{
    
},{deep:true})
```

### 立即监听

上面监听器只有在数据发生改变时才会监听，但有时候我们希望在一开始就监听数据的变化，执行某些操作。这时可以加上immediate:true实现立即监听，即页面一被渲染就开始监听。

```vue
<template>
  <div id="d">
    <button @click="obj.count++">{{obj.count}}</button>
  </div>
</template>

<script setup>
import {ref,reactive, watch} from 'vue'
const obj = reactive({ count: 0,name: '' })
watch(obj, (newValue,oldValue) => {
  console.log(newValue,oldValue)
},{immediate:true}) //立即监听
</script>
<style></style>
```

现在我没有改变数据，但是页面渲染完后就实现了监听。

newValue为obj对象，oldValue是undefined.

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20230330111542925.png)