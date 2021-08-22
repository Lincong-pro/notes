[toc]

# 黑马vue2021

## 前端工程化

### 工程化概述

1. 小白

   * HTML+CSS+Javascript

   * boostrap美化样式
   * 操作DOM或发起Ajax请求，jQuery
   * Layui实现网页布局效果

2. 实际的前端开发

   * 模块化（js模块化、css模块、资源的模块化）
   * 组件化（复用UI结构、样式、行为）
   * 规范化（代码约束、格式、接口规范化、Git分支管理）
   * 自动化（自动构建、自动部署、自动测试）

```js
	前端工程化：在企业级的前端项目开发中，把前端开发所需的工具、技术、流程、经验等进行规范化、标准化。
```

### 前端工程化解决方案

1. webpack[打包工具]('www.webpackjs.com')
2. parcel[打包工具]('https://www.parceljs.cn/')

## webpack基本使用

### webpack概览

```js
	webpack:前端工程的具体解决方案，它提供了友好的前端模块化开发支持，以及代码压缩混淆、处理浏览器JavaScript的兼容性、性能优化等强大的功能。
```

* 兼容性：Chrome能够解析ES6语法，但是IE8不能解析ES6语法。如箭头函数、let关键字、require、export之类的。
* 优点：工作重心放在具体功能的实现上，提高了前端开发效率和项目的可维护性。

### 创建列表隔行变色项目

#### 项目初始化

1. 创建空白目录，运行npm init -y，初始化包管理配置文件package.json
2. 新建src源代码目录
3. 新建src->index.html首页和src->index.js脚本文件
4. 初始化首页基本的结构
5. 运行npm install jquery -S,安装jquery，其中的-S参数会将其记录在package.json下面的dependency下面去，完整名字为**--save**。
6. 通过ES6模块化的方式导入jQuery，实现列表隔行变色效果

#### 安装webpack

* npm install webpack@5.42.1 webpack-cli@4.7.2 -D (其中npm使用@指定版本)
* 其中-D指定参数之后，npm安装包的位置会存在devDependencies下面，为**--save-dev**的简写形式
* dependencies:上线和运行阶段都需要的包
* devDependencies:开发阶段需要的包

#### webpack.config.js文件mode详解

1. production 
   * 打包时间长，体积小
2. development
   * 打包时间短，体积大

#### 配置webpack

+ 在项目根目录下创建名为**webpack.config.js**的webpack配置文件，并初始化如下的基本配置

  ```js
  // node.js中模块的导出方式
  module.exports = {
  	mode: 'development' // development和production选项
  }
  ```

+ 在package.json中script节点下添加**"dev": "webpack"**，之后就可以使用**npm run dev**的方式运行webpack

+ 终端中使用**npm run dev**

#### 第一个项目代码

##### index.js

```js
import $ from 'jquery'

$(function(){
    // 实现隔行换颜色的效果
    $('li:odd').css('background-color','red')
    $('li:even').css('background-color','blue')
})
```

##### index.html

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>第一个模块化项目</title>
    <script src="../dist/main.js"></script>
</head>
<body>
    <div class="container">
        <ul>
            <li>第1个</li>
            <li>第2个</li>
            <li>第3个</li>
            <li>第4个</li>
            <li>第5个</li>
            <li>第6个</li>
            <li>第7个</li>
            <li>第8个</li>
            <li>第9个</li>
        </ul>
    </div>
</body>
</html>
```

##### webpack.config.js

```js
// 使用node.js的导出语法向外界导出webpack配置文件
module.exports = {
    // mode代表webpack的运行模式
    mode: 'development',
}
```

##### package.json

```json
{
  "name": "firstproj",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "webpack" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "jquery": "^3.6.0"
  },
  "devDependencies": {
    "webpack": "^5.42.1",
    "webpack-cli": "^4.7.2"
  }
}
```

### webpack详解

#### webpack默认约定

​	webpack 4.x 和 5.x 的版本中，有如下默认规定：

* 默认的打包入口文件为src->index.js
* 默认的输出文件路径为dist->main.js
* webpack.config.js中修改打包的默认约定

#### webpack指定entry和output

##### webpack.config.js引入的node.js模块

```js
const path = require('path') // node.js中的模块使用require进行导入，而非ES6的语法
```

##### entry

```js
 entry: path.join(__dirname,'src/index.js'), // 使用node.js中的内置模块path进行操作
```

##### output

```js
 // 指定生成的文件放置的地方
    output: {
        path: path.join(__dirname, 'dist'),
        filename: "bundle.js",
    }
