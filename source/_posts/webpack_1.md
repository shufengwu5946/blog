---
title: webpack 学习笔记（一）
date: 2018-11-3 14:52:11
tags:
    - webpack
---

本文主要参考文章：

[《入门 Webpack，看这篇就够了》](https://segmentfault.com/a/1190000006178770?utm_source=tag-newest)

## 开始使用Webpack

我们一步步的开始学习使用Webpack。

### 安装

新建一个空的练习文件夹（此处命名为webpack-base-demo）。

切换到webpack-base-demo文件夹，在终端中使用npm init命令可以自动创建package.json文件，package.json文件这是一个标准的npm说明文件，里面蕴含了丰富的信息，包括当前项目的依赖模块，自定义的脚本任务等等。

```
npm init
```

<!--more-->

输入这个命令后，终端会问你一系列诸如项目名称，项目描述，作者等信息，不过不用担心，如果你不准备在npm中发布你的模块，这些问题的答案都不重要，回车默认即可。

package.json文件已经就绪，我们在本项目中安装Webpack作为依赖包
```
// 安装Webpack
npm install --save-dev webpack

// 安装webpack-cli
npm install --save-dev webpack-cli
```

回到webpack-base-demo文件夹，并在里面创建app文件夹，app文件夹用来存放原始数据和我们将写的JavaScript模块，接下来我们在app文件夹中创建两个文件:

```
index.js
index.css
```
index.js文件内容：

```javascript
window.onload = function(){
    document.getElementById('div1').textContent="Hello world";
}
```

在webpack-base-demo文件夹里面创建dist文件夹，dist文件夹中创建index.html，index.html文件中写入最基础的html代码：

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="div1" class="div1"></div>
    <script src="./index.js"></script>
</body>
</html>
```

### 通过配置文件来使用Webpack

Webpack拥有很多其它的比较高级的功能（比如说本文后面会介绍的loaders和plugins），这些功能其实都可以通过命令行模式实现，但是正如前面提到的，这样不太方便且容易出错的，更好的办法是定义一个配置文件，这个配置文件其实也是一个简单的JavaScript模块，我们可以把所有的与打包相关的信息放在里面。

继续上面的例子来说明如何写这个配置文件，在当前练习文件夹的根目录下新建一个名为webpack.config.js的文件，我们在其中写入如下所示的简单配置代码，目前的配置主要涉及到的内容是入口文件路径和打包后文件的存放路径。

```javascript
module.exports = {
    entry: __dirname + "/app/index.js", //已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist", //打包后的文件存放的地方
        filename: "index.js" //打包后输出文件的文件名
    }
}
```

> 注：“__dirname”是node.js中的一个全局变量，它指向当前执行脚本所在的目录。

在package.json中对scripts对象进行相关设置即可，设置方法如下。

```json
{
  "name": "webpack-base-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --mode development" // 修改的是这里，JSON文件不支持注释，引用时请清除
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.25.1",
    "webpack-cli": "^3.1.2"
  }
}
```

> 注：package.json中的script会安装一定顺序寻找命令对应位置，本地的node_modules/.bin路径就在这个寻找清单中，所以无论是全局还是局部安装的Webpack，你都不需要写前面那指明详细的路径了。

npm的start命令是一个特殊的脚本名称，其特殊性表现在，在命令行中使用npm start就可以执行其对于的命令，如果对应的此脚本名称不是start，想要在命令行中运行时，需要这样用npm run {script name}如npm run build。

现在只需要使用npm start就可以打包文件了,可以发现在dist文件夹中生成了index.js文件。

## Webpack的强大功能

### 生成Source Maps（使调试更容易）

开发总是离不开调试，方便的调试能极大的提高开发效率，不过有时候通过打包后的文件，你是不容易找到出错了的地方，对应的你写的代码的位置的，Source Maps就是来帮我们解决这个问题的。

通过简单的配置，webpack就可以在打包时为我们生成的source maps，这为我们提供了一种对应编译文件和源文件的方法，使得编译后的代码可读性更高，也更容易调试。

在webpack的配置文件中配置source maps，需要配置devtool，它有以下四种不同的配置选项，各具优缺点，描述如下：

devtool选项	|配置结果
-|-
source-map	|在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包速度；
cheap-module-source-map	|在一个单独的文件中生成一个不带列映射的map，不带列映射提高了打包速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；
eval-source-map	|使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。在开发阶段这是一个非常好的选项，在生产阶段则一定不要启用这个选项；
cheap-module-eval-source-map	|这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

正如上表所述，上述选项由上到下打包速度越来越快，不过同时也具有越来越多的负面作用，较快的打包速度的后果就是对打包后的文件的的执行有一定影响。

对小到中型的项目中，eval-source-map是一个很好的选项，再次强调你只应该开发阶段使用它，我们继续对上文新建的webpack.config.js，进行如下配置:

```javascript
module.exports = {
  devtool: 'eval-source-map',
  entry:  __dirname + "/app/index.js",
  output: {
    path: __dirname + "/dist",
    filename: "index.js"
  }
}
```
> cheap-module-eval-source-map方法构建速度更快，但是不利于调试，推荐在大型项目考虑时间成本时使用。

### 使用webpack构建本地服务器

想不想让你的浏览器监听你的代码的修改，并自动刷新显示修改后的结果，其实Webpack提供一个可选的本地开发服务器，这个本地服务器基于node.js构建，可以实现你想要的这些功能，不过它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖

```
npm install --save-dev webpack-dev-server
```

devserver作为webpack配置选项中的一项，以下是它的一些配置选项，更多配置可参考这里

devserver的配置选项	|功能描述
-|-
contentBase	|默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“dist"目录）
port	|设置默认监听端口，如果省略，默认为”8080“
inline	|设置为true，当源文件改变时会自动刷新页面
historyApiFallback	|在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html

把这些命令加到webpack的配置文件中，现在的配置文件webpack.config.js如下所示

```javascript
module.exports = {
  devtool: 'eval-source-map',
  entry:  __dirname + "/app/index.js",
  output: {
    path: __dirname + "/dist",
    filename: "index.js"
  },

  devServer: {
    contentBase: "./dist",//本地服务器所加载的页面所在的目录
    historyApiFallback: true,//不跳转
    inline: true//实时刷新
  } 
}
```

在package.json中的scripts对象中添加如下命令，用以开启本地服务器：

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --mode development",
    "server": "webpack-dev-server --open --mode development"
},
```

