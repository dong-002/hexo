---
title: vue3中遇到的问题
date: 2022-10-31 16:29:35
tags:
- vue
categories:
- [vue]
---

### 前言

最近在看视频跟着做一个[博客项目](https://www.bilibili.com/video/BV1Lg411a7N5)，这过程中遇到了不少的问题，由此记录一下。注意：这里的后台不是我所写，是UP主打包好的，以我目前的能力还写不出来。

### 登录

登录功能如下：首先有一个表单叫你输入账号密码，当你点击记住账号密码时会把你的信息存在Cookies中，下次再登录会从Cookies中获取账号密码，自动填到表单中。我们用到了md5进行密码的加密，不然别人可以从数据库或者Cookies中看到你的明文密码。(这个项目配合element plus一起使用)

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20221114181647291.png)

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20221114184150563.png)

```vue
<template>
  <div class="login-container">
      <div class="login-form">
          <div class="txt">用户登录</div>
          <el-form :model="formData" :rules="rules" ref="ruleFormRef">
              <el-form-item prop="account">
                  <el-input v-model="formData.account" placeholder="请输入账号" >
                      <!-- 插槽 -->
                      <template #prefix>
                          <!-- 这下面的iconfont是阿里云图标 官网下载压缩包放到静态文件下-->
                          <span class="iconfont icon-account"></span>
                      </template>
                  </el-input>
              </el-form-item>
              <el-form-item  prop="password">
                  <el-input v-model="formData.password" placeholder="请输入密码" type="password">
                      <template #prefix>
                          <span class="iconfont icon-password"></span>
                      </template>
                  </el-input>
              </el-form-item>
              <el-form-item prop="checkCode">
                  <div id="check-code">
                      <el-input v-model="formData.checkCode" placeholder="请输入验证码" @keyup.enter.native="login"></el-input>
                  <img :src="checkCodeUrl" alt="" class="code" @click="changeCheckCode">
                  </div>
              </el-form-item>
              <el-form-item class="remember" prop="rememberMe">
                  <el-checkbox true-label=true v-model="formData.rememberMe">记住我</el-checkbox>
              </el-form-item>
              <el-form-item >
                  <el-button class="login-btn" type="primary" @click="login">登录</el-button>
              </el-form-item>
          </el-form>
      </div>
  </div>
</template>

<script setup>
import md5 from 'js-md5' //加密密码
import {ElMessage} from 'element-plus'
import {getCurrentInstance, reactive, ref} from 'vue'
import {useRouter} from 'vue-router'
import Cookies from 'vue-cookies'

const {proxy} = getCurrentInstance()
const router=useRouter()
const api={
    // 接口
    checkCode: 'api/checkCode',
    login: '/login'
}
const formData=reactive({}) //存放表单数据
//这里是验证规则
const rules=reactive({
    account:[
        { required: true, message: '请输入账号',trigger: 'change' },
    { min: 1, max: 15, message: '长度在1到15',trigger: 'change' },
    ],
    password: [
        { required: true, message: '请输入密码'},
    { min: 8,  message: '至少8位',trigger: 'blur' },
    ],
    checkCode: [
        {required: true,message: '请输入验证码',trigger: 'change' }
    ]
})
//显示登录界面进行初始化 如果Cookies中有记录就把Cookies中的信息填到表单中 如果没有就说明是新用户登录，或者用户没有点击记住我
const init=()=>{
    let loginInfo=Cookies.get('loginInfo')
    if(!loginInfo) {
        return //没记住登录信息
    }
    // 记住的话就填上去
    Object.assign(formData,loginInfo)
}
init()
const ruleFormRef=ref(null)// 提交时进行表单验证
// 初始化后如果Cookies中有信息就会填到表单中，将Cookies中的密码和表单中的密码进行比较，不相等说明是新输入的 进行加密，相等可以直接拿表单中的信息进行登录。点击登录要把账号密码存到Cookies中
const login=()=>{
    ruleFormRef.value.validate(async(valid)=>{
        if(!valid){
            return
        }
        const CookiesLoginInfo=Cookies.get('loginInfo')
        const CookiesPassword=CookiesLoginInfo==null?null:CookiesLoginInfo.password
        //拿到Cookies中的密码 和现在表单中的密码进行比较 
        if(formData.password!=CookiesPassword) { //不相等说明是新输入的，进行加密 相等直接登录
            formData.password=md5(formData.password)
        }
        let params={
                account: formData.account,
                password: formData.password,
                checkCode: formData.checkCode
            }
        const result=await proxy.request({
            url: api.login,
            params,
            errorCallback:()=>{
                changeCheckCode()
            }
        })
        if(!result) {
            return
        }
        ElMessage.success('登录成功')
        setTimeout(()=>{
            router.push('/')
        },1000)
        const loginInfo={
            account: formData.account,
            password: formData.password,
            rememberMe: formData.rememberMe
        }
        Cookies.set('userInfo',result.data,0) //保存返回的信息永不过期
        // 记住登录信息
        if(formData.rememberMe) {
            Cookies.set('loginInfo',loginInfo,'7d')//保留7天
        }
    })
}

// 这里是验证码
const checkCodeUrl=ref(api.checkCode)
const changeCheckCode=()=>{
    checkCodeUrl.value =api.checkCode+'?'+new Date().getTime()
}
</script>
```



### 父组件和子组件的传递

#### 父向子传值

这里是想点击父组件中的按钮让子组件显示出来，原来子组件是不显示的。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/%E7%A9%BA%E7%99%BD.png)

```xml
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

```xml
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

```xml
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

```xml
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

```xml
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

```xml
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