```

#### webpack插件

**目的：**拓展webpack的能力，从而让webpack用起来更方便，常用的插件有如下两个

* webpack-dev-server
  + 类似于node.js阶段的nodemon工具（监听node.js的源代码的变动重启node项目）
  + 每当修改了源代码，webpack自动进行项目的打包和构建
* html-webpack-plugin
  + webpack中的HTML插件
  + 可以通过此插件定制index.html页面的内容

##### 安装并配置插件

* ```shell
  npm i webpack-dev-server -D
  ```

* 修改package.json中的script选项

  ```json
  "dev": "webpack serve" // script节点下的脚本，可以通过npm run执行
  ```

* 再次运行npm run dev命令，重新进行项目的打包

* 在浏览器中访问[网页](localhost:8080)地址，查看自动打包效果

##### 配置js地址

* webpack-dev-server的原理就是在用户保存之后自动进行js的插入和更新，并不需要自己去重新打包或者启动，其访问方式为http模式而非文件路径的模式，访问网址为[网址]('localhost:8080')。
* 同时由于其生成的路径为/,所以就是/bundle.js，但是实际上在项目路径下并不会生成bundle.js，其中的bundle.js是生成在内存中，速度非常快。
* 配置好之后无需刷新浏览器自动更新。

##### 安装和配置html插件

###### 目的

* webpack-dev-server插件自动访问的是项目根目录，需要程序员再点击src目录才能进入index.html
* 为了满足自动化，自动拷贝一份在项目根目录，这样一打开浏览器就是index.html
* 同样是在内存中存放而不会存放在磁盘中
* HTML插件生成的页面中自动引入bundle.js

###### 安装命令

```shell
npm install html-webpack-plugin@5.3.2 -D
```

######  配置步骤

* webpack.config.js引入插件导出的构造函数

  ```js
  const Htmlplugin = require('html-webpack-plugin')
  ```

* 使用构造函数创建对象，创建对象返回的时候可以传递一些配置项

  ```js
  const htmlplugin = new Htmlplugin({
      template: './src/index.html', // 指定源文件的存放路径
      filename: './index.html' // 指定输出文件的存放路径
  })
  ```

* 为webpack的运行添加插件

  ```js
  plugins: [htmlplugin]
  ```

##### webpack配置devServer

```js
// 该节点为mode\plugins\output\entry的平行节点   
devServer: {
        open: true, // 初次打包完成之后打开浏览器
        host: '127.0.0.1', // 实时打包所使用的主机地址
        port: 81, // 实时打包所使用的端口号
    }
```

#### webpack中loader

##### why

* webpack默认只能处理\\*.js$的文件
* webpack如遇到\\*.css$  \\*.less$ 的文件，只能转交给相应的loader进行处理
* webpack如遇到高级的js语法无法处理，转交babel-loader进行处理，如果rules数组中无对应的loader就进行报错

##### css-loader

###### 安装loader

```shell
npm i css-loader
npm i style-loader
```

###### 配置webpack中的module选项

```js
    module: {
        rules: [
          {
            test: /\.css$/i,
            use: ["style-loader", "css-loader"],
          },
       ]
    }
```

* test表示匹配的文件类型
* use表示对应要调用的loader
* use数组中指定的loader顺序是固定的
* 多个loader的调用顺序是：从后往前调用

###### index.js中引入css文件

```js
// 在webpack所有的文件都可以使用ES6语法进行导入而不需要在html中进行复杂的配置
import './css/index.css'
```

###### webpack处理css文件过程

1. webpack发现处理不了的css文件，自动查找webpack.config.js这个配置文件，检查module.rules数组中是否配置了对应的loader加载器
2. webpack将index.css这个文件，转交最后一个loader进行处理
3. css-loader处理完毕之后将处理的结果转交给style-loader处理
4. style-loader处理完毕之后，若没有下一个loader就将处理的结果转交webpack
5. webpack把style-loader处理的结果合并到/dist/bundle.js中，最终生成打包好的文件

##### less-loader

###### 安装loader

```shell
npm i css-loader style-loader less-loader less
```

* 其中less是less-loader的必要前置核心包

###### 配置webpack中的module选项

```js
 module: {
        rules: [
          {
            test: /\.css$/i,
            use: ["style-loader", "css-loader"],
          },
          {
            test: /\.less$/i,
            use: [
              {
                loader: "style-loader",
              },
              {
                loader: "css-loader",
              },
              {
                loader: "less-loader",
                options: {
                  lessOptions: {
                    strictMath: true,
                  },
                },
              },
            ],
          }
        ],
    },
```

###### index.js中引入less文件

```js
import "./css/index.less";
```

##### 图片资源loader

###### base64编码引入图片

* 不需要多次请求
* 文件体积变大

###### 安装loader

```shell
npm i file-loader url-loader -D
```

###### 配置webpack中的module选项

```js
     {
        test: /\.(png|jpg|gif)$/i,
        use: [
          {
            loader: "url-loader?limit=22229",
            options: {
              mimetype: "image/png",
            },
          },
        ],
      },
