---
title: Webpack
date: 2016-09-04 19:17:08
categories: automate
tags:
  - Webpack
  - React
  - CommonJS
---
---

[Webpack](https://webpack.github.io/)  一个前端工具，可以对 js/jsx/coffee/less/sass/图片等资源模块进行加载，预处理，再进行打包。
优点如下：

+ 以 commonJS 的形式来书写，支持 AMD/CMD ,方便项目迁移。
+ 各资源被模块化。
+ 替代部分 grunt/gulp 的工作，比如打包、压缩混淆、图片转 base64 等。
+ 支持很多模块加载器的调用，可以使模块加载器灵活定制，比如 babel-loader 加载器，该加载器能使我们使用 ES6 的语法来编写代码。
+ 可以通过配置打包成多个文件，有效的利用浏览器的缓存功能提升性能。
+ 使用模块加载器，可以支持 sass，less 等处理器进行打包且支持静态资源样式及图片进行打包。
+ 在内存中完成打包，性能更快。

Webpack 将项目中用到的一切静态资源都视之为模块，模块之间可以互相依赖。Webpack 对它们进行统一的管理以及打包发布。

<!--more-->


### JS 规范
对 AMD/CMD 模式来说，模块是异步加载的，所以我们常规需要使用 define 函数来定义回调。
AMD，用过 RequireJS 的并不陌生，AMD 是在 CommonJS 的基础上考虑了浏览器的异步加载特性而产生的，可以让模块异步加载并保证执行顺序。

``` javascript
define(['a'], function(a){
    return {
        foo: a.foo
    };
});
```
CMD，用过 SeaJS 的小伙伴是不是很熟悉。

``` javascript
define(function (require, exports, module){
    var a = require('a');
    exports.foo = a.foo;
    exports.b = function (){
 
    };
});
```
webpack 遵循 commonJS 语法，无须任何 define ，这种方式语法更加简洁直观。

``` javascript
var a = require('a');
exports.foo = a.foo;          
exports.b = function (){
 
};
```

CommonJS 的 require 函数虽是同步加载，但浏览器并不同步加载该模块，对 require 的处理是由 Webpack 进行解析和打包的（类似 browserify），浏览器只需要执行打包后的代码。但是对于 React 中的 JSX 语法，这就需要使用 Webpack 的扩展加载器来处理了，如 jsx-loader。


### 安装
1.全局安装

执行命令：`npm install webpack -g`

2.项目依赖安装

执行命令：`npm install webpack --save-dev`
node_modules 文件夹中会包含 webpack，此时可以使用 webpack 命令了

3.生成 package.json 文件

根目录下执行命令：`npm init`

4.安装插件

若 package.json 文件已配置好，根目录下执行命令：`npm install`


### 配置文件
配置文件 webpack.config.js，好比 Gulpfile.js 或 Gruntfile.js，用来配置项目，告 诉webpack 需要做哪些操作。

一个简单的 webpack.config.js 代码如下：

``` js
module.exports = { 
    entry: {
        main : './assets/js/src/main.js',
    }, 
    output: {
        path: path.join(__dirname, './assets/build/'),
        publicPath: '/assets/build/',
        filename: 'js/[name].bundle.js'
    },
    module: {
      loaders: [
        { test: /\.css$/, loader: 'style-loader!css-loader' },
        //{ test: /\.css$/,loader: ExtractTextPlugin.extract("style-loader", "css-loader")},
        { test: /\.js[x]?$/, loaders: ['react-hot', 'jsx?harmony'], exclude: /node_modules/ },
        { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
        { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192&name=images/[name].[hash].[ext]'},
        { test: /\.coffee$/, loader: 'coffee'},
        { test: /\.html$/,   loader: 'html'},
        { test: /\.json$/,   loader: 'json'},
        { test: /\.(woff|woff2)$/,   loader: "url?limit=10000&minetype=application/font-woff&name=fonts/[name].[ext]"},
        { test: /\.ttf$/,    loader: "file?name=fonts/[name].[ext]"},
        { test: /\.eot$/,    loader: "file?name=fonts/[name].[ext]"},
        { test: /\.svg$/,    loader: "file?name=fonts/[name].[ext]"}
      ]
    },
    resolve: {
        root: path.resolve('./assets'),
        extensions: ['', '.js', '.jsx', '.json', '.scss'],
        alias: {
            jquery : 'js/lib/jquery-2.2.0.min.js',
            semantic : '3rdparty/dist/semantic.min.js',
            signals : '3rdparty/signals.min.js',
            lodash : 'js/lib/lodash.min.js',
            component : 'js/jsx/component.js',
            lib : 'js/lib/lib.js',
            module:'js/src/module.js',
        }
    },
    plugins: []
};
```

`entry`: 入口文件，比如我这边的入口文件时 main.js
`output`: 目标文件，我这边是在根目录下生成 assets/build/js 文件夹，该文件夹内有 [name].bundle.js 文件; output.path: 打包文件存放的绝对路径;
output.publicPath: 网站运行时的访问路径;
output.filename: 打包后的文件名;
`resolve`: 解析模块路径;
root：绝对路径;
extensions：可以用来指定模块的后缀，这样在引入模块时就不需要写后缀，会自动补全;
alias：别名，直接require别名;
`plugins`: 定义了需要使用的插件，比如 commonsPlugin 在打包多个入口文件时会提取公用的部分。
`module.loaders`：文件加载器，对相应文件需要使用什么加载器来处理，比如 jsx-loader 加载器处理 jsx 文件，还有 style-loader 和 css-loader 加载器，处理样式文件。

加载器安装

``` bash
npm install jsx-loader style-loader css-loader sass-loader url-loader file-loader --save-dev
```

更多详情参见：[configuration](http://webpack.github.io/docs/configuration.html)




### Webpack 命令

``` js
webpack     //启动webpack命令
webpack -p  //对打包后的文件进行压缩
webpack -d  //提供SourceMaps，方便调试
webpack -w  //提供watch监听，实时打包更新
webpack --config    //指定配置文件
webpack --colors    //输出结果带彩色，比如：会用红色显示耗时较长的步骤
webpack --profile   //输出性能数据，可以看到每一步的耗时
//默认情况下 node_modules 下的模块会被隐藏，加上这个参数可以显示这些被隐藏的模块
webpack --display-modules
webpack --display-error-details     //可以查看详细信息。
```

如命令：webpack --display-modules --profile --colors

main.html 代码如下：

``` html
<!DOCTYPE html>
<html lang="en">
</head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <meta http-equiv="Pragma" content="no-cache">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>main</title>
    <link rel="stylesheet" href="/assets/build/css/main.css">
</head>
<body>
    <div id="rp_main"></div>
    <script src="assets/build/js/vendor.js"></script>
    <script src="assets/build/js/main.bundle.js"></script>
</body>
</html>
```

main.js 代码如下：

``` js
require("../../3rdparty/dist/semantic.min.css");
require("../../style/lib.css");
var React = require('react');
var ReactDOM = require('react-dom');
var m=require('module');
ReactDOM.render(<m.app name="test"></m.app>, document.getElementById('rp_main'));
```

到此，webpack 都配置好了，执行行命令：webpack，ok



### 模块加载器
Webpack 将所有静态资源都认为是模块，比如 js，jsx，css，scss，图片等，从而可以对其进行统一管理，通过对应的加载器处理成模块。
Webpack 的加载器之间可以进行串联，一个加载器的输出可以成为另一个加载器的输入。比如 less 文件先通过 `less-loader` 处理成 css，然后再通过 `css-loader` 加载成css模块，最后由 `style-loader` 加载器对其做最后的处理，通过 `style` 标签将其应用到最终的浏览器环境。
为了让Webpack识别什么样的资源应该用什么加载器去载入，需要在配置文件进行配置，通过正则表达式对文件名进行匹配。例如：

``` js
module: {
  loaders: [
    { test: /\.css$/, loader: 'style-loader!css-loader' },
    { test: /\.js[x]?$/, loaders: ['react-hot', 'jsx?harmony'], exclude: /node_modules/ },
    { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
    { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192&name=images/[name].[hash].[ext]'},
    { test: /\.coffee$/, loader: 'coffee'},
    { test: /\.html$/,   loader: 'html'},
    { test: /\.json$/,   loader: 'json'},
    { test: /\.(woff|woff2)$/,   loader: "url?limit=8192&minetype=application/font-woff&name=fonts/[name].[ext]"},
    { test: /\.ttf$/,    loader: "file?name=fonts/[name].[ext]"},
    { test: /\.eot$/,    loader: "file?name=fonts/[name].[ext]"},
    { test: /\.svg$/,    loader: "file?name=fonts/[name].[ext]"}
  ]
}
```

加载器之间的级联是通过 ! 来连接，例如：style-loader!css-loader!less-loader。"-loader" 可以省略，如：style!css!less。
jsx 文件由 jsx-loader 处理，加载器支持参数，?harmony 参数使其支持ES6语法。当然也可以用 babel-loader
图片资源，由 url-loader 实现，代码中 url-loader?limit=8192 小于 8k 的图片转为 Base64。
更多详情参见：[loaders](http://webpack.github.io/docs/loaders.html)，[list-of-loaders](http://webpack.github.io/docs/list-of-loaders.html)




### 使用别名
别名 `resolve.alias`，把一个请求重定向到另一个路径，如下：

``` js
resolve: {
    root: path.resolve('./assets'),
    extensions: ['', '.js', '.jsx', '.json', '.scss'],
    alias: {
        jquery : 'js/lib/jquery-2.2.0.min.js',
        semantic : '3rdparty/dist/semantic.min.js',
        signals : '3rdparty/signals.min.js',
        lodash : 'js/lib/lodash.min.js',
        component : 'js/jsx/component.js',
        lib : 'js/lib/lib.js',
        module:'js/src/module.js',
    }
}
```

require('jquery')，其实就等价于 require('./assets/js/lib/jquery-2.2.0.min.js')。



### 公共模块提取
使用 `CommonsChunkPlugin` 插件来提取多个页面之间的公共模块，并将该模块打包为 common.js 。配置如下：

``` js
entry: {
    main : './assets/js/src/main.js',
    form : './assets/js/jsx/form.js',
    vendor: ['jquery','react','react-dom','signals','semantic','component','lib']
},
plugins: [
    new CommonsChunkPlugin({
        name: 'vendor',
        filename: 'js/vendor.js',
        chunks: ["main","form"],
        minChunks: 2
    })
]
```

`entry`: 入口文件增加 entry.main, entry.form, entry.vendor
`plugins`: 插件项增加 new CommonsChunkPlugin
最终将生成公共模块文件 js/vendor.js
更多详情参见：[code-splitting](http://webpack.github.io/docs/code-splitting.html)



### 独立打包样式
样式会被默认打包到 js 文件中，怎么独立出来作为单独样式文件，然后在页面中用 `link` 标签引入。
用插件 `extract-text-webpack-plugin`

``` js
entry: {
    main : './assets/js/src/main.js',
    form : './assets/js/jsx/form.js',
    vendor: ['jquery','react','react-dom','signals','semantic','component','lib']
},
module: {
  loaders: [
    { test: /\.css$/,loader: ExtractTextPlugin.extract("style-loader", "css-loader")}
  ]
},
plugins: [
    new ExtractTextPlugin("css/[name].css")
]
```

`module.loaders` 增加 { test: /\.css$/,loader: ExtractTextPlugin.extract("style-loader", "css-loader")}
`plugins`: 插件项增加 new ExtractTextPlugin("css/[name].css")
最终将生成公共模块文件 css/main.css 和 css/form.css
更多详情参见：[stylesheets](http://webpack.github.io/docs/stylesheets.html)



### 打包成多个资源文件
为减少加载时间，可能要将项目打包成多个资源文件，怎么办呢？创建多个入口文件：如下：

``` js
entry: {
    main : './assets/js/src/main.js',
    form : './assets/js/jsx/form.js',
    vendor: ['jquery','react','react-dom','signals','semantic','component','lib']
}, 
output: {
    path: path.join(__dirname, './assets/build/'),
    publicPath: '/assets/build/',
    filename: 'js/[name].bundle.js'
},
plugins: [
    new CommonsChunkPlugin({
        name: 'vendor',
        filename: 'js/vendor.js',
        chunks: ["main","form"],
        minChunks: 2
    })
]
```

打包后，会生成三个文件，分别为：main.bundle.js, form.bundle.js, vendor.js
更多详情参见：[code-splitting](http://webpack.github.io/docs/code-splitting.html)



### 打包图片
图片资源由 `url-loader` 来加载的，既可以是 css 文件里 url 属性，也可以是 require 请求，如下：

``` css
.bgimg{
    background: url(../images/18050.png);
}
```

``` js
var img = document.createElement("img"); 
img.src = require("../../images/a.jpg"); 
document.body.appendChild(img);
```

webpack.config.js 配置如下：

``` js
output: {
    path: path.join(__dirname, './assets/build/'),
    publicPath: '/assets/build/',
    filename: 'js/[name].bundle.js'
},
module: {
  loaders: [
    { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192&name=images/[name].[hash].[ext]'},
  ]
}
```

小于 8k 的图片会转化为 base64，否则，在 ./assets/build/images 目录下生成相应的图片。此时要注意 output.publicPath 的配置，若为空，则为根目录，有可能导致打包后的图片路径找不到。



### 按需加载
使用 `require.ensure`，当需要时再加载，如下：

``` js
require.ensure(["module-a", "module-b"], function(require) {
    var a = require("module-a");
    // ...
});
```

更多详情参见：[code-splitting](http://webpack.github.io/docs/code-splitting.html)



### 使用 CDN
有些文件我们并不希望被打包，而是以CDN方式并以 `script` 的形式引用，但在 webpack 中又可以正常使用。怎么办呢？请用 externals 声明外部依赖。

``` js
externals: {
    react : "React",
    jquery: "jQuery",
    lodash: "_",
    module:true
}
```

更多详情参见：[library-and-externals](http://webpack.github.io/docs/library-and-externals.html)


### Webpack & Gulp
webpack 可以与 gulp 配合，一起使用，配置如下：

``` js
var webpack = require("webpack");
var webpackConfig = require('./webpack.config.js');
gulp.task("webpack", function(callback) {
    webpack(webpackConfig, function(err, stats) {
      if(err) throw new gutil.PluginError("webpack:build", err);
      gutil.log("[webpack:build]", stats.toString({
        colors: true
      }));
      callback();
    });
});
```

执行命令：`gulp webpack`，ok
更多详情参见：[usage-with-gulp](http://webpack.github.io/docs/usage-with-gulp.html)



### Webpack & React
一个简单的 React 组件，module.js 代码如下：

``` js
class Hello extends React.Component {
  render() {
    return (
      <h3>Hello {this.props.name}!</h3>
    );
  }
}
 
var App = React.createClass({
    getInitialState: function() {
      return {name: 'test'};
    },
    render: function() { 
        return (
          <h3>{this.props.name}</h3>
      );
    }
});
module.exports = {
  Hello:Hello,
  App:App
}
```

在 main.js 中引入 module.js

``` js
require("../../3rdparty/dist/semantic.min.css");
require("../../style/lib.css");
var React = require('react');
var ReactDOM = require('react-dom');
var m=require('module');
ReactDOM.render(<m.app name="test"></m.app>, document.getElementById('rp_main'));

```

这时，main.js 和 module.js 都是JSX语法，需要对它们进行预处理，这就要引入 JSX 加载器。配置文件中加入如下配置：

``` js
module: {
  loaders: [
    { test: /\.js[x]?$/, loaders: ['jsx?harmony'], exclude: /node_modules/ },
  ]
}  
```


### webpack-dev-server
Webpack 提供了一个轻量的 node.js express 服务器，开发过程中，监听文件变化，实时打包，通过 socket.io 通知客户端，实现页面的自动刷新。
首先，执行安装命令：`npm install webpack-dev-server --save-dev`
启动开发服务器，运行命令：`webpack-dev-server`，请看控制台日志，然后访问 `localhost:8080/webpack-dev-server`。改变相应 js 中的内容，保存，看页面，有效果了。再看看控制台输出日志，自动编译哦，然后把编译结果通知客户端更新。
更多详情参见：[webpack-dev-server](http://webpack.github.io/docs/webpack-dev-server.html)



### react-hot-loader
Webpack 有运行时模块替换功能，称之为 Hot Module Replacement (HMR)。当某个模块代码发生变化时，Webpack 实时打包将其推送到页面并进行替换，从而无需刷新页面就实现代码替换。
在 React 开发中，针对 React 出现了一个第三方 `react-hot-loader` 加载器，可以轻松实现 React 组件的热替换。因为 React 的每一次更新都是全局刷新虚拟 DOM，让 React 组件的热替换可以成为通用的加载器，从而极大提高开发效率。
首先，执行安装命令：`npm install react-hot-loader —save-dev`
webpack-dev-server 需要开启 HMR 参数 hot，下面创建一个 server.js 文件启动 Webpack 开发服务器，代码如下：

server.js

``` js
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var config = require('../webpack.config2');
 
new WebpackDevServer(webpack(config), {
  publicPath: config.output.publicPath,
  hot: true,
  historyApiFallback: true
}).listen(3000, 'localhost', function (err, result) {
  if (err) {
    console.log(err);
  }
 
  console.log('Listening at localhost:3000');
});
```

除了 server.js 外，webpack.config.js 还需要如下修改：

``` js
entry: {
    main : './assets/js/src/main.js',
    form : './assets/js/jsx/form.js',
    index: [
        'webpack-dev-server/client?http://localhost:3000', // WebpackDevServer host and port
        'webpack/hot/only-dev-server',
        './assets/js/src/index.js' // Your appʼs entry point
    ],
    vendor: ['jquery','react','react-dom','signals','semantic','component','lib']
},
module: {
  loaders: [
    { test: /\.js[x]?$/, loaders: ['react-hot', 'jsx?harmony'], exclude: /node_modules/ }
  ]
},
plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoErrorsPlugin()
]
```

`entry.index`：配置 webpack 开发服务器，接收 Webpack 的推送，通知相关 React 组件进行重新 Render。
`module.loaders`：Webpack 用 react-hot-loader 加载 React 组件。
`plugins`：加上热替换的插件和防止报错的插件。
执行命令：`node server.js` ，ok，资源的任何修改，无需刷新页面，即可重新宣染。
更多详情参见：[react-hot-loader](http://gaearon.github.io/react-hot-loader/getstarted/)



---