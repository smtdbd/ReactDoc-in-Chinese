# 状态和生命周期
考虑一下[之前](./Rendering Elements.md)的时钟例子，到目前为止，我们只学到一个方法来更新UI，我们调用```ReactDOM.render()```来改变渲染的输出：
```javascript
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```
[尝试一下](http://codepen.io/gaearon/pen/gwoJZk?editors=0010)

在本节里，我们将会学习如何使得```Clock```组件变得真正的可重用和封装。它将会为自己设置一个定时器且每秒钟更新自己一次。

我们先从如何封装时钟开始：
```javascript
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```
[尝试一下](http://codepen.io/gaearon/pen/dpdoYR?editors=0010)
然而，它遗漏了一个重要的需求：事实上```Clock```设置了一个计时器并每秒钟更新一次UI应该是在```Clock```内实现的细节。

理想中我们还是想要写下面的代码然后让```Clock```自己更新自己.
```javascript
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
为了实现这个需求，我们要为```Clock```组件添加一个状态。

状态类似于props，但是更私有化且完全由组件所掌控。

我们之前提到过，用类来定义的组件可以有更多的特性，局部状态恰好就是其中之一，一个只有类能使用的特性。
# 将函数转化为类
你可以将一个类似于```Clock```的函数组件转化为类，只用五个步骤：
1. 创建一个相同名字的[ES6类](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)并继承```React.Component```
2. 加入一个名为```render()```的空方法
3. 将函数体移到```render()```方法内
4. 将函数体内的```props```替换为```this.props```
5. 删除剩余的空函数定义
```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
[尝试一下](http://codepen.io/gaearon/pen/zKRGpo?editors=0010)
现在```Clock```就被定义为了一个类而不是一个函数了。现在就来试试局部状态和生命周期钩子等新增特性。
# 向类里面加入局部状态
将```date```从props变为状态，只需以下三步：
1. 将```render()```方法中的```this.props.date```替换为```this.state.date```
```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
2. 添加[构造函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)初始化```this.state```：
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
我们是如何通过```props```来通过基类构造函数的：
```javascript
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
  ```
类组件总应该通过```props```调用基类构造函数
3. 从```<Clock />```元素中删除```date``` prop:
```javascript
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
我们稍后向组件内加入计时器代码。结果就是这样：
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
[尝试一下](http://codepen.io/gaearon/pen/KgQpJd?editors=0010)

接下来，我们将为```Clock```创建它自己的计时器，并且每秒钟都更新它自己
# 向类中添加生命周期方法
在一个有大量组件的应用中，非常重要的一点就是当组件销毁时，务必释放它占用的资源

我们试图在```Clock```第一次被渲染到DOM上时给它设定好计时器。我们称之为"装载"

我们还想在```Clock```产出的DOM被移除时，重置计时器。我们称之为"卸载"

我们还能再组件中声明特殊的方法，其中有一些代码，会在组件装载和卸载时运行：
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```
这些方法被叫做"生命周期钩子"

```componentDidMount()```钩子会在组件输出并渲染至DOM之后运行，这是个绝佳的时机来设置一个定时器。
```javascript
componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```
* Note:
* 我们是如何把定时器ID保存到```this```中的

虽然```this.props```是被React创建的，而```this.state```有着特殊的含义，但你可以往类中自由地添加一些你需要储存且与实际输出无关的东西。

如果你不在```render()```中使用的话，他将不会成为状态。我们会在```componentWillUnmount()```生命周期钩子中卸载计时器：
```javascript
componentWillUnmount() {
    clearInterval(this.timerID);
  }
  ```
最终，我们需要实现```tick()```方法，它每秒钟运行一次。它将会使用```this.setState()```来安排组件局部状态的更新：
```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```
[尝试一下](http://codepen.io/gaearon/pen/amqdNA?editors=0010)

现在这个时钟就会每秒钟跳一次了。

让我们快速地复习一下知识点：
1. 当```<Clock />```传到```ReactDOM.render()```时，React调用```Clock```组件的构造函数。当```Clock```需要立即显示是，它将会使用现在的时间来初始化```this.state```并稍后更新它的状态。
2. 接下来React将会调用```Clock```组件的```render()```方法。这就是为何React知道什么需要显示再屏幕上，React接下来会更新DOM以保持```Clock```渲染输出的匹配。
3. 当```Clock```的输出插入到DOM中后，React调用```componentDidMount()```生命周期钩子函数。在其中，```Clock```组件请求浏览器设置一个定时器用于每秒钟调用```tick()```一次
4. 每秒钟浏览器都会调用```tick()```方法，其中，```Clock```组件计划通过使用包含当前时间的对象来调用```setState()```，进而更新UI。多亏调用了```setState()```，使得React得知状态已经改变了，进而再次调用```render()```方法获取应该显示的内容。此时，```render()```方法中的```this.state.date```就会改变。所以渲染输出的是新的时间，于是React刷新了DOM。
5. 如果```Clock```组件曾被从DOM中移除，React就会调用```componentWillUnmount()```生命周期钩子函数使得计时器停止。
# 正确地使用状态
下面是关于```setState()```你不得不知道的事
## 不要直接更改状态
比如说，这酱不会使得组件重绘：
```javascript
// Wrong
this.state.comment = 'Hello';
```
取而代之的是使用```setState()```函数：
```javascript
// Correct
this.setState({comment: 'Hello'});
```
唯一一个你能直接指定```this.state```的位置是构造函数。
## 状态更新也许是异步的
React为了性能也许在单次更新中需要分批处理多个```setState()```调用
因为```this.props```和```this.state```也许是异步更新的，你得保证不依赖他们的值用于计算下次的状态

比如说，下面的代码也许会在更新counter时失败：
```javascript
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
为了修复这个错误，使用```setState()```的另一个形式，它可以接受一个函数而非一个对象。这个函数可以接受上次的状态作为第一个参数，接受更新的props作为第二个参数：
```javascript
// Correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment
}));
```
前面用了箭头函数，但是用普通的函数也能行：
```javascript
// Correct
this.setState(function(prevState, props) {
  return {
    counter: prevState.counter + props.increment
  };
});
```
## 状态更新是合并的
当你调用了```setState()```时，React将会把你提供的当前状态合并进对象中。

比如说，你的状态也许包含了几个相互独立的变量：
```javascript
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```
这是你可以利用分开的```setState()```调用来分别更新它们：
```javascript
componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```
这个合并是浅的，所以```this.setState({comments})```保持了```this.state.posts```的完整性，但是完全的替换掉了```this.state.comments```。
## 数据向下传递
无论是父组件还是子组件，都无从得知一个组件是有状态的还是无状态的，它们也不关心它是由函数定义的还是由类定义的。

这就是为什么状态经常被称为局部的或者是封装的。它不会容许任何其他部件拥有或设置自己的状态。

一个组件可以选择通过把他的状态作为props传给他的子组件：
```javascript
<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```
这在用户定义的组件中也行得通：
```javascript
<FormattedDate date={this.state.date} />
```
```FormattedDate```组件接受一个```date```作为props，而且它也无从得知这是```Clock```的状态，抑或是```Clock```的props，还是手写出来的。
```javascript
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```
[尝试一下](http://codepen.io/gaearon/pen/zKRqNB?editors=0010)

这一般称为自顶向下的，或者是单向的数据流。任何状态总是被一些特殊的组件所拥有，而状态中所有的数据或是UI设备也只能影响比它们更低的组件。

如果你想象出一个类似于水流的组件树，每个组件状态就像是新增至任意位置的水源，但只能向下流。

为了展示所有的组件都确实是孤立的，我们可以创建一个```App```组件渲染三个```<Clock>```:
```javascript
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```
[尝试一下](http://codepen.io/gaearon/pen/vXdGmd?editors=0010)

每个```Clock```都设置它们自己的计时器并独立的更新

在React应用中，无论组件是有状态还是无状态的，它都会被好好的实现细节以便组件随时间发生改变。你可以把无状态组件放置于有状态组件当中，反之亦然。