```

* 值得注意的是，use后面如果只有一个loader，就可以不使用数组
* url-loader?limit=22229，其中的limit=22229代表着传递给url-loader的参数
* limit用来指定图片的大小，单位是字节（byte）
* 只有≤limit大小的图片，才会被转为base64格式的图片
* 之所以有这个参数是因为图片资源大了之后会丧失性能

##### 处理高级JS的loader

###### 安装loader

```shell
npm i babel-loader @babel/core @babel/plugin-proposal-decorators -D
```

###### 高级JavaScript代码

```js
function info(target) {
  target.info = "lincong";
}
// 装饰器语法---比较高级，webpack无法处理
@info
class Person {}
console.log(Person.info);
```

###### 配置webpack中的module选项

```js
  {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
      }
}
```

* exclude包含要排除的文件夹

###### 根目录添加babel.config.js

```js
module.exports = {
  // 声明babel可用的插件
  // 将来，webpack在调用babel-loader的时候，会先加载插件
  plugins: [["@babel/plugin-proposal-decorators", { legacy: true }]],
};
```

+ babel.config.js和webpack.config.js位于同一目录

###### loader调用的路径

```js
webpack -> babel-loader -> @babel/plugin-proposal-decorators 
其中，@babel/plugin-proposal-decorators 是babel-loader的插件
```

* babel插件[官网]('https://babeljs.io/docs/en/babel-plugin-proposal-decorators')

#### webpack打包项目上线

##### package.json中添加script脚本

```json
  "scripts": {
    "dev": "webpack serve",
    "build": "webpack --mode production"
  },
```

##### 执行打包

```shell
npm run build # node解析该命令，最后替换为build这个key对应的value
```

* **--model**是一个参数项，用来指定webpakc的运行模式。production代表生产环境，会对打包生成的文件进行代码压缩和性能优化。
* 通过--model指定的参数项，会覆盖webpack.config.js中的model选项。

##### 优化项目结构

###### 修改输出js输出文件路径

```js
  output: {
    path: path.join(__dirname, "dist"),
    filename: "js/bundle.js", //标准化输出到js文件夹中
  },
```

###### 修改引入图像文件路径

* 方式一

  ```js
        {
          test: /\.(png|jpg|gif)$/i,
          use: [
            {
              loader: "url-loader",
              options: {
                limit: 22222,
                outputPath: "images",
              },
            },
          ],
        },
  ```

* 方式二

  ```js
        {
          test: /\.(png|jpg|gif)$/i,
          use: 'url-loader?limit=22222&outputPath=images'
        },
  ```

##### 优化项目的构建（自动删除上一次）

###### clean-webpack-plugin插件安装

```shell
npm i clean-webpack-plugin -D
```

###### 插件配置

```js
const { CleanWebpackPlugin } = require("clean-webpack-plugin"); // 其中请注意这个名字是不可以改变的
const cleanPlugin = new CleanWebpackPlugin(); // 使用创建插件对象
module.exports = {
    // 步骤三，为webpack的运行添加插件
  plugins: [htmlplugin, cleanPlugin],
} 
```

* 解构操作

  ```js
  const { CleanWebpackPlugin } = require("clean-webpack-plugin"); // 其中请注意这个名字是不可以改变的
  const obj = require("clean-webpack-plugin");
  // obj含有CleanWebpackPlugin属性，而上面的大括号就是直接取出该属性
  ```

##### webpack配置Source Map

###### Source Map概述

* Source Map就是一个信息文件，里面存储着位置信息。也就是说，Source Map文件中存储着压缩混淆后的代码所对应的转换前的位置。
* 利用Source Map，出错之后将直接显示原始代码，而不是转换后的代码，能够极大的方便后期调试

###### Source Map问题

* 开发环境下默认生成的Source Map，记录的是生成后的代码的位置。会导致运行时报错的行数与源代码的行数不一致的问题。

###### 解决默认Source Map问题

* 开发环境下，推荐在webpack.config.js中添加如下配置，即可保证运行时报错的行数和源代码的行数保持一致：

  ```js
  module.exports = {
  	mode: 'development',
      // eval-source-map仅限在"开发模式"下使用，不建议在“生产模式”下使用
      // 此选项生成的Source Map 能够保证运行时报错的行数与源代码的行数保持一致
  	devtool: 'eval-source-map'
  }
  ```

###### Source Map带来的安全性问题

* 如果在生产环境下使用Source Map之后我们的源代码就很容易暴露在程序员面前，版权很容易被窃取。使用bundle.js的压缩版本就可以很大程度上保证原始代码通过Source Mao的形式暴露，所以一定要省略devtool选项。

* 若想让用户定位错误并禁止查看对应的错误代码使用如下配置

  ```js
  devtool: 'nosources-source-map'
  ```

* 若想用户定位错误并查看对应的错误行使用如下配置

  ```js
  devtool: 'source-map'
  ```

* 省略devtool选项发布用户可以定位到出错地方，但是无法显示错误行

##### 路径美化问题

###### 别名配置

* **webpack.config.js添加resolve节点**

  ```js
    resolve: {
      alias: {
        // 告诉webpack，程序员写的代码中，@表示的是src目录绝对路径
        "@": path.join(__dirname, "./src/"),
      },
    },
  ```

###### 替换相对路径

```js
import "@/css/index.css";
import "@/css/index.less";
import "@/js/test1/msg.js";

