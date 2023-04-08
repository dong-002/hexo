---
title: hash模式和history模式
date: 2023-04-08
tags:
- 路由
categories:
- [vue]
- [路由]
---

### hash模式

hash模式就是把路径前面加多一个`#`，然后再连接到URL后面。#后面path的改变不会引起浏览器向服务器发送请求。

hash的原理是通过监听浏览器的`hashchange`事件，改变`window.location.hash`来改变hash值。

#### hash模式特点

* URL中会有一个`#`
* 改变URL中的hash值不会引起页面的重新加载
* 不利于SEO优化
* 刷新不会出现404

下面是vue-router中配置为hash模式，默认是hash模式

```js
import {createWebHashHistory,createRouter} from 'vue-router'
const router=createRouter({
    history: createWebHashHistory(),
    routes: []
})
```

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20230404173426735.png)

### history模式

history模式利用HTML5中history的`pushState()`方法和`replaceState()`方法改变URL。history模式改变URL同样也不会引起页面的重新加载，它会记录浏览器历史，点击后退按钮会回到上一个页面。

#### history模式特点

* URL中没有`#`
* 同样不会引起页面重新加载
* 找不到URL对应的页面会出现404

在vue-router中配置为history模式

```js
import {createWebHistory,createRouter} from 'vue-router'
const router=createRouter({
    history: createWebHistory(),
    routes: []
})
```

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20230404175434918.png)

#### 解决404问题

由于vue是单页面应用，只有一个页面，对于不同的URL请求找不到相应的资源会出现404的情况。为了解决这个问题，需要在服务器上进行配置，当URL没有匹配任何资源就返回`index.html`页面。

下面这个是Nginx的配置

```js
location / {
  try_files $uri $uri/ /index.html;
}
```

对上面try_files进行解读：try_files表示尝试读取服务器上静态文件，`$uri`是Nginx的一个变量，代表用户访问的地址，`$uri`表示访问的是一个文件，`$uri/`表示访问的是一个目录。try_files就是尝试到网站读取这个文件，有这个文件就返回；没有这个文件就查找这个目录，找到这个目录返回目录；如果没有找到目录返回最后一个参数`/index.html`，表示返回网站的index.html文件。