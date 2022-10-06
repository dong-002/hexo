---
title: npm的使用
date: 2022-10-06 22:08:21
tags: npm
categories: 
- [技能学习]
- [前端]
---
npm是一个包管理工具，所谓的包也就是第三方模块，好比如jQuery这些，当然我们也可以自定义模块，但是在实际应用中都是导入包来实现功能。npm是跟随着node.js的下载而有的，可以通过npm -v来查看版本号。

#### 项目新建

在新建项目时在项目根目录用终端执行npm init -y来创建一个package.json文件，这个文件用来记录这个项目用到了哪些包，这些包都在node_modules文件中存放。

![image-20220731171811789](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220731171811789.png)

#### 下载包

项目中由于包占用的内存太大，用git提交项目要把node_modules文件包含在gitignore中，这样就省略所用的包，只是包源码提交，从Gitee或GitHub下载项目只是源码，要在终端执行`npm install`命令就会自动下载项目所依赖的包，这样项目才能跑起来。

package.json文件中的dependencies记录了下载了哪些包和版本号，例如下面就是用到了jQuery包。

![image-20220731172100367](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220731172100367.png)

#### 存放到Dev

在开发用到的包但是在项目上线后不再使用放到devDependencies节点中，在项目上线后还要使用的就放到dependencies节点中。

使用npm i 包名 -D这样就会自动把包放到Dev节点中。

![image-20220731172943102](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220731172943102.png)

#### 删除包

不使用包可以通过npm unistall 包名来删除对应的包，这样还会在dependencies中删除包的名字。

---

#### 淘宝镜像npm

由于原来的npm下载包是在国外的服务器所以下载得比较慢，但是在国内淘宝做了一个镜像同步国外的npm，我们可以通过切换下载的镜像源为淘宝，这样就可以提高速度。

查看镜像源 `npm config get registry`

将镜像源改为淘宝镜像 `npm config set registry=https://registry.npm.taobao.org/`

![image-20220731174912803](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220731174912803.png)

将镜像源切换为国内之后下载速度就会提高。