// 导入图片
import logo from "@/images/logo.png";
```

## vue

### vue开发准备

* vue devtools chrome插件安装
* 设置扩展插件的选项，允许访问文件网址

### vue概述

#### 官方定义

* vue是一套用于构建用户界面的前端框架
  1. 构建用户界面：用vue往html页面中填充数据，非常方便
  2. **框架**
     1. 框架是一套现成的解决方案，程序员只能遵守框架的规范，去编写自己的业务功能
     2. 要学习vue，就是在学习vue框架中规定的用法
     3. vue的指令、组件（是对UI结构的复用）、路由、Vuex、vue组件库
     4. 只有把上面的内容掌握之后，才有开发vue项目的能力

#### vue的特性

##### 数据驱动视图

* 在vue页面中，vue会监听数据的变化，从而自动重新渲染页面的结构
  * 好处：当页面数据发生变化时，页面会自动重新渲染！
  * 注意：数据驱动视图是单向的数据绑定

##### 双向数据绑定

* 在填写表单时，双向数据绑定可以辅助开发者在不操作DOM的前提下，自动把用户填写的内容同步到数据源中，然后**开发者不再需要手动操作DOM元素就可获取表单元素最新的值**！

> 在网页中，form表单负责采集数据，Ajax负责提交数据

* js数据变化，会被vue自动渲染到页面上
* 页面的值发生变化，会被vue自动同步更改依赖数据

#### MVVM

##### 概述

> ​		MVVM是vue实现数据驱动视图和双向数据绑定的核心原理。MVVM指的是Model、View、ViewModel，它把每个HTML页面都拆分成了这三个部分。

##### MMVM的工作原理

> ​		ViewModel作为MVVM的核心，是它把当。前页面的数据源（Model）和页面的结构（View）连接在了一起。当数据源发生变化时，会被ViewModel监听到，VM会根据最新的数据源自动更新页面的结构。当表单元素的值发生变化时，也会被VM监听到，VM会把变化过后最新的值自动同步到Model数据源中。

#### Vue版本历史

1. > 1.x版本的vue几乎被淘汰，不建议学习与使用（2016）

2. > 2.x版本的vue是目前企业级项目开发中的主流版本，未来1-2年会被逐渐淘汰

3. > 3.x版本的vue于2020-09-19发布，生态还不完善，尚未在企业级项目开发中普及和推广

#### Vue初体验

##### vue.js的export构造函数Vue

> 通过对函数以构造的形式进行调用，传入一个对象，其中对象控制着DOM元素目标值以及对象的数据

```js
    <script>
      const vm = new Vue({
        el: "#app",
        data: { hello: "Hello,Vue2" },
      });
    </script>
```

* el:  待操作的DOM元素，类似于像CSS的方式指明待操作的DOM对象，但是若使用p标签之类的html含有多个的这种情况，其只会控制第一个。
* data:  数据对象，以键值对的方式进行传入

##### {{}}引入数据

```html
<div id="app">{{hello}}</div>
```

##### MVVM设计嵌入

* html页面部分是View视图区域
* el指向的选择器，就是View视图区域
* data指向的对象，就是Model数据源
* new Vue()构造函数，得到的vm实例对象就是ViewModel

##### vue插件的使用

###### 数据驱动视图

![image-20210808101233998](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210808101233998.png?token=AQBB52H3EX4E7BU72QRADFLBECUUG)

#### Vue指令

> 指令(Directives)是vue为开发者提供的模板语法，用于辅助开发者渲染页面的基本结构。指令是vue开发最基础、最常用、最简单的知识点。

##### 内容渲染指令

> 主要针对标签中的内容插值，vue给出了三种方式进行插值，即插值表达式只用于内容修改

###### v-text

```html
   <div id="app">
      <h5 v-text="name"></h5>
      <h5 v-text="info">女</h5>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          name: "林聪",
          info: "男",
        },
      });
    </script>