在终端中输入npm run server即可在本地的8080端口查看结果,成功后页面显示“Hello world”。

### Loaders

Loaders是webpack提供的最激动人心的功能之一了。通过使用不同的loader，webpack有能力调用外部的脚本或工具，实现对不同格式的文件的处理，比如说分析转换scss为css，或者把下一代的JS文件（ES6，ES7)转换为现代浏览器兼容的JS文件，对React的开发而言，合适的Loaders可以把React的中用到的JSX文件转换为JS文件。

Loaders需要单独安装并且需要在webpack.config.js中的modules关键字下进行配置，Loaders的配置包括以下几方面：

* test：一个用以匹配loaders所处理文件的拓展名的正则表达式（必须）
* loader：loader的名称（必须）
* include/exclude：手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
* query：为loaders提供额外的设置选项（可选）

不过在配置loader之前，我们把index.js里的“Hello world”放在一个单独的JSON文件里,并通过合适的配置使index.js可以读取该JSON文件的值，各文件修改后的代码如下：

在app文件夹中创建带有问候信息的JSON文件(命名为config.json)

```json
{
  "greetText": "Hello world"
}
```

更新后的index.js

```javascript
import config from './config.json';
window.onload = function(){
    document.getElementById('div1').textContent=config.greetText;
}
```

> **注** 由于webpack4.* / webpack3.* / webpack2.* 已经内置可处理JSON文件，这里我们无需再添加webpack1.*需要的json-loader。在看如何具体使用loader之前我们先看看Babel是什么？

### Babel

Babel其实是一个编译JavaScript的平台，它可以编译代码帮你达到以下目的：

* 让你能使用最新的JavaScript代码（ES6，ES7...），而不用管新标准是否被当前使用的浏览器完全支持；

* 让你能使用基于JavaScript进行了拓展的语言，比如React的JSX；

#### Babel的安装与配置

Babel其实是几个模块化的包，其核心功能位于称为babel-core的npm包中，webpack可以把其不同的包整合在一起使用，对于每一个你需要的功能或拓展，你都需要安装单独的包，用得最多的是解析Es6的babel-env-preset包。

我们先来一次性安装这些依赖包

```
// npm一次性安装多个依赖模块，模块之间用空格隔开
npm install --save-dev babel-core babel-loader babel-preset-env
```
在webpack中配置Babel的方法如下:

