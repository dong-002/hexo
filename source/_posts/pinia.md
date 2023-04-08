---
title: pinia
date: 2023-04-06
tags:
- pinia
categories:
- [vue]

---

### 介绍

pinia是vue中用来状态管理的，状态管理的意思就是这个应用的公共状态封装起来放到一个文件中，封装的状态所有组件都可以使用。pinia的前身就是vuex，对比vuex，pinia有以下特点：

* pinia提供了更简单的API，并且更加适合TypeScirpt的使用
* 弃用了复杂的mutation，只有state，getters，actions
* 在pinia中无需动态添加Store，它们默认是动态的
* 不再有嵌套模块，pinia提供的是一个扁平的结构，每个store都是独立的，但是可以在store中导入另一个store使用

### 定义store

每一个store就是一个状态，可以定义多个store。在pinia中的store是使用`defineStore()`来定义，第一个参数是这个store的名字，作为ID可以连接store和devtools。一般规定对`defineStore()`的返回值用use···命名。

```js
import {defineStore} from 'pinia'
export const useCouter=defineStore('couter',{
  //state用来存放数据 相当于组件中的data
  state:()=>{
    return {
      count: 0
    }
  },
  // getters相当于组件中的computed
  getters: {
    double: (state)=>{return state.count*2}
  },
  // actions用来定义方法改变state中的数据 相当于组件中的methods
  actions: {
    increment() {
      this.count++;
    }
  }
})
```

state用来存放数据，相当于组件中的data；getters相当于组件中的computed；actions用来定义方法改变state中的数据 ，当于组件中的methods。

#### 使用store

在定义了store之后，开始使用它，首先在`main.js`中创建pinia，全局使用。

```js
import { createApp } from 'vue'
import App from './App.vue'
import {createPinia} from 'pinia'
const app=createApp(App)
const pinia=createPinia()
app.use(pinia)
app.mount('#app')
```

然后在想要使用store的组件中导入进行使用。在组件中我们可以访问到state中的数据和actions中的方法。

```vue
<script setup>
import {useCouter} from './stores/couter'
const couterStore=useCouter()
</script>

<template>
  <div>
    <p>{{couterStore.count}}</p>
    <button @click="couterStore.increment()">add</button>
    <p>double:{{couterStore.double}}</p>
  </div>
</template>
```

结果如下。在任何组件中都可以使用这个store，我们在不同的组件中使用了这个共享状态，store中的数据改变，所有组件的视图也会发生改变。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/GIF%202023-4-6%2023-10-32.gif)

#### 解构store

store是一个用reactive包装的对象，在使用时不需要在后面写`.value`。直接通过`store.属性`使用。

**对store进行解构从而提取属性时将失去响应性。可以直接解构actions。**

```vue
<script setup>
import {useCouter} from './stores/couter'
const couterStore=useCouter()
// 通过解构的数据和方法是没有响应式的
const {count}=(couterStore)
</script>
<template>
	<div>
    <p>有响应式count:{{couterStore.count}}</p>
    <p>无响应式:{{count}}</p>
    <button @click="couterStore.increment()">add</button>
    <p>doubleCount:{{couterStore.double}}</p>
  </div>
</template>
```

可以看到直接解构使得属性失去了响应式。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/GIF%202023-4-6%2023-20-05.gif)

为了store中的属性保持响应性，可以使用`storeToRefs()`。使用`storeToRefs()`之后上面的解构的count也会响应式改变。

```js
import {useCouter} from './stores/couter'
const couterStore=useCouter()
// 通过解构的数据和方法是没有响应式的
//const {count}=(couterStore)

// 要通过storeToref才是响应式
import {storeToRefs} from 'pinia'
const {count}=storeToRefs(couterStore)
```

### state

state是store中的核心，它被定义为一个返回初始状态的函数。

1. 访问state

可以通过`store.属性`访问到里面的数据。

```js
import {useStore} from './store/useCount.js'
const store=useStore()
store.count++ //通过store.xx访问
```

2. 重置state

使用store中的`$reset()`方法将state重置为初始值。

3. $patch

除了用`store.属性`的方法直接改变数据，还可以调用`$patch()`方法，它使用对象的方式改变多个属性。

```js
store.$patch({
    count: store.count=3,
    text: 'hello'
})
```

使用对象的方式很难修改集合属性，例如修改数组需要新建一个数组，因此也可以通过一个函数的方式来改变状态。

```js
store.$patch((state)=>{
    state.arr.push({name:'jack',age: 29})
})
```

### getters

getters就是state的计算值，即把复杂的逻辑写在这里，它的参数为state。

1. 访问其他getter

我们可以定义多个getter，通过`this`可以访问到整个store实例，这样我们就能访问到其他的getter。

```js
export const useCount=defineStore('counter',{
    state:()=>({
        count: 0
    }),
    getters: {
        double: (state)=> state.count*2,
        //this.double就是上面的
        add: (){return this.double+1}
    }
})
```

2. 向getter传递参数

getter只是计算属性，除了state不接受任何参数，不过可以从getter返回一个函数，该函数接受任何参数

```js
export const useStore=defineStore('id',{
    getters: {
        getUserId: (state)=>{
            return (userId)=>state.users.find((user)=>user.id===userId)
        }
    }
})
```

3. 访问其他store的getter

要访问其他store中的getter，就要导入其他store。

```js
import { defineStore } from 'pinia'
import {useUser} from './user.js'
export const useCount = defineStore('couter', {
  state: () => ({
    count: 0,
  }),
  getters: {
    toString(){
      const User=useUser()
      //getUserName返回一个用户名
      return 'id为2的用户名字：'+User.getUserName(2)
    }
  },
})
```

### actions

action相当于组件中的method，你可以在里面定义一些操作修改state。action可以通过this访问整个store实例。

```js
import { defineStore } from 'pinia'
export const useCount = defineStore('couter', {
  state: () => ({
    count: 0,
  }),
  actions: {
    increment() {
      this.count++
    },
  },
})

```

action是可以异步的，可以在里面await调用任何API。

访问其他store中的action也像getter一样，在这个store中导入其他的store，然后使用。

```js
import { defineStore } from 'pinia'
import {useUser} from './user.js'
export const useCount = defineStore('couter', {
  state: () => ({
    count: 0,
  }),
  actions: {
    const User=useUser()
    if(User.action) {
       doSomething
	}
  }
})
```

