---
title: webpack介绍
date: 2022-10-06 22:23:56
tags: webpack
categories: 前端
---
webpack是一个可以将项目进行规范化、将项目打包压缩的工具

### 初始化

终端使用`npm init -y`在项目中建立package.json来记录项目，使用`npm i webpack webpack-cli -D`将webpack包放到devDependencies中，在项目根目录下建立一个webpack.config.js的文件

里面有两个参数可选，development在开发的时候用打包速度快 production在发布的时候使用 可以进行压缩 体积小

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220810001403153.png)

然后再package.json中找到scripts节点，添加如下属性

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220810001609715.png)

在终端中运行`npm run dev`命令，这样项目就自动打包，将src中的index.js的文件打包放到dist中的main.js中。可以在webpack.config.js文件中配置要打包的文件和放到哪里。

### 插件

#### webpack-dev-server

每次修改代码都要重新执行`npm run dev`这样很麻烦，可以安装插件当代码改变是自动执行，`npm i webpack-dev-server -D`下载插件然后再package.json中dev加多一个如下配置，但是要在http://localhost:8080/中打开。**并且HTML中引入的连接改为/index.js**，这是因为新生成的index.js在内存中，不在磁盘上。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220810164843354.png)

#### html-webpack-plugin

打开网址还要点到index.html文件中才能看到页面，通过`npm i html-webpack-plugin`安装插件，然后在webpack.config.js文件中配置

```js
// 导入构造函数
const HtmlPlugin=require('html-webpack-plugin')
// 创建实例对象
const htmlPlugin=new HtmlPlugin({
    template: './src/index.html', //指定文件
    filename: './index.html' //生成的文件
})


module.exports={
    // development是开发  production是上线后 两个参数可以选择
    //development在开发的时候用打包速度快 production在发布的时候使用 可以进行压缩 体积小
    mode: 'development',
    plugins: [htmlPlugin], //使htmlPlugin生效
}
```

这样`npm run dev`然后打开http://localhost:8080/就可以看到页面。

在module.exports中配置一个如下节点可以自动打开浏览器

```js
    devServer: {
        // 运行npm run dev自动打开浏览器
        open: true,
        // 端口号
        port: 8080,
        // 打开浏览器时用什么域名或IP地址
        host: 'localhost'
    }
```

#### loader

webpack只能处理js文件，其他不是js的文件不能处理，要通过loader进行打包处理。

所有的css,less文件不在index.html文件中引入，在index.js文件import引入，然后通过loader和webpack处理会自动引入到index.html文件中。![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220810214101643.png)

打包css文件：`npm i style-loader css-loader -D`下载插件，然后在webpack.config.js文件中加多一个节点

```js
module: {
        rules: [
            // 以.css后缀结尾的文件  使用的loader 顺序不能改变
            {test: /\.css$/,use:['style-loader','css-loader']}
        ]
    }
```

打包less文件：`npm i less-loader less -D`，然后在webpack.config.js文件中加多一个节点

```js
module: {
        rules: [
            // 以.css后缀结尾的文件  使用的loader
            {test: /\.css$/,use:['style-loader','css-loader']},
            {test: /\.less$/,use:['style-loader','css-loader','less-loader']}
        ]
    }
```

打包图片：`npm i url-loader file-loader -D`，然后在webpack.config.js文件中加多一个节点

```js
module: {
        rules: [
            // 以.css后缀结尾的文件  使用的loader
            {test: /\.css$/,use:['style-loader','css-loader']},
            {test: /\.less$/,use:['style-loader','css-loader','less-loader']},
            // 图片                      表示小于等于多少字节才转换为base64格式
            {test: /\.jpg|png|gif$/,use: 'url-loader?limit=10000'}
        ]
    }
```

处理高级的ES6语法：`npm i babel-loader @babel/core @babel/plugin-proposal-decorators -D`，添加节点

```js
// 将ES6高级语法处理 除掉第三方包不用管
{test: /\.js$/,use:'babel-loader',exclude: /node_modules/}
```

在根目录下新建babel.config.js文件，配置如下：

```js
module.exports={
    plugins: [['@babel/plugin-proposal-decorators',{legacy: true}]]
}
```

==其实在开发中很少自己配置webpack，一般用插件一键生成的，重点在于理解==

#### 进行发布

项目完成后要将项目进行发布，在package.json中配置如下，--mode production指的是将代码压缩，然后执行`npm run build`，在根目录下出现一个dist文件夹里面是进行压缩的文件。`npm run dev`是在开发阶段使用，添加**serve**打包的代码会存储在内存中，build属性中没有serve，执行`npm run bulid`会在磁盘上生成dist文件夹。

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220811002432483.png)

#### source-map

在调试时显示出错的行号和源码行号对应不上，因此在webpack.config.js文件中配置节点

```js
// 在开发阶段可以查看出错的行号
devtool: 'eval-source-map',

//在发布时上面的做法会显示源码 改为下面的
devtool: 'nosources-source-map'
```