```javascript
module.exports = {
    devtool: 'eval-source-map',
    entry: __dirname + "/app/index.js", //已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist", //打包后的文件存放的地方
        filename: "index.js" //打包后输出文件的文件名
    },

    devServer: {
        contentBase: "./dist", //本地服务器所加载的页面所在的目录
        historyApiFallback: true, //不跳转
        inline: true //实时刷新
    },
    module: {
        rules: [{
            test: /(\.js)$/,
            use: {
                loader: "babel-loader",
                options: {
                    presets: [
                        "env"
                    ]
                }
            },
            exclude: /node_modules/
        }]
    }
}
```

现在你的webpack的配置已经允许你使用ES6了，运行npm start，提示如下错误：
```
ERROR in ./app/index.js
Module build failed (from ./node_modules/babel-loader/lib/index.js):
Error: Cannot find module '@babel/core'
 babel-loader@8 requires Babel 7.x (the package '@babel/core'). If you'd like to use Babel
6.x ('babel-core'), you should install 'babel-loader@7'.
    at Function.Module._resolveFilename (module.js:547:15)
    at Function.Module._load (module.js:474:25)
    at Module.require (module.js:596:17)
```
原因是版本babel-loader的版本为8.x，匹配的Babel的版本为7.x，查看package.json文件，babel-core的版本为6.26.3，我们采取的解决办法是将重新安装版本为7.x的babel-loader，重新运行npm start 就没问题了。

#### 增加React的支持

安装解析JSX的babel-preset-react包：

```
npm install --save-dev babel-preset-react
```
webpack.config.js中更新module如下：

```javascript
module: {
    rules: [{
        test: /(\.jsx|\.js)$/,
        use: {
            loader: "babel-loader",
            options: {
                presets: [
                    "env", "react"
                ]
            }
        },
        exclude: /node_modules/
    }]
}
```

现在webpack的配置已经允许使用JSX的语法了。继续用上面的例子进行测试，不过这次我们会使用React，记得先安装 React 和 React-DOM
```
npm install --save-dev react react-dom
```
接下来我们使用ES6的语法，APP文件夹中新建Greeter.js文件并返回一个React组件
```javascript
//Greeter.js
import React, {Component} from 'react'
import config from './config.json';

class Greeter extends Component{
  render() {
    return (
      <div>
        {config.greetText}
      </div>
    );
  }
}

export default Greeter
```
修改index.js如下，使用ES6的模块定义和渲染Greeter模块

```javascript
// index.js
import React from 'react';
import {render} from 'react-dom';
import Greeter from './Greeter';

render(<Greeter />, document.getElementById('div1'));
```

重新使用npm start打包，并且运行npm run server，你应该可以在localhost:8080下看到与之前一样的内容，这说明react被正常打包了。

#### Babel的配置

Babel其实可以完全在 webpack.config.js 中进行配置，但是考虑到babel具有非常多的配置选项，在单一的webpack.config.js文件中进行配置往往使得这个文件显得太复杂，因此一些开发者支持把babel的配置选项放在一个单独的名为 ".babelrc" 的配置文件中。我们现在的babel的配置并不算复杂，不过之后我们会再加一些东西，因此现在我们就提取出相关部分，分两个配置文件进行配置（webpack会自动调用.babelrc里的babel配置选项），如下：
```javascript
module.exports = {
    entry: __dirname + "/app/index.js",//已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist",//打包后的文件存放的地方
        filename: "index.js"//打包后输出文件的文件名
    },
    devtool: 'eval-source-map',
    devServer: {
        contentBase: "./dist",//本地服务器所加载的页面所在的目录
        historyApiFallback: true,//不跳转
        inline: true//实时刷新
    },
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            }
        ]
    }
};
```
```javascript
//.babelrc
{
  "presets": ["react", "env"]
}
```
到目前为止，我们已经知道了，对于模块，Webpack能提供非常强大的处理功能，那那些是模块呢。

## 一切皆模块

Webpack有一个不可不说的优点，它把所有的文件都都当做模块处理，JavaScript代码，CSS和fonts以及图片等等通过合适的loader都可以被处理。

### CSS
webpack提供两个工具处理样式表，css-loader 和 style-loader，二者处理的任务不同，css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能,style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。

继续上面的例子
```
//安装
npm install --save-dev style-loader css-loader
```
```javascript
//使用
module.exports = {

   ...
    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "style-loader"
                    }, {
                        loader: "css-loader"
                    }
                ]
            }
        ]
    }
};
```
请注意这里对同一个文件引入多个loader的方法。

