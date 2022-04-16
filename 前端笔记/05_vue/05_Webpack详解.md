# Webpack详解

## 认识webpack

webpack是一个现代的JavaScript应用的静态模块打包工具。

![webpack](md/webpack.png)



### webpack中模块化

1. 帮助我们处理模块间的依赖关系，并且将其进行整合打包。
2. 不仅仅是JavaScript文件，我们的CSS、图片、json文件等等在webpack中都可以被当做模块来使用
3. 打包时，可以对资源进行处理，比如压缩图片，将scss转成css，将ES6语法转成ES5语法，将TypeScript转成JavaScript等等操作。

### grunt/gulp和webpack有什么不同

1. grunt/gulp更加强调的是前端流程的自动化，模块化不是它的核心。
2. webpack更加强调模块化开发管理，而文件压缩合并、预处理等功能，是他附带的功能。

## webpack的安装

安装webpack首先需要安装Node.js，Node.js自带了软件包管理工具npm

查看自己的node版本：`node -v`

全局安装webpack(这里我先指定版本号3.6.0，因为vue cli2依赖该版本)

`npm install webpack@3.6.0 -g`

局部安装webpack

--save-dev`是开发时依赖，项目打包后不需要继续使用的。

```shell
cd 项目目录
npm install webpack@3.6.0 --save-dev
```


为什么全局安装后，还需要局部安装呢？

在终端直接执行webpack命令，使用的全局安装的webpack

当在package.json中定义了scripts时，其中包含了webpack命令，那么使用的是局部webpack



## webpack的起步

### 文件和文件夹解析：

1. dist文件夹：用于存放之后打包的文件
2. src文件夹：用于存放我们写的源文件
   1. main.js：项目的入口文件。
   2. mathUtils.js：定义了一些数学工具函数，可以在其他地方引用，并且使用。
3. index.html：浏览器打开展示的首页html
4. package.json：通过npm init生成的，npm包管理的文件

### js文件的打包

使用webpack的指令`webpack src/main.js dist/bundle.js`

打包后会在dist文件下，生成一个bundle.js文件

bundle.js文件是webpack处理了项目直接文件依赖后生成的一个js文件，我们只需要将这个js文件在index.html中引入即可

## webpack的配置

### 入口和出口

创建一个webpack.config.js文件

```javascript
const path = require('path');
module.exports = {
  // 入口
  // 可以是 字符串/数组/对象
  entry: './src/main.js',
  // 出口
  // 对象包含 path 和 filename属性
  output: {
    // 绝对路径
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
}
```



### 局部安装webpack

因为一个项目往往依赖特定的webpack版本，全局的版本可能很这个项目的webpack版本不一致，导出打包出现问题。
所以通常一个项目，都有自己局部的webpack。

第一步，项目中需要安装自己局部的webpack

```shell
npm install webpack@3.6.0 --save-dev
```

第二步，通过node_modules/.bin/webpack启动webpack打包

```shell
node_modules/.bin/webpack
```



### package.json中定义启动

我们可以在package.json的scripts中定义自己的执行脚本。

```json
"scripts": {
	"build": "webpack"
}
```



package.json中的scripts的脚本在执行时，会按照一定的顺序寻找命令对应的位置。

首先，会寻找本地的node_modules/.bin路径中对应的命令。

如果没有找到，会去全局的环境变量中寻找。

执行build指令

`npm run build`

## loader的使用

webpack处理我们写的js代码，并且webpack会自动处理js之间相关的依赖。

webpack本身的能力来说，不支持的转化操作：

1. 加载css、图片
2. 将ES6转成ES5代码
3. 将TypeScript转成ES5代码
4. 将scss、less转成css
5. 将.jsx、.vue文件转成js文件

webpack扩展对应的 loader 来支持相应的转化

### loader使用过程

步骤一：通过npm安装需要使用的loader

步骤二：在webpack.config.js中的modules关键字下进行配置



### css文件处理 

在入口文件 main.js 中引入 css

```javascript
// main.js
require('./css/normal.css');
```

#### css-loader

css-loader 用来将css插入到页面的style标签

```shell
npm install css-loader --save-dev
```

css-loader先加载css文件，再由style-loader来进行进一步的处理，将css具体样式嵌入到文档中

#### style-loader

style-loader 用来处理css文件中的url()等

```shell
npm install style-loader --save-dev
```

###### 在 `webpack.config.js` 模块配置项中配置 `loaders`

webpack在读取使用的loader的过程中，是按照从右向左的顺序读取的。

```json
module.exports = {
  module:{
      rules: [
          {
              test: /\.css$/,      // 用正则找到需要处理的文件扩展名
              use: [ 'style-loader', 'css-loader' ],    // 需要使用的loader

              /** use属性也可以换成loader
              * loader: [ 'style-loader', 'css-loader' ],
              *
              * use 的值也可以是个对象
              * use: [
              *   {loader: 'style-loader'},
              *   {loader: 'css-loader'}
              * ],
              */
          }
      ]
  },
}
```



### less文件处理

#### 安装对应的loader。 less-loader，less

```shell
# "less": "^3.0.0",
# "less-loader": "^4.1.0",
npm install less-loader@4.1.0 less --save-dev
```



#### 添加一个rules选项，用于处理.less文件

```json
{
  test: /\.less$/,
  use: [{
    loader: "style-loader" // creates style nodes from JS strings
  },{
    loader: "css-loader" // translates CSS into CommonJS
  },{
    loader: "less-loader" // compiles Less to CSS
  }]
}
```

**引入**

```html
<!-- lang="less" -->
<style scoped lang="less">
@import './assets/css/index.css';
@import './assets/less/test.less';
</style>
```



### sass/scss文件处理

安装对应的loader。 less-loader，less

```sh
# "sass": "^1.49.9",
# sass-loader 高版本 需要 webpack4+
# sass-loader 低版本 需要 node-sass
# "sass-loader": "^7.3.1",
# "node-sass": "^4.13.1", 

npm install sass sass-loader@7.3.1 node-sass@4.13.1 -D
```



一旦安装，请找到你的 `webpack.config.js` 文件，并将下面的片段复制到 **rules** 。如果已经存在 **sass** 规则，你可能需要应用下面的一些或所有的更改。

```js
// webpack.config.js
module.exports = {
  rules: [
    {
      test: /\.s(c|a)ss$/,
      use: [
        'style-loader',
        'css-loader',
       	'sass-loader'
      ],
    },
  ],
}
```



**引入**

```html
<!-- lang="scss" -->
<style lang="scss" scoped>
@import './assets/scss/test.scss';
</style>
```



### 图片文件处理

图片处理，我们使用url-loader来处理，依然先安装url-loader

```shell
npm install --save-dev url-loader
```

当图片小于 limit属性大小(8kb)时，对图片进行base64编码，通过base64显示出来的

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
}
```

安装file-loader

```shell
npm install --save-dev file-loader
```



**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 8192,
              name: 'img/[name].[hash:8].[ext]'
            }
          }
        ]
      }
    ]
  }
}
```



在options中添加上如下选项：

img：文件要打包到的文件夹

name：获取图片原来的名字，放在该位置

hash:8：为了防止图片名称冲突，依然使用hash，但是我们只保留8位

ext：使用图片原来的扩展名

默认情况下，webpack会将生成的路径直接返回给使用者
但是，我们整个程序是打包在dist文件夹下的，所以这里我们需要在路径下再添加一个dist/

```javascript
const path = require('path');
module.exports = {
  // 入口
  // 可以是 字符串/数组/对象
  entry: './src/main.js',
  // 出口
  // 对象包含 path 和 filename属性
  output: {
    // 绝对路径
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
    publicPath: 'dist/'
  }
}
```





### ES6语法处理

使用babel对应的loader

```shell
npm install --save-dev babel-loader@7 babel-core babel-preset-es2015
```



```json
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['es2015']
        }
      }
    }
  ]
}
```



### 引入vue.js

```shell
npm install vue --save
```



## webpack中配置Vue

修改webpack的配置，添加如下内容即可

```json
resolve: {
  alias: {
    'vue$': 'vue/dist/vue.esm.js'
  }
}
```



## el和template模板的关系

1. el用于指定Vue要管理的DOM，可以帮助解析其中的指令、事件监听等等。
2. 在Vue实例中同时指定了template，那么template模板的内容会替换掉挂载的对应el的模板。

这样就不需要再次操作index.html，只需要在template中写入对应的标签即可。

直接书写template模块非常麻烦，再将template模板中的内容进行抽离。

分成三部分书写：template、script、style，结构变得非常清晰。

## Vue组件化开发引入

```html
<!-- App.vue -->
<template>
    <h2 class="title">{{name}}</h2>