```

* 缺点：v-text会完全替换内容区域的文本

###### {{ }}

```html
   <div id="app">
      <!-- <h5 v-text="name"></h5>
      <h5 v-text="info">女</h5> -->

      <h5>{{ name }}</h5>
      <h5>女{{ info }}</h5>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          name: "林聪",
          info: "男",
        },
      });
    </script>
```

* 缺点：{{ }}并不能解析html标签内容

###### v-html

```html
    <div id="app">
      <!-- <h5 v-text="name"></h5>
      <h5 v-text="info">女</h5> -->

      <!-- <h5>{{ name }}</h5>
      <h5>女{{ info }}</h5> -->

      <h5>{{ name }}</h5>
      <h5 v-html="hcontent">{{info}}</h5>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          name: "林聪",
          info: "男",
          hcontent:
            '<p style="color:red;font-weight:900">欢迎第一天学习vue!</p>',
        },
      });
    </script>
```

* 能解析html标签但仍然会像v-text一样替换原内容

##### 属性绑定指令

> 插值表达式即内容渲染指令只能用在元素的内容节点中，不能用在元素的属性节点中

###### v-bind:

* v-bind:用于为元素的属性动态绑定值
* v-bind:的简写形式就是:

```html
   <div id="app">
      <input type="text" name="" id="" :placeholder="text_placeholder" />
      <hr style="opacity: 0" />
      <img v-bind:src="photo" style="width: 150px" alt="" />
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          photo: "https://vuejs.org/images/logo.svg",
          text_placeholder: "请输入名字",
        },
      });
    </script>
```

##### 属性绑定和内容插值中使用js语法

```js
    <div id="app">
      <strong :class="myname+'1'+age"
        >{{hello.split('').reverse().join('')}}</strong
      >
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          hello: "Hello,Vue2",
          myname: "lincong",
          age: "21",
        },
      });
    </script>
```

* 在属性绑定和内容插值语法中使用js语法进行操作需要特别注意的就是单引号与双引号的问题，其中单引号就是内容而未用单引号涵盖的值就会使用data中的数据进行替换。

##### 事件绑定指令

```html
        <div id="app">
      <!--如果要绑定参数，直接使用()方式进行传参，若没有参数直接写待调用的函数名字即可-->
      <input type="button" :value="count" v-on:click="add(2)" />
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: { count: 1 },
        methods: {
          add: function add(n) {
            this.count += n;
          },
          subtract(n) {
            this.count -= n;
          },
        },
      });
    </script>
```

* 注意：属性名：匿名函数可以简写为 具名函数的形式
*  v-on的简写形式为@，即v-on:click可以简写为@click
* 原生DOM对象有onclick\oninput\oninput等原生事件，替换为vue事件绑定形式后分别为v-on:click\v-on:input\v-on:keyup
* 原生DOM事件[参考网址](https://www.w3school.com.cn/jquery/jquery_ref_events.asp)

###### 事件的默认参数event对象

> 当我们绑定的事件函数不需要参数的时候，我们可以设置一个默认参数e，用于获取参数中的event对象，比如下面这样。

```html
    <div id="app">
      <button @click="add">{{count}}</button>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          count: 1,
        },
        methods: {
          add(e) {
            console.log(e);
            this.count += 1;
          },
        },
      });
    </script>
```

* 当然如果不想在函数上写一个没必要的参数e，也可以使用默认的event对象，就像es5中的arguments伪数组一样。

  ```js
  console.log(event)
  ```

###### vue中封装的$event事件对象

> 需要注意的一点就是当我们传递参数的时候，会忽略掉我们的事件对象参数，这个时候只有使用$event代替参数传递才可以正确使用。

```html
   <div id="app">
      <!--传递参数的时候将$event传递，$event为vue内置的对象-->
      <button @click="sub($event,1)">{{count}}</button>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          count: 1,
        },
        methods: {
          add(e) {
            console.log(e);
            this.count += 1;
          },
          sub(e, n) {
            console.log(event);
            console.log(e);
            this.count -= n;
          },
        },
      });
    </script>
```

* 值得注意的是，$event一定是第一个参数

###### 事件修饰符

> 在事件处理函数中调用event.preventDefault()或event.stopPropagation()是非常常见的需求。因此，vue提供了事件修饰符的概念来辅助程序员更方便的对事件的触发进行控制。

```html
 <!--原生阻止事件默认行为的方法-->
	<div id="app">
      <a
        href="http://www.baidu.com"
        style="text-decoration: none"
        @click="show"
        v-text="hello"
      ></a>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: { hello: "事件默认行为" },
        methods: {
          show() {
            event.preventDefault(); //阻止事件的默认行为的原生方法
            console.log("阻止事件的默认行为");
          },
        },
      });
    </script>
