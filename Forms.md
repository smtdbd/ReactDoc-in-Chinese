# 表单
HTML表单元素和React中的DOM元素稍微有一些差别。因为表单元素本来就要保持一些内部状态。比如说，这个表单在纯HTML中接受一个名字：
```HTML
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```
这个表单有着HTML默认的表单行为，当用户提交时，会开始浏览一个新页面。如果你想在React中实现这种行为的话,可以做到。但在大多数的情况中，用JS函数可以很方便的处理用户输入的表单和传输数据。标准做法是使用叫做"标准部件"的手段。
## 标准组件
再HTML中，表单元素，比如```<input>```,```<testarea>```,和```<select>```基本保持它们自己状态并基于用户输入更新它。再React中，性能组件的状态大多是多变的，而且只在```setState()```中刷新。

我们可以通过使得React状态为"单源头事实"来联合上述两种特性。这使得React组件渲染一个表单，并且随着用户输入控制应该发生什么。一个输入表单元素而且他的值还受REact控制的组件叫做"受控组件"

比如说，如果我们想创建类似刚才的示例那种当表单被提交时，记下名字的组件，我们可以利用受控组件来写出这个表单。
```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
[尝试一下](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

由于```value```属性被我们的窗体元素所设置，显示的值就总是```this.state.value```，使得React状态总为实际来源。又因```handleChange```再每次击键时都运行，更新React状态，显示的值就总是醉着用户输入而更新。

使用受控组件，每个状态改变都关联处理函数。这使得确认或改变用户输入变得非常容易。比如，如果我们想要强制输入名字时使用大写字母，我们可以把```handleChange```写为：
```javascript
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}
```
## 输入框标签
在HTML中，```<textarea>```元素利用他的子节点定义了其中的文字
```HTML
<textarea>
  Hello there, this is some text in a text area
</textarea>
```
在React中，```<textarea>```被```value```属性所替代了。如此这般，一个使用```<textarea>```的表单就可以被写成非常类似于含有一个单行输入的表单。
```javascript
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
注意```this.state.value```再构造函数中被初始化，所以输入框就有一些初始的文字在里面。
## 选择标签
在HTML中，```<select>```创建了一个下拉列表，比如说，HTML创建一个包含风味的下拉表：
```HTML
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```
注意一下Coconut选项是初始选项，因为```selected```属性。在React中，取而代之的是再```select```标签的根部使用```value```属性。这在受控标签里面更加方便，因为你只需要再一个地方更新，比如：
```javascript
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite La Croix flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
[尝试一下](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

总而言之，这使得 ```<input type="text">```, ```<textarea>```, 和 ```<select>```运行起来非常的相似，都通过控制受控组件的```value```标签就行了。
## 处理多个输入
当你需要处理多个受控```input```元素时，你可以为每个元素添加一个```name```标签，然后让处理函数基于```event.target.name```的值来选择该做什么。

比如：
```javascript
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputCh ange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

注意我们使用了ES6的[计算属性名称](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)语法来更新状态键值,相当于给入输入的名字：
```javascript
this.setState({
  [name]: value
});
```
这等同于下面的ES5代码：
```javascript
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```
还有，由于```setState()```的自动合并局部状态为当前状态，我们只需要使用需要改变的部分调用它即可。
## 可控组件的替代
有时使用可控组件会很烦闷，因为你得为所有改变数据的方式写事件处理函数并将传入的状态发给React组件。这回使得你将现有代码转为React或是整合React应用和一个非React库的过程变得相当的无聊。这时你需要看看非受控组件，一个输入表单的替代方式。
