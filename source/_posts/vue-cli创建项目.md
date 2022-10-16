---
title: vue-cli创建项目
date: 2022-10-06 22:21:53
tags: vue
categories: 前端
---
在实际项目中都是用vue-cli进行搭建项目，不用自己配置webpack。

#### 安装

使用`npm i @vue/cli -g`命令全局安装vue-cli，如果下载慢的话可以换为淘宝镜像。下载完成可以查看版本号

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817090329853.png)



#### 创建项目

在想要创建项目的目录下输入cmd，然后在终端中使用命令`vue create 项目名称`

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817090031851.png)

我在终端中输入`vue create demo`然后出现了下面的选项，第一个是vue 3，第二个是vue 2，最后一个是手动配置，这里我们选择最后一个。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817090523474.png)

然后又出现了下面的选项，刚开始学习我们选中图中的两项，后面有需要再选择其他，按空格键进行选择

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817090916549.png)

后面出现下图选项，我们选择vue 2版本

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817091143760.png)

叫你选择哪个插件，这里选择less

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817091546255.png)

把选择的配置放到哪里，这里选择第一个放到一个独立的文件中

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817091701710.png)

是否选择把这次的配置保存下来，以后可以使用相同的配置

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817092223551.png)

填写一个名称，之后就会安装配置选项

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817092336121.png)

执行下面两个命令就可以将项目跑起来（可以使用vs code打开项目文件然后在终端执行`npm run serve`）

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817092850572.png)

不要关闭终端，关闭之后相当于把服务器关掉了。然后在浏览器中输入第一个local网址就可以看到项目

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220817093057572.png)

---

#### vite

上面的步骤太过繁琐，并且vue-cli的速度会比较慢，现在有了更好的vite。

首先安装`npm i vite -g`进行全局安装，然后执行`npm create vite@latest 项目名 -- --template vue`就可以创建一个项目，进入目录然后执行`npm i`下载依赖包，执行`npm run dev`运行项目

在使用element-plus的时候有全部导入和按需导入，在按需导入的时候出现了一些问题，记录一下。

首先创建vite.config.js文件，然后按照官网配置

```js
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
//这个包也要下载
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [
    // 这里是一定要的
    vue(),
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})

```