```

```html
<!--vue阻止事件默认行为的方法-->    
	<div id="app">
      <a
        href="http://www.baidu.com"
        style="text-decoration: none"
        @click.prevent="show"
        v-text="hello"
      ></a>
    </div>
```

```html
<!--阻止事件冒泡-->
	<div id="app">
      <a
        href="http://www.baidu.com"
        style="text-decoration: none"
        @click.prevent="show"
        v-text="hello"
      ></a>
      <hr />
      <div
        @click="divHandler"
        style="height: 100px; width: 100%; background-color: pink"
      >
        <button @click.stop="btnHandler">click</button>
      </div>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: { hello: "事件默认行为" },
        methods: {
          show() {
            // event.preventDefault(); //阻止事件的默认行为的原生方法
            console.log("阻止事件的默认行为");
          },
          btnHandler() {
            event.stopPropagration(); //原生方法防止事件冒泡
            console.log("btnHandler");
          },
          divHandler() {
            console.log("divHandler");
          },
        },
      });
    </script>
```

* .prevent 阻止默认行为（例如：阻止a链接的跳转、阻止表单的提交等）
* .stop 阻止事件冒泡
* .capture 以捕获模式触发当前的事件处理函数
* .once 绑定的事件只触发1次
* .self 只有在event.target是当前元素自身时触发事件处理函数

###### 按键修饰符

> 在监听键盘事件时，我们经常需要判断详细的按键。此时，可以为键盘相关的事件添加按键修饰符。

```html
  <div id="app">
      <input
        type="text"
        @keyup.esc="clean"
        @keyup.enter="commit"
        :placeholder="hello"
      />
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: { hello: "Hello,Vue2" },
        methods: {
          clean(e) {
            // 事件的target就是input，然后其value属性可以进行操作
            e.target.value = "";
          },
          commit(e) {
            console.log("提交了" + e.target.value);
          },
        },
      });
    </script>
```

##### 双向绑定指令

> vue提供了 v-model 双向数据绑定指令，用来辅助开发者在不操作DOM的前提下，快速获取表单的数据。

```html
    <div id="app">
      <form action="#">
        <!-- 双向数据绑定 -->
        <input type="text" v-model="username" style="display: block" />
        <!-- 单向数据绑定 -->
        <input type="text" :value="username" style="display: block" />
        <!-- select具有value属性,select会根据设置的value设置显示页面的默认值 -->
        <select v-model="city">
          <option value="null">请选择</option>
          <option value="上海">上海</option>
          <option value="北京">北京</option>
          <option value="深圳">深圳</option>
        </select>
      </form>
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          username: "Hello,Vue2",
          city: null,
        },
      });
    </script>
```

* v-model双向数据绑定只用于表单元素
* input输入框，type="radio"、type="checkbox"、type="button"
* textarea
* select		

###### v-model中的修饰符

> 为了方便对用户输入的内容进行处理，vue为v-model指令提供了3个修饰符。

* .number 自动将用户的输入值转为数值类型，当转化为number之后如遇到问题就会变为字符串
* .trim 自动过滤用户输入的首尾空白字符
* .lazy 在“change”时而非“input”时更新，需要点击输入框以外的地方才会更新到vue

```html
    <div id="app">
      username:
      <!-- 双向数据绑定 -->
      <input type="text" v-model.trim.lazy="username" />
      <!-- 单向数据绑定 -->
      <label :for="username" style="display: block; height: 20px"
        >{{username}}</label
      >
      age:
      <!-- 双向数据绑定，传递数据给vue，然后vue发起ajax请求发送到后台 -->
      <input type="text" v-model.number="age" />
    </div>
    <script>
      const vm = new Vue({
        el: "#app",
        data: {
          username: "",
          age: "",
        },
      });
    </script>
```

##### 条件渲染指令

##### 列表渲染指令

#### axios

[官方帮助文档]('https://axios-http.com/docs/api_intro')

> axios是一个专注于网络请求，前端圈最火的库。Vue和React都是用axios请求数据，主要基于Promise库对异步请求进行同步化封装。

##### axios基本语法

```js
      // result is also a Promise Object
      const result = axios(config).then(function (data) {
        console.log(data);
        // 这其中的data已经被axios进行了相应的封装
        console.log(data.data);
      });
