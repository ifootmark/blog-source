---
title: React
date: 2016-09-04 19:16:56
categories: component
tags:
  - React
  - CommonJS
---
---


[React](http://facebook.github.io/react/) 是一个 Facebook 和 Instagram 用来创建用户界面的 JavaScript 库。 我们以 react 为库来创建组件，组件有自己的结构、逻辑、样式，会依赖一些资源，或依赖其他组件。
+ 通过前端模板引擎定义结构
+ JS 文件中写自己的逻辑
+ CSS 中写组件的样式
+ 通过 RequireJS、SeaJS 等库来解决模块之间的相互依赖

<!--more-->


### JSX

用 React 写组件的时候，通常会用到 JSX 语法，HTML 语言直接写在 JavaScript 语言之中，被 JSX 转换工具转换成纯 Javascript 代码，当然你想直接使用纯 Javascript 代码写也是可以的，只是利用 JSX，组件的结构和组件之间的关系看上去更加清晰。

``` html
<!DOCTYPE html>
<html>
  <head>
    <script src="build/react.js"></script>
    <script src="build/JSXTransformer.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/jsx">
        React.render(
            <h1>Hello, world!</h1>,
            document.getElementById('example')
        );
    </script>
  </body>
</html>
```

通过 JavaScript 直接生成模板，这样你就可以用一个真正语言的所有表达能力去构建用户界面。为了使这变得更简单，我们做了一个非常简单、可选类似 HTML 语法 ，通过函数调用即可生成模板的编译器，称为 JSX。

文件分离

你的 React JSX 代码文件可以写在另外的文件里。新建下面的 helloworld.js。
``` js
React.render(
    <h1>Hello, world!</h1>,
    document.getElementById('example')
);
```

然后在 helloworld.html 引用该文件：
`<script type="text/jsx" src="./helloworld.js"></script>`


### JSX 转化器
JSX 转换，下面 JSX 代码：
``` js
var Nav = React.createClass({/*...*/});
var app = <nav color="blue"><profile>click</profile></nav>;
```
转化后：
``` js
var Nav = React.createClass({/*...*/});
var app = React.createElement(
    Nav,
    {color:"blue"},
    React.createElement(Profile, null, "click")
);
```
React.createElement 这个方法有三个参数，第一个参数可以是一个字符串，表示是一个HTML标准内的元素，或者是一个 ReactClass 类型的对象，表示我们之前封装好的自定义组件。第二个参数是一个对象，它保存了这个元素的所有固有属性；从第三个参数，内容或子元素。

把JSX语法的代码转化为纯 Javascript 代码，有多种方式。

1、对于内联与 HTML 中的代码或者是未经过转化的外部文件，在 script 标签中要加上 type="text/jsx"，并引入 JSXTransformer.js 文件即可，不过这种方式并不建议在生产环境使用，建议的方法是在代码上线前就将代码转换好。

2、也可以使用 npm 全局安装 `react-tools`：
`npm install -g react-tools`

执行命令：
`jsx src/ build/`

若想监听文件变化并自动编译，可执行命令：
`jsx --watch src/ build/`

3、如果使用自动化工具，比如 gulp 的话，可以使用相应插件 gulp-react。

更多详情参见：[jsx-in-depth](http://docs.reactjs-china.com/react/docs/jsx-in-depth.html)


### HTML 标签 & React 模块
React 可以渲染 HTML 标签 (strings) 或 React 模块 (classes)。

要渲染 HTML 标签，只需在 JSX 里使用小写字母开头的标签名。要创建一个 HTML 标准中存在的元素，直接像写 HTML 代码一样即可：

``` js
var myDivElement = <div className="foo" />;
React.render(myDivElement, document.body);
```

要渲染 React 模块，只需创建一个大写字母开头的本地变量。

``` js
var MyComponent = React.createClass({/*...*/});
var myElement = <MyComponent someProperty={true} />;
React.render(myElement, document.body);
```

React 的 JSX 里约定分别使用首字母大、小写来区分本地模块的类和 HTML 标签。

不过需要注意的是 class 和 for 这两个属性，由于 JSX 就是 JavaScript，一些标识符像 class 和 for 不建议作为 XML 属性名。作为替代，React DOM 使用 className 和 htmlFor 来做对应的属性。

还有一点是，在创建 HTML 标准内的元素时，JSX 转化器会丢弃那些非标准的属性，如果一定要添加自定义属性，那么需要在这些自定义属性之前添加 data- 前缀。

``` html
<div data-custom-attribute="foo" />
```


### Javascript 表达式
在 JSX 语法中，要使用 JavaScript 表达式作为属性值，只需把这个表达式用一对大括号 ({}) 包起来，不要用引号 ("")。 比如下面这个使用三目运算符的例子：

``` js
// Input (JSX):
var content = <Container>{window.isLoggedIn ? <Nav /> : <Login />}</Container>;
// Output (JS):
var content = React.createElement(
    Container,
    null,
    window.isLoggedIn ? React.createElement(Nav) : React.createElement(Login)
);
```

更多详情参见：[jsx-in-depth](http://docs.reactjs-china.com/react/docs/jsx-in-depth.html)



### 注释
JSX 里添加注释很容易；它们只是 JS 表达式而已。你只需要在一个标签的子节点内(非最外层)小心地用 {} 包围要注释的部分。

``` js 
var content = (
  <Nav>
    {/* 一般注释, 用 {} 包围 */}
    <Person
      /* 多
         行
         注释 */
      name={window.isLoggedIn ? window.name : ''} // 行尾注释
    />
  </Nav>
);
```


### 传播属性
在 JSX 中，可以使用 ... 运算符，表示将一个对象的键值对与 ReactElement 的 props 属性合并，这个 ... 运算符的实现类似于 ES6 Array 中的 ... 运算符的特性。

``` js
var props = { foo: x, bar: y };
var component = <Component { ...props } />;
```

相当于：

``` js
var component = <Component foo={x} bar={y} />
```

它也可以和普通的XML属性混合使用，需要同名属性，后者将覆盖前者：

``` js
var props = { foo: 'default' };
var component = <Component {...props} foo={'override'} />;
console.log(component.props.foo); // 'override'
```

更多详情参见：[jsx-spread](http://docs.reactjs-china.com/react/docs/jsx-spread.html)



### 获取真实的 DOM 节点
组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM 。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM 上，这种算法叫做 DOM diff ，它可以极大提高网页的性能表现。
但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 ref 属性

``` js
var MyComponent = React.createClass({
    componentDidMount: function() {
        var test = ReactDOM.findDOMNode(this.refs.test);
        $(test).focus();
    },
    render: function() {
        return (
          <div>
            <input type="text" ref="test">
          </div>
        );
      }
});
 
ReactDOM.render(
  <MyComponent />,
  document.getElementById('example')
);
```

需要注意的是，由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。
更多详情参见：[more-about-refs](http://facebook.github.io/react/docs/more-about-refs.html)

### 组件的生命周期
React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理函数。

``` js
componentWillMount()
componentDidMount()
componentWillUpdate(object nextProps, object nextState)
componentDidUpdate(object prevProps, object prevState)
componentWillUnmount()
```

更多详情参见：[lifecycle-methods](http://facebook.github.io/react/docs/component-specs.html#lifecycle-methods)


### 组件
React 组件非常简单。你可以认为它们就是简单的函数，接受 props 和 state 作为参数，然后渲染出 HTML。正是因为它们是这么的简单，这使得它们非常容易理解。

注意: 有一个限制，React 组件只能渲染单个根节点。如果你想要返回多个节点，它们必须被包含在同一个节点里。

还有很多内容，待续...

---