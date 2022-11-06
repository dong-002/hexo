---
title: vue3中遇到的问题
date: 2022-10-31 16:29:35
tags:
- vue
categories:
- [vue]
---

### 父组件和子组件的传递

#### 父向子传值

这里是想点击父组件中的按钮让子组件显示出来，原来子组件是不显示的。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/%E7%A9%BA%E7%99%BD.png)

```vue
//父组件
<template>
	<button @click="comeOn">确定</button>
    <div>
        <!-- 传入的参数 子组件是否显示-->
        <Son :show='son.show' ></Son>
    </div>
</template>
<script setup>
	import {reactive} from 'vue'
    //这里要用reactive 
	const son=reactive({
		show: false
	})
    const comeOn=()=>{
        son.show=true
    }
</script>
```

```vue
//子组件
<template>
	<div>
        
    </div>
</template>
<script setup>
    //这里设置属性 父组件使用时要传入的参数
	const props=defineProps({
        show: {
            type: Boolean,
            default: false
        }
    })
</script>
```

#### 子向父传递方法

子组件是一个弹窗，有一个关闭按钮，当点击关闭按钮时就把弹窗关掉

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/%E7%A9%BA%E7%99%BD.png)

```vue
//父组件
<template>
	<button @click="comeOn">确定</button>
    <div>
        <!-- 传入的参数 子组件是否显示-->
        <Son :show='son.show' @closeDialog='close'></Son>
    </div>
</template>
<script setup>
	import {reactive} from 'vue'
	const son=reactive({
		show: false
	})
    const comeOn=()=>{
        son.show=true
    }
    const close=()=>{
        son.show=false
    }
</script>
```

```vue
//子组件
<template>
	<div>
        <button @click="close"></button>
    </div>
</template>
<script setup>
    //这里设置属性 父组件使用时要传入的参数
	const props=defineProps({
        show: {
            type: Boolean,
            default: false
        }
    })
    //定义emit用来传递
    const emit=defineEmits(['closeDialog'])
    const close=()=>{
        //这里将方法传递给父组件
        emit('closeDialog') //父组件中的接受方法也要是这个名字
    }
</script>
```

#### grandPa通过子组件让grandSon显示

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/a.png)

在祖父中有一个按钮，点击这个按钮让孙子显示，但是孙子中的参数是父亲传入的，这里需要在父亲中定义一个方法然后暴露给祖父，祖父调用这个方法改变参数。defineExpose将方法或者属性暴露出去。

```vue
//grandpa组件
<template>
	<button @click="show">确定</button>
    <div>
        <!-- ref可以获取这个组件 -->
        <Father ref='showRef'></Father>
    </div>
</template>
<script setup>
	import {ref} from 'vue'
    const showRef=ref() //需要定义一个ref
    const show=()=>{
        //点击祖父的按钮 调用父的方法改变值
        showRef.value.comeOn()
    }
</script>
```

```vue
//father组件
<template>
	<Son :show="son.show"></Son>
</template>
<script setup>
	import {reactive} from 'vue'
	const son=reactive({
		show: false
	})
    //这里定义一个方法 可以改变参数
    const comeOn=()=>{
        son.show=true
    }
    defineExpose({comeOn}) //结构 只是将这个方法暴露
</script>
```

```vue
//son组件
<template>
	<div>
        son组件
    </div>
</template>
<script setup>
    //这里设置属性 父组件使用时要传入的参数
	const props=defineProps({
        show: {
            type: Boolean,
            default: false
        }
    })
</script>
```