接下来，在app文件夹里的index.css文件中，对一些元素设置样式

```css
/* index.css */
div{
    color:green;
}
```
我们这里例子中用到的webpack只有单一的入口，其它的模块需要通过 import, require, url等与入口文件建立其关联，为了让webpack能找到index.css文件，我们把它导入index.js中，如下

```javascript
//index.js
import React from 'react';
import {render} from 'react-dom';
import Greeter from './Greeter';
import './index.css'; //使用require导入css文件

render(<Greeter />, document.getElementById('div1'));
```
通常情况下，css会和js打包到同一个文件中，并不会打包为一个单独的css文件，不过通过合适的配置webpack也可以把css打包为单独的文件的。

上面的代码说明webpack是怎么把css当做模块看待的，咱们继续看一个更加真实的css模块实践。

### CSS module

在过去的一些年里，JavaScript通过一些新的语言特性，更好的工具以及更好的实践方法（比如说模块化）发展得非常迅速。模块使得开发者把复杂的代码转化为小的，干净的，依赖声明明确的单元，配合优化工具，依赖管理和加载管理可以自动完成。

不过前端的另外一部分，CSS发展就相对慢一些，大多的样式表却依旧巨大且充满了全局类名，维护和修改都非常困难。

被称为CSS modules的技术意在把JS的模块化思想带入CSS中来，通过CSS模块，所有的类名，动画名默认都只作用于当前模块。Webpack对CSS模块化提供了非常好的支持，只需要在CSS loader中进行简单配置即可，然后就可以直接把CSS的类名传递到组件的代码中，这样做有效避免了全局污染。具体的代码如下
```javascript
module.exports = {

    ...

    module: {
        rules: [
            {
                test: /(\.jsx|\.js)$/,
                use: {
                    loader: "babel-loader"
                },
                exclude: /node_modules/
            },
            {
                test: /\.css$/,
                use: [
                    {
                        loader: "style-loader"
                    }, {
                        loader: "css-loader",
                        options: {
                            modules: true, // 指定启用css modules
                            localIdentName: '[name]__[local]--[hash:base64:5]' // 指定css的类名格式
                        }
                    }
                ]
            }
        ]
    }
};
```
我们在app文件夹下创建一个Greeter.css文件来进行一下测试
```css
/* Greeter.css */
.by{
    background-color: yellow;
}
```
导入.root到Greeter.js中
```javascript
import React, {Component} from 'react';
import config from './config.json';
import styles from './Greeter.css';//导入

class Greeter extends Component{
  render() {
    return (
      <div className={styles.by}> //使用cssModule添加类名的方法
        {config.greetText}
      </div>
    );
  }
}

export default Greeter
```
重新运行npm start打包，运行npm run server，打开localhost:8080，文字背景变为黄色，div的class为“Greeter__by--U44rR”这样的形式，这样就可以保证不同组件之间不会存在相同的类名。

### CSS预处理器

(后续补充)

## 插件（Plugins）

插件（Plugins）是用来拓展Webpack功能的，它们会在整个构建过程中生效，执行相关的任务。

Loaders和Plugins常常被弄混，但是他们其实是完全不同的东西，可以这么来说，loaders是在打包构建过程中用来处理源文件的（JSX，Scss，Less..），一次处理一个，插件并不直接操作单个文件，它直接对整个构建过程其作用。

