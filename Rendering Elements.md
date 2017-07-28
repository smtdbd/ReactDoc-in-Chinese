# 渲染元素
元素是React应用中最小的building blocks

描述一个Helloworld元素以显示再屏幕上
```javascript
const element = <h1>Hello, world</h1>;
```
不像是浏览器DOM元素，React元素是简单的对象，而且容易创建。React DOM更关心更新DOM以便与React元素相匹配
* Note:
* 也许元素会和一个更为广泛的观念相混淆：组件。我们会在下一节介绍[组件](./Components and Props.md)。元素是组件的组成元件。我们建议你在读完这一节之前不要跳过。
# 将一个元素渲染至DOM
```html
<div id="root"></div>
```
我们将其称为root DOM节点，因为其中的所有东西都会被ReactDOM所管理。

只用React构建应用程序的话，常常会有一个单独的root DOM 节点。如果你想将React整合进一个已存在的应用中，你也许会有许多孤立的root DOM节点。

为了将React元素渲染进root DOM节点中，常使用"ReactDOM.render()"
```javascript
const element = <h1>Hello, world</h1>;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[尝试一下](http://codepen.io/gaearon/pen/rrpgNB?editors=1010)
# 刷新渲染元素
React元素是[不变的](https://en.wikipedia.org/wiki/Immutable_object).一旦你创造了一个元素，你就不能更改他的属性或者子节点。一个元素就类似于电影里的一帧，它代表着UI中的某个时间点。

既然咱们知道了这么多，更新UI的唯一方法是创建一个元素，然后将其传给"ReactDOM.render()"

思考一下这个时钟例子：
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
[尝试一下](http://codepen.io/gaearon/pen/dpdoYR?editors=0010)

它将"ReactDOM.render()"作为"setInterval()"的回调函数，每秒钟调用一次。
* Note:
* 在练习中，大多数的React应用只会调用ReactDOM.render()一次，再下一节中，我们会学到这些代码是如何被装入[状态部件](https://facebook.github.io/react/docs/state-and-lifecycle.html)中的。
* 我们建议你不要跳过，因为他们之间是相互联系的。
# React只会再必要时更新
React DOM会将元素及其子节点与之前的相比较，然后在必要时更新。
你可以在上一个[例子](https://codepen.io/gaearon/pen/gwoJZk?editors=0010)中利用浏览器的检查元素工具观察。
<img src="./img/granular-dom-updates.gif"></img>

尽管我们创建了一个元素用于表达整个UI树的每一刻，但是只有再节点内容发生改变时，才会更新React DOM。

在我们的经验中，与其给定UI在每一刻看起来应该是什么样，倒不如定义好它该如何改变，以减少整个工程的BUG。