```

##### axios返回的数据

> 返回的数据中，axios进行了严密的封装，该返回对象包含6个属性，其中包含

* 用户代码请求所设置的配置项
* 服务器返回的数据-**data属性**
* 请求的headers
* XMLHttpRequest请求状态（axios在服务端使用Node的http模块、浏览器端使用ajax进行封装）
* statusText文本状态

![image-20210817131454810](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210817131454810.png?token=AQBB52HOSRZ2U4HBOWAQXRLBECUVI)

##### axios传参数

* get请求使用params传递请求行参数
* post请求使用data传递请求体参数

```js
// 刷新图表显示---axios进行封装
async function refresh(url_param, _method) {
  let config;
  if (_method.toUpperCase() === "get") {
    config = {
      method: "get",
      url: url_param,
      // get请求使用params传递对象
      params: {
        ID: 12345,
      },
    };
  } else {
    config = {
      method: "post",
      url: url_param,
      // post请求使用data传递对象
      data: {
        ID: 12345,
      },
    };
  }

  // await只能等待Promise，最后返回的是Promise deal得到的数据（异步请求的数据）
  await axios(config).then(function (responsedata) {
    //将字符串转化为数组
    option.series[0].data = JSON.parse(responsedata.data.data); // 赋值数据
    myChart.setOption(option);
  });//undefine，因为我并没有resolve数据传送出去
  const { data } = await axios(config);//ajax请求返回的数据，axios封装得到,axios就是我们服务器封装的数据
}
```

###### axios解构赋值取数据

```js
 const { data： res } = await axios(config);//ajax请求返回的数据，axios封装得到,axios就是我们服务器封装的数据
```

##### axois请求具体写法

```js
axios.get('xx_url',{
    params: {id: 1}
})
axios.request(config)
axios.get(url[, config])
axios.delete(url[, config])
axios.head(url[, config])
axios.options(url[, config])
axios.post(url[, data[, config]])
axios.put(url[, data[, config]])
axios.patch(url[, data[, config]])
//在使用别名方法时， url、method、data 这些属性都不必在配置中指定
```

#### vue-cli

##### vue-cli概述

> vue-cli是Vue.js开发的标准工具。简化了程序员基于webpack创建工程化的Vue项目的过程。

* [中文官网]('https://cli.vuejs.org/zh/')

##### vue-cli安装

* vue-cli安装(vue2)

  ```shell
  npm install -g vue-cli
  ```

* @vue/cli安装(vue3)

  ```shell
  npm install -g @vue/cli
  ```

##### 创建vue项目

* > vue create demo
  >
  > ![image-20210818163932050](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210818163932050.png?token=AQBB52HKJW2LEHQNRZ64XJ3BECUWA)
  >
  > webpack配置插件选择
  >
  > ![image-20210818164026876](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210818164026876.png?token=AQBB52DT5PV7AAEAYBUJWY3BECUWI)
  >
  > Vue版本选择
  >
  > ![image-20210818164112761](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210818164112761.png?token=AQBB52CVZV2V2LWOCDQOJ4DBECUWO)
  >
  > CSS Loader配置项选择
  >
  > ![image-20210818164148201](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210818164148201.png?token=AQBB52GTYJDJ7VB26TWD4NTBECUWU)
  >
  > 选择Babel之类的插件放置在自己的配置文件中
  >
  > ![image-20210818164345129](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210818164345129.png?token=AQBB52HS6JN7AJR3IECYKH3BECUW6)

* > cd demo
  >
  > npm run serve

  

##### 安装插件Path Autocomplete

> 打开settings.json配置如下

```json
  //导入文件时是否携带文件的扩展名
  "path-autocomplete.extensionOnImport": true,
  //配置@的路径显示
  "path-autocomplete.pathMappings": {
    "@": "${folder}/src"
  },
```

* 注意，这个配置生效的前提一定要是打开自己的项目根文件夹，不然是无法提示的

##### 组件使用三步骤

1. import导入组件

   ```js
   //第一步导入视图
   import Left from "@/components/Left.vue";
   ```

2. 使用components节点注册组件

   ```js
   export default {
     name: "App",
     // 第二步注册组件
     components: {
       Left,
       Right,
     },
   };
   ```

   > 通过components注册的是私有子组件

3. 以标签形式使用刚才注册的主键

   ```vue
   <template>
     <div id="hello">
       <!--以标签的形式插入视图-->
       <Left></Left>
       <Right></Right>
     </div>
   </template>
   
   ```

##### 组件类型

> 私有组件：即每个vue都可以导入其余的vue组件，但是有一个问题就是需要多次进行进行导入

> 全局组件：在vue项目的main.js入口文件中，通过Vue.components()方法，可以注册全局组件。优点：一次注册导出使用，需要考虑进行全局注册的应该是频繁使用的。

###### 全局组件使用

##### 组件的Props

> props是组件的自定义属性，在封装通用组件的时候，合理地使用props可以极大的提高组件的复用性！

###### Count组件定义props

```js
<script>
export default {
  data() {
    return {
      message: "hello",
      cnt: 0,
    };
  },
  methods: {
    add() {
      this.cnt += 1;
      this.props.init += 1;
    },
  },
  props: ["init"],//array
};
</script>
```

###### 外部组件使用Count组件

###### Right.vue使用属性

```vue
<template>
  <div class="right">
    <h6>{{ Hello }}</h6>
    <Count init="9"></Count>
  </div>