Webpack有很多内置插件，同时也有很多第三方插件(https://webpack.js.org/plugins/)，可以让我们完成更加丰富的功能。

### 使用插件的方法

要使用某个插件，我们需要通过npm安装它，然后要做的就是在webpack配置中的plugins关键字部分添加该插件的一个实例（plugins是一个数组）继续上面的例子，我们添加了一个给打包后代码添加版权声明的插件[BannerPlugin](https://webpack.js.org/plugins/banner-plugin/)。
```javascript
const webpack = require('webpack'); //引入webpack

module.exports = {
...
    module: {
        rules: [{
            test: /(\.jsx|\.js)$/,
            use: {
                loader: "babel-loader"
            },
            exclude: /node_modules/
        }, {
            test: /\.css$/,
            use: [
                {
                    loader: "style-loader"
                }, {
                    loader: "css-loader",
                    options: {
                        modules: true, // 指定启用css modules
                        localIdentName: '[name]__[local]--[hash:base64:5]' // 指定css的类名格式
                    }
                }
            ]
        }]
    },
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究')
    ],
};
```
通过这个插件，打包后的JS文件显示如下

```javascript
/*! 版权所有，翻版必究 */
/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache
/******/ 	var installedModules = {};
/******/
/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {
```

这就是webpack插件的基础用法了，下面给大家推荐几个常用的插件.

### HtmlWebpackPlugin

这个插件的作用是依据一个简单的index.html模板，生成一个自动引用你打包后的JS文件的新index.html。这在每次生成的js文件名称不同时非常有用（比如添加了hash值）。

#### 安装
```
npm install --save-dev html-webpack-plugin
```

这个插件自动完成了我们之前手动做的一些事情，在正式使用之前需要对一直以来的项目结构做一些更改：

1. 移除dist文件夹，利用此插件，index.html文件会自动生成，此外CSS已经通过前面的操作打包到JS中了。

2. 在app目录下，创建一个index.tmpl.html文件模板，这个模板包含title等必须元素，在编译过程中，插件会依据此模板生成最终的html页面，会自动添加所依赖的 css, js，favicon等文件，index.tmpl.html中的模板源代码如下：
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>

</head>

<body>
    <div id="div1" class="div1"></div>
</body>

</html>
```
3. 更新webpack的配置文件。
```javascript
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    devtool: 'eval-source-map',
    entry: __dirname + "/app/index.js", //已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist", //打包后的文件存放的地方
        filename: "index.js" //打包后输出文件的文件名
    },

    devServer: {
        contentBase: "./dist", //本地服务器所加载的页面所在的目录
        historyApiFallback: true, //不跳转
        inline: true //实时刷新
    },
    module: {
        rules: [{
            test: /(\.jsx|\.js)$/,
            use: {
                loader: "babel-loader"
            },
            exclude: /node_modules/
        }, {
            test: /\.css$/,
            use: [
                {
                    loader: "style-loader"
                }, {
                    loader: "css-loader",
                    options: {
                        modules: true, // 指定启用css modules
                        localIdentName: '[name]__[local]--[hash:base64:5]' // 指定css的类名格式
                    }
                }
            ]
        }]
    },
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究'),
        new HtmlWebpackPlugin({
            template: __dirname + "/app/index.tmpl.html"//new 一个这个插件的实例，并传入相关的参数
        })
    ],
}
```
再次执行npm start你会发现，dist文件夹会重新生成，并文件夹下面生成了index.js和index.html。

### Hot Module Replacement

（后续补充）

## 产品阶段的构建

目前为止，我们已经使用webpack构建了一个完整的开发环境。但是在产品阶段，可能还需要对打包的文件进行额外的处理，比如说优化，压缩，缓存以及分离CSS和JS。

对于复杂的项目来说，需要复杂的配置，这时候分解配置文件为多个小的文件可以使得事情井井有条，以上面的例子来说，我们创建一个webpack.production.config.js的文件，在里面加上基本的配置,它和原始的webpack.config.js很像，如下
```javascript
// webpack.production.config.js
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    devtool: 'null', //注意修改了这里，这能大大压缩我们的打包代码
    entry: __dirname + "/app/index.js", //已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist", //打包后的文件存放的地方
        filename: "index.js" //打包后输出文件的文件名
    },

    devServer: {
        contentBase: "./dist", //本地服务器所加载的页面所在的目录
        historyApiFallback: true, //不跳转
        inline: true //实时刷新
    },
    module: {
        rules: [{
            test: /(\.jsx|\.js)$/,
            use: {
                loader: "babel-loader"
            },
            exclude: /node_modules/
        }, {
            test: /\.css$/,
            use: [
                {
                    loader: "style-loader"
                }, {
                    loader: "css-loader",
                    options: {
                        modules: true, // 指定启用css modules
                        localIdentName: '[name]__[local]--[hash:base64:5]' // 指定css的类名格式
                    }
                }
            ]
        }]
    },
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究'),
        new HtmlWebpackPlugin({
            template: __dirname + "/app/index.tmpl.html"//new 一个这个插件的实例，并传入相关的参数
        })
    ],
}
```
```json
//package.json
{
  "name": "webpack-base-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --mode development",
    "server": "webpack-dev-server --open --mode development",
    "build": "NODE_ENV=production webpack --config ./webpack.production.config.js --progress"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.5",
    "babel-preset-env": "^1.7.0",
    "babel-preset-react": "^6.24.1",
    "css-loader": "^1.0.1",
    "html-webpack-plugin": "^3.2.0",
    "react": "^16.6.1",
    "react-dom": "^16.6.1",
    "style-loader": "^0.23.1",
    "webpack": "^4.25.1",
    "webpack-cli": "^3.1.2",
    "webpack-dev-server": "^3.1.10"
  },
  "dependencies": {}
}