```xml
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

### 不同组件共享数据

两个没有关系的组件之间怎么共用同一个数据呢？可以使用vuex来管理，先在项目中安装依赖包`npm i vuex`，然后在src目录下新建一个store文件夹，在其中新建index.js

```js
import {createStore} from 'vuex'
// 创建一个store实例 可以通过他在不同组件中进行数据传输
const store=createStore({
    // 这里放组件共享的数据 相当一个仓库所有组件可以从这里拿数据
    state() {
        return {
            userInfo: {
                avatar: '',
                nickName: ''
            }
        }
    },
    mutations: {
        // 第一个参数一定是state 第二个参数是传进来的
        updateUserInfo(state,userInfo) {
            state.userInfo=userInfo
        }
    }
})

export default store
```

然后在main.js文件中引入这个文件并使用

```js
import { createApp } from 'vue'
import App from './App.vue'
import store from './store/index.js'
const app=createApp(App)
app.use(store)
app.mount('#app')
```

假设在A组件中改变了某个数据，要同步到B组件中

```js
//这里是A组件的script
const {useStore} from 'vuex'
const store=useStore()

const changeData()=>{
    // 改变了数据 提交到store 注意第一个参数要和mutations中一样
    store.commit('updateUserInfo',{avatar:formData.value.avatar,nickName:formData.value.nickName})
}
```

```js
//这里是B组件的script
const {useStore} from 'vuex'
const store=useStore()

//当数据发生改变时监听数据 要用watch才能拿到更新后的值
watch(()=>store.state.userInfo,(newVal,oldVal)=>{
  const avatar=newVal.avatar //拿到更新后的值
  const nickName=newVal.nickName
},{immediate: true,deep: true})

```

### nextTick的使用

总是遇到nextTick，但是不知道为什么要使用他，网上搜了一下：**在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM**。就是说在更新了数据之后要调用这个方法才能得到最新的数据。

经常遇到这样一个使用场景：在清除表单的时候总是要在`nextTick(()=>{})`进行。

```xml
<template>
	<button @click="showForm"></button>
	<el-form ref="formRef"></el-form>
</template>
<script>
import {ref} from 'vue'
const formRef=ref() //通过formRef.value可以拿到表单这个dom
// ！！！因为点击按钮才出现表单，表单是后面出现的，要用nextTick等到表单这个dom加载完才能得到value
const showForm=()=>{
    //formRef.value.resetFields() 这样是错误的 这时formRef.value是undifine
    nextTick(()=>{
        formRef.value.resetFields() //首先进行表单的清空 
        //下面在进行其他操作
    })
}
</script>
```

### axios封装

一个项目中要用到接口，但是每次都直接调用axios代码变得冗余，原来的axios：

```js
axios('填写url',{
    method: 'GET',
    timeout: 10*1000, //超时
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded;charset=UTF-8', //这里是表单格式 不同格式有不同
        'X-Requested-With': 'XMLHttpRequest'
    }
})
.then((data)=>{
    
}, (err)=>{
    
})
.catch(error=>{
    
})
```

每次调用都要这样写非常麻烦，所以我们要对axios进行封装，在src目录下新建文件

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20221113125557979.png)

文件如下：

```js
import axios from "axios";

const contentTypeForm="application/x-www-form-urlencoded;charset=UTF-8" //表单
const contentTypeJson="application/json" //json格式
const contentTypeFile="multipart/form-data" //文件

// 这里封装一下axios 要不然每次使用都要调用axios 代码很多
const request=(config)=>{ //传递的参数
    let {url,params,dataType='form',showLoading='true'}=config //解构出这些参数
    let contentType=contentTypeForm //默认上传表单
    if(dataType==='json') {
        contentType=contentTypeJson
    } else if(dataType==='file') { //上传对象
        contentType=contentTypeFile
        // 对文件进行处理 利用formData对象把文件和额外参数加入
        let param=new FormData()
        for(let key in params) {
            param.append(key,params[key])
        }
        params=param
    }

    // 每次发送请求都创建axios实例
    const instance=axios.create({
        baseURL: '/api', //这里设置基地址 网上看到好像生产、发布阶段可以设置不同的URL
        timeout: 10*1000, //超时时间
        headers: { //头部信息
            'Content-Type': contentType, //上传不同格式文件有不同
            'X-Requested-With': 'XMLHttpRequest'
        }
    })

    let loading=null //页面有一个加载效果
    // 请求拦截器 发送请求进行一些处理后再发送 处理回调函数,错误回调函数
    instance.interceptors.request.use(
        (config)=>{
            if(showLoading) {
                
            }
            return config //一定要返回 不然报错
        },
        (err)=>{ //错误处理
            return Promise.reject(err)
        }
    )

    // 响应拦截器 对服务器返回结果进行处理
    instance.interceptors.response.use(
        (response)=>{
            return response
        },
        (err)=>{
            return Promise.reject(err)
        }
    )

    // 发送请求捕捉错误 可以另外写一个文件封装
    return instance.post(url,params).catch(error=>{
        console.log(error); //出现了什么错误
        return null //返回一个空对象
    })
}
export default request
```

然后在main.js文件中引入

```js
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
import request from './utils/request'

const app=createApp(App)
app.config.globalProperties.request=request
app.mount('#app')
```

在组件中使用

```vue
<script setup>
import {getCurrentInstance} from 'vue'
const {proxy} = getCurrentInstance()

const sbumit=async()=>{
    const result=await proxy.request({
            url: '地址',
            params: {//参数
                
            }
        })
}
</script>

<template>
  <div></div>
</template>

<style scoped>

</style>

```

