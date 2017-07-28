# 组件及其支持
组件让你可以将UI分割成各自独立的、可重复使用的多个部分。

理论上，组件将看起来像是JS的函数。它接受任意个输入(也叫做"props")并返回React元素便于描述什么应该显示在屏幕上
# 函数和类组件
最简单的定义一个组件的方法是写一个JS函数：
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
这个函数是一个可用的React组件，因为它接受一个prop对象参数，并返回了一个React元素。我们这种组件为"函数组件(functional)"因为它就是一个JS函数。

你还能用一个[ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)来定义一个组件：
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上面的两个组件，在React看来，是完全一致的。

类还有更多的特性，我们会在[下一节](./State and Lifecycle.md)中讨论。到目前为止，我们将会使用函数组件，因为它比较简洁。
# 渲染一个组件
此前，我们只遇到过React元素用来表示一个DOM标签：
```javascript
const element = <div />;
```
然而，元素还能用用户定义的组件来表示：
```javascript
const element = <Welcome name="Sara" />;
```
当React发现了一个用户定义的组件所表示的元素时，它通过JSX属性来将组件表示为一个对象，我们称其为对象props

比如，下面的代码渲染了"Hello, Sara"：
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```
[尝试一下](http://codepen.io/gaearon/pen/YGYmEG?editors=0010)

让我们总结一下这段示例代码中发生了什么：
1. 我们用"<Welcome name="Sara" />元素调用了"ReactDOM.render()"
2. React 用"{name: 'Sara'}"作为props调用了Welcome
3. 我们的welcome组件返回了一个"<h1>Hello, Sara</h1>"元素作为返回值
4. React DOM 高效地更新了DOM以匹配<h1>Hello, Sara</h1>
* Caveat:
* 组件名总以大写字母开头，比如说"<div />"表示一个DOM标签，但是<Welcome />表示一个组件并请求一个"Welcome"
# 组件的组成
组件可以在他的输出中参考别的组件。这使得我们使用相同的组件抽象化表示任何层次的细节，一个按钮，窗体，对话框或是整个屏幕。在React中，这些全部都可以被表示为组件。
比如说，我们可以创建一个"App"组件用于渲染"Welcome"许多次:
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

有代表性的是，新的React应用有一个```App```组件at the very top。然而，如果你是整合React进已存在的应用中的话，你也许需要倒置you might start bottom-up with a small component like Button and gradually work your way to the top of the view hierarchy.
* Caveat：
* 组件必须返回一个根元素，这就是为什么我们在```<Welcome />```组件中包含了```<div>```。
# 提取组件
不要害怕吧组件分割成更小的组件，比如说，思考下下面的```Comment```组件：
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
[尝试一下](http://codepen.io/gaearon/pen/VKQwEo?editors=0010)

它接受一个对象```author```，一个字符串```text```和一个日期```date```作为props，用于描述一个社交网站上的评论。

这个组件很难更改，因为嵌套太多了。而且很难被别的部分重复使用。让我们提取一些组件出来。

首先，我们提取了```Avatar```
```javascript
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```
```Avatar```并不知道它会被渲染在```Comment```里面。这就是为什么我们给了prop一个更普遍的名字：```user```而非```author```

我们建议对prop的命名来自于组件所处的上下文而不是它在页面上看起来的样子。

我们现在可以将```Comment```简化一下：
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
接下来，我们要提取出```UserInfo```组件，它用于再用户名旁渲染```Avatar```：
```javascript
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```
这就让```Comment```更进一步的简化了：
```javascript
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
[尝试一下](http://codepen.io/gaearon/pen/rrJNJY?editors=0010)

提取组件也许刚开始看起来枯燥乏味，but having a palette of reusable components pays off in larger apps. A good rule of thumb is that if a part of your UI is used several times (Button, Panel, Avatar), or is complex enough on its own (App, FeedStory, Comment), it is a good candidate to be a reusable component.
# Props是只读的
无论你把组件定义为一个函数抑或一个类，它必不能改变他的props，思考下列代码：
```javascript
function sum(a, b) {
  return a + b;
}
```
这样的函数被称为[纯函数](https://en.wikipedia.org/wiki/Pure_function),因为他们不会试图改变他们的参数值，而且在相同的参数下，总是有相同的返回值。

作为对比，下列函数就不是纯函数，因为它改变了它的参数的值：
```javascript
 function withdraw(account, amount) {
  account.total -= amount;
}
```
React尽管非常灵活，但它有一条严格的规定：

**所有的React组件的行为必须像纯函数一样的尊重props**

当然，应用的UI是动态的而且随时间改变，在[下一节](./State and Lifecycle.md),我们会介绍一个新的概念"状态"，状态允许React组件随着时间响应用户的操作、响应网络、以及所有的东西，除非违背上一条规定。