```
> 注意：如果是window电脑，build需要配置为"build": "set NODE_ENV=production && webpack --config ./webpack.production.config.js --progress"。谢谢评论区简友提醒。

### 优化插件

webpack提供了一些在发布阶段非常有用的优化插件，它们大多来自于webpack社区，可以通过npm安装，通过以下插件可以完成产品发布阶段所需的功能

* OccurenceOrderPlugin：为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID

* UglifyjsWebpackPlugin：压缩JS代码；

* ExtractTextPlugin：分离CSS和JS文件（暂时不支持webpack 4，本文档不予讨论）

我们继续用例子来看看如何添加它们，OccurenceOrder是内置插件，你需要做的只是安装其它非内置插件

```
npm install --save-dev uglifyjs-webpack-plugin
```
在配置文件的plugins后引用它们
```javascript
// webpack.production.config.js
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
// extract-text-webpack-plugin暂时不支持webpack4
// const ExtractTextPlugin = require('extract-text-webpack-plugin');
const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
    devtool: 'null', //注意修改了这里，这能大大压缩我们的打包代码
    entry: __dirname + "/app/index.js", //已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist", //打包后的文件存放的地方
        filename: "index.js" //打包后输出文件的文件名
    },

    devServer: {
        contentBase: "./dist", //本地服务器所加载的页面所在的目录
        historyApiFallback: true, //不跳转
        inline: true //实时刷新
    },
    module: {
        rules: [{
            test: /(\.jsx|\.js)$/,
            use: {
                loader: "babel-loader"
            },
            exclude: /node_modules/
        }, {
            test: /\.css$/,
            use: [
                {
                    loader: "style-loader"
                }, {
                    loader: "css-loader",
                    options: {
                        modules: true, // 指定启用css modules
                        localIdentName: '[name]__[local]--[hash:base64:5]' // 指定css的类名格式
                    }
                }
            ]
        }]
    },
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究'),
        new HtmlWebpackPlugin({
            template: __dirname + "/app/index.tmpl.html"//new 一个这个插件的实例，并传入相关的参数
        }),
        new webpack.optimize.OccurrenceOrderPlugin(),
        new UglifyjsWebpackPlugin(),
        // extract-text-webpack-plugin暂时不支持webpack4
        // new ExtractTextPlugin("style.css"),
    ],
}
```
此时执行npm run build可以看见代码是被压缩后的。

### 缓存

缓存无处不在，使用缓存的最好方法是保证你的文件名和文件内容是匹配的（内容改变，名称相应改变）

webpack可以把一个哈希值添加到打包的文件名中，使用方法如下,添加特殊的字符串混合体（[name], [id] and [hash]）到输出文件名前

```javascript
// webpack.production.config.js
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
// extract-text-webpack-plugin暂时不支持webpack4
// const ExtractTextPlugin = require('extract-text-webpack-plugin');
const UglifyjsWebpackPlugin = require('uglifyjs-webpack-plugin');

module.exports = {
...
    entry: __dirname + "/app/index.js", //已多次提及的唯一入口文件
    output: {
        path: __dirname + "/dist", //打包后的文件存放的地方
        filename: "index-[hash].js" //打包后输出文件的文件名
    },
    ...
}
```
现在用户会有合理的缓存了。

![带hash值的js名](webpack_1/webpack_cache.PNG)

### 去除dist文件中的残余文件

添加了hash之后，会导致改变文件内容后重新打包时，文件名不同而内容越来越多，因此这里介绍另外一个很好用的插件clean-webpack-plugin。

**安装**：

```
npm install clean-webpack-plugin --save-dev
```

**使用**：

引入clean-webpack-plugin插件后在配置文件的plugins中做相应配置即可：

```javascript
const CleanWebpackPlugin = require("clean-webpack-plugin");
  plugins: [
    ...// 这里是之前配置的其它各种插件
    new CleanWebpackPlugin('dist/*.*', {
      root: __dirname,
      verbose: true,
      dry: false
  })
  ]
```

关于clean-webpack-plugin的详细使用可参考[这里](https://github.com/johnagan/clean-webpack-plugin)。