</template>

<script>
export default ({
    name: 'App',
    data() {
        return {
            name: '.vue 的 App 组件'
        }
    }
})
</script>

<style scoped>
.title{
    color:blue;
}
</style>
```



```javascript
// main.js
new Vue({
  el: '#app',
  template: `
    <div id="app">
      {{message}}
      <App/>
    </div>`,
  data: {
    message: '孙悟空'
  },
  components: {
    App
  }
})
```

安装 vue-loader 和 vue-template-compiler 

```shell
# vue-template-compiler 和 vue 版本一致
npm install vue-loader vue-template-compiler --save-dev
```

修改 webpack.config.js 的配置文件

```json
{
  test: /\.vue$/,
  use: ['vue-loader']
}
```



## plugin的使用

### loader和plugin区别

1. loader主要用于转换某些类型的模块，它是一个转换器。
2. plugin是插件，它是对webpack本身的扩展，是一个扩展器。

plugin的使用过程：

步骤一：通过npm安装需要使用的plugins(某些webpack已经内置的插件不需要安装)

步骤二：在webpack.config.js中的plugins中配置插件。

### 添加版权的Plugin

为打包的文件添加版权声明

BannerPlugin，属于webpack自带的插件。

修改webpack.config.js的文件

```javascript
//...
const webpack = require('webpack');


