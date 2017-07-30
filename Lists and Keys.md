# 表与键值
首先，先复习一下在JS里面你是如何转化一个list的

给定下面的代码，我们利用```map()```函数使得```number```数组里面的值全部翻倍。我们通过```map()```得到了一个新的名为```doubled```的数组，并将其输出。
```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```
这段代码把```[2, 4, 6, 8, 10]```输出到了控制台
## 渲染多个组件
你可以收集多个元素并利用大括号将其放在JSX中。

接下来，我们将会循环遍历```bumbers```数组并使用```map()```函数。为其每个值返回一个```<li>```元素。最终，我们把结果数组元素分配到了```listItems```中。
```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```
我们把整个```listItems```数组放进了```<ul>```元素中，然后将其渲染到DOM中。
```javascript
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/gaearon/pen/GjPyQr?editors=0011)

这段代码显示一个无序列表，上面显示1到5的数字
## 基础组件列表
你经常会需要再组件中渲染一个列表

我们可以将先前例子中的组件重构一下，使其接受一个```numbers```数组然后输出一个元素组成的无序列表
```javascript
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
当你运行这一段代码是，你会收到一个警告说"需要为表中的元素给定键值".键值是指一个特殊的字符串当你创建元素的列表时给定的属性。我们会在下一节讨论为什么这会如此重要。

接下来让我们在```numbers.map()```中为列表的值分配键值以修复这个问题：
```javascript
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/gaearon/pen/jrXYRR?editors=0011)
## 键值
键值帮助React区分部件，当它们被改变、增加、移除时。键值需要在数组中就为元素分配一个稳定的标识：
```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```
选择一个键值的最好方法是给予一个独特的字符串以便和它的兄弟们区分开来。d啊多数时候你会选用数据的ID作为键值：
```javascript
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```
如果你没有已经渲染的部件的稳定ID时，你也许需要使用部件的下标作为键值。：
```javascript
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```
如果部要重新整理的话，我们不推荐使用下标作为键值，即使会比较慢。如果你感兴趣的话，可以读一下[深入解释为何键值如此重要](https://facebook.github.io/react/docs/reconciliation.html#recursing-on-children)
## 提取组件和键值
键值只在数组的上下文中才有意义。

比如说，如果你提取了一个```ListItem```组件，你需要把键值保持再数组中的```<ListItem />```元素中要比根```<li>```节点元素要好得多

**例子：错误的键值用法**
```javascript
function ListItem(props) {
  const value = props.value;
  return (
    // Wrong! There is no need to specify the key here:
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Wrong! The key should have been specified here:
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
**示例：正确键值用法**
```javascript
function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/rthor/pen/QKzJKG?editors=0010)

好的做法是```map()```中的元素需要键值
## 键值必须在同一数组中是独一无二的
在数组中使用键值的话，那么在此数组中每个键值都应该是独一无二的。然而并不需要在全局里面是独一无二的。我们可以在处理两个不同的数组时使用相同的键值：
```javascript
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/gaearon/pen/NRZYGN?editors=0010)

键值为React起到提示作用，但是并不会被传入到你的组件中。如果你需要在你的组件中使用相同的值，将其显式的作为不同名的props传入。
```javascript
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```
再上面的例子中，```Post```组件可以读入```props.id```，但不能读```props.key```。
## 在JSX中嵌入map()
在上面的例子中我们知道了分离```listItems```变量和将其插入JSX中：
```javascript
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```
JSX允许插入任何表达式在大括号之间，所有我们也能内联```map()```：
```javascript
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```
[尝试一下](https://codepen.io/gaearon/pen/BLvYrB?editors=0010)

有时这能产生清楚明确的代码，但是这种风格也会遭到滥用。就像JS一样，是否为了易读而将其提取为变量，完全取决于你。但要记住，如果```map()```嵌套太深的话，就是提取组件的好时机了。
