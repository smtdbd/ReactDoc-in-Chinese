# 关于JSX
思考一下下面的变量定义：

```javascript
const element = <h1>Hello, world!</h1>;
```

这个有趣的标签语法，它既不是一个字符串，也不是一个HTML。  
 
它叫做JSX，是JS语法的一种扩展，我们建议您使用它在React里面描述UI看起来应该是什么样子的。JSX也许会让你想起一个模板语言，但是它完全是来自于JavaScript的。 

JSX生产了React的"元素"。我们会在[下一节](./Rendering Elements.md)介绍这是怎么被渲染到DOM中的。接下来，你将会看到一些必须的JSX的知识。
# 在JSX中嵌入表达式
你可以在JSX中用大括号包裹住你想嵌入的JS表达式。比如说，"2 + 2","user.firstName",和"formatName(user)"都是合法的表达式
```javascript
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```
[尝试一下](http://codepen.io/gaearon/pen/PGEjdG?editors=0010)   
为了保证可读性，我们把JSX分成了许多行。尽管并非必须，但当你这样做时，我们希望你将其放在括号中以避免[自动添加分号](http://stackoverflow.com/q/2846283)陷阱.
# JSX也是一个表达式
在编译之后，JSX表达式会变成一个JS的Object    

这以为这你可以在JSX中使用if语句或者for循环，将其分配给变量，把它当作一个参数，或者作为函数的返回值。

```javascript
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```
# 说明JSX的属性
你也许使用过引号来指定字符串的字面属性

```javascript
const element = <div tabIndex="0"></div>;
```
也许你也用过大括号来将一段JS语句嵌入到一个属性中。

```javascript
const element = <img src={user.avatarUrl}></img>;
```
当你向属性中插入JS语句时，不要用引号包裹住大括号。否则JSX将会把属性当作字符串而不是一条语句。所以你要么使用引号(用于字符串)，要么使用大括号(用于表达式)，不要同时使用。
# 用JSX声明子标签
如果一个标签是空的，你可以用/>将其立即结束，就像XML一样。
```javascript
const element = <img src={user.avatarUrl} />;
```
JSX也含有子标签
```javascript
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```
* 注意： 由于JSX相比HTML更接近于JS，React DOM使用驼峰命名法而不是HTML的标签命名。
* 比如：class在JSX中是className，tabindex是tabIndex。
# JSX阻止注入攻击
下例使用JSX嵌入一个安全的用户输入框
```javascript
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```
默认情况下，React DOM会在所有变量被渲染之前，将其从JSX中[释放](http://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)。因此可以保证你的应用不会被注入任何不明确的输入内容。所有东西在渲染之前都会被转化为字符串。这有助于避免[XSS](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击。
# JSX 表示对象
Babel编译JSX时会将创建对象转变为调用"React.createElement()"

下面两个示例是完全相同的。
```javascript
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```
```javascript
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
"React.createElement()"执行了一些检查有助于你写出没有bug的代码，但是其实它是这样创建一个对象的：
```javascript
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world'
  }
};
```
这些对象被叫做"React元素"。你可以将其理解为它用来说明你想在屏幕上显示的东西。React读入这些对象并用其来构建DOM以保持实时性。

我们会在下一节讲如何React元素是如何被渲染至DOM中的。
* Tip：
* 我们建议为您的编辑器搜索"Babel"语法组合一边保持对ES6和JSX的代码着色及代码高亮。