module.exports = {
	// ...
	plugins: [
		new webpack.BannerPlugin("版权信息")
	]
}
```



### 打包html的plugin

将index.html文件打包到dist文件夹中，这个时候就可以使用HtmlWebpackPlugin插件。

HtmlWebpackPlugin插件可以为我们做这些事情：

1. 自动生成一个index.html文件(可以指定模板来生成)
2. 将打包的js文件，自动通过script标签插入到body中

安装HtmlWebpackPlugin

```shell
npm install html-webpack-plugin --save-dev
```



使用插件，修改webpack.config.js文件中plugins部分的内容如下：

```js
// ...
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	// ...
	plugins: [
		new webpack.BannerPlugin("版权信息"),
    new HtmlWebpackPlugin({
      template: 'index.html'
    })
	]
}
```

这里的template表示根据什么模板来生成index.html

另外，我们需要删除之前在output中添加的publicPath属性。否则插入的script标签中的src可能会有问题

### js压缩的Plugin

插件uglifyjs-webpack-plugin，并且版本号指定1.1.1，和CLI2保持一致

```shell
npm install uglifyjs-webpack-plugin@1.1.1 --save-dev
```



```javascript
const path = require('path');
const webpack = require('webpack');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
	// ...
	plugins: [
		new webpack.BannerPlugin("版权信息"),
    new htmlWebpackPlugin({
      template: 'index.html'
    }),
    new UglifyJsPlugin(),
	]
}

```



## 搭建本地服务器

webpack提供了一个可选的本地开发服务器，这个本地服务器基于node.js搭建，内部使用express框架，可以实现我们想要的让浏览器自动刷新显示我们修改后的结果。

不过它是一个单独的模块，在webpack中使用之前需要先安装它

```shell
npm install --save-dev webpack-dev-server@2.9.1
```

devserver也是作为webpack中的一个选项，选项本身可以设置如下属性：

1. contentBase：为哪一个文件夹提供本地服务，默认是根文件夹，我们这里要填写./dist
2. port：端口号
3. inline：页面实时刷新
4. historyApiFallback：在SPA页面中，依赖HTML5的history模式

webpack.config.js文件配置修改如下：

```json
devServer: {
  contentBase: path.join(__dirname, "dist"),
  compress: true,
  port: 9000,
  inline: true
}
```

我们可以再配置另外一个scripts：
--open参数表示直接打开浏览器

```json
"scripts": {
	"build": "webpack",
  "dev": "webpack-dev-server --open"
}
```