</template>
```

![](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210821204907773.png?token=AQBB52DC6WSEKUMOILUGIGDBED3IG)

> 注意：如果通过**init="9"**的方式给其赋值，最后得到的是字符串类型

* 通过下面的方式变换为js语法进行输入之后就是数值类型

```vue
<template>
  <div class="right">
    <h6>{{ Hello }}</h6>
    <Count :init="9"></Count>
  </div>
</template>
```

![](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210821210227497.png?token=AQBB52G2HIFRDBZT6NHFMI3BED42G)

###### props属性是只读

> ​		文档中明确定义自定义的props属性是不可以进行改变的，但是实际上也是可以对其进行改变的，2021的vue课程是显示报错，但是实际操作之后并没有显示报错。

```vue
<template>
  <!--click后面接着原生js语法-->
  <button @click="init += 1">click me</button>
  <h5>{{ init }}</h5>
</template>

<script>
export default {
  props: ["init"],
};
</script>
```

###### 间接通过props属性获取不同的初值

```vue
<template>
  <button @click="add">click me</button>
  <h5>{{ cnt }}</h5>
</template>

<script>
export default {
  data() {
    return {
      message: "hello",
      cnt: this.init, // 这个的前提就是props属性一定是在data初始化之前进行赋值
    };
  },
  methods: {
    add() {
      this.cnt += 1;
    },
  },
  props: ["init"],
};
</script>
```

![](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210821230644854.png?token=AQBB52BWV5C7WLY7U4OSZRDBEELMG)

###### props默认值

```vue
<template>
  <div class="right">
    <h6>Hello</h6>
    <!-- 下面的情况就是外部在使用组件的时候未给props属性赋值 -->
    <Count></Count>
  </div>
</template>
```

> 上面的情况vue插件中输出的自定义属性值就是undefine

```vue
<script>
export default {
  data() {
    return {
      message: "hello",
      cnt: this.init, // 这个的前提就是props属性一定是在data初始化之前进行赋值
    };
  },
  methods: {
    add() {
      this.cnt += 1;
    },
  },
  // props从数组的自定义方式转变为对象的高级定义方式
  props: {
    //属性1
    init: {
      default: 0,
    },
  },
};
</script>
```

> 通过上面的设置props属性就默认设置为0

###### props自定义属性的值类型

```vue
<script>
export default {
  data() {
    return {
      message: "hello",
      cnt: this.init, // 这个的前提就是props属性一定是在data初始化之前进行赋值
    };
  },
  methods: {
    add() {
      this.cnt += 1;
    },
  },
  // props从数组的自定义方式转变为对象的高级定义方式
  props: {
    //属性1
    init: {
      default: 0,
      // Array Object Number Function Null Boolean,如果指定了该类型不执行就会发出警告
      type: Number,
    },
  },
};
</script>
```

![](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210821232626828.png?token=AQBB52GS7AYYRTKCIOY2FILBEENWK)

###### 属性必须赋值的选项

```js
// props从数组的自定义方式转变为对象的高级定义方式
  props: {
    //属性1
    init: {
      default: 0,
      // Array Object Number Function Null Boolean,如果指定了该类型不执行就会报错
      type: Number,
      // 必须校验项
      required: true,
    },
  },
```

> 警告信息如下

<img src="https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210821233539812.png?token=AQBB52FXYGHJPETLJXJQ26LBEEOYW" alt="image-20210821233539812" style="zoom:150%;" />

![test](https://raw.githubusercontent.com/Lincong-pro/C-Properties/master/img/image-20210821235001663.png?token=AQBB52F5BETRRLQ3A5YATV3BEEQOQ)

#### VuePress笔记文档部署

##### 小试牛刀

```shell
# 安装
npm install -g vuepress
# 新建一个 markdown 文件
echo '# Hello VuePress!' > README.md
# 开始写作
vuepress dev .
# 构建静态文件
vuepress build .
```

##### 快速上手VuePress

###### 修改package.json

```json
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

###### 部署vue项目

```shell
# VuePress 会在 http://localhost:8080 (opens new window)启动一个热重载的开发服务器。
npm run docs:dev
```

###### 默认的路由地址

| 文件的相对路径     | 页面路由地址   |
| ------------------ | -------------- |
| `/README.md`       | `/`            |
| `/guide/README.md` | `/guide/`      |
| `/config.md`       | `/config.html` |

