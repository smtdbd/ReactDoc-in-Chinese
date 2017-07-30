# 条件渲染
在React中，你可以创建不同的组件，其中封装着你所需要的行为。那么，你可以只渲染其中的一部分，取决于你的应用的状态

在React中，条件渲染和JS的条件语句其实是以相同的方式工作着。用JS运算符类似与```if```或者是[条件运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)来创建代表当前状态的元素，然后让React更新UI以适配它们

思考下面两个组件：
```javascript
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
```
我们创建了一个```Greeting```组件来表示上面组件之一，这取决于用户是否登陆。
```javascript
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/gaearon/pen/ZpVxNq?editors=0011)

这个示例判断```isLoggedIn```prop的状态来渲染不同的欢迎界面
# 元素变量
你可以使用变量来储存元素，这有助于在条件渲染部分组件时剩下的输出不会改变

思考下面两个新的组件表示的登入登出按钮：
```javascript
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```
在上面的示例中，我们创建了一个名为```LoginControl```的状态组件

它渲染```<LoginButton />```还是```<LoigoutButton />```完全取决于它当前的状态。在先前的例子中，它还能渲染```<Greeting />```
```javascript
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;

    let button = null;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/gaearon/pen/QKzAgB?editors=0010)

定义一个变量并使用```if```是条件渲染一个组件的良好方法，有时你也许想用更简短的语法，下面有一些JSX内联的条件。
# if内联逻辑&&运算符
你也许知道JSX中嵌入表达式的方法是用大括号括起来。其中也包含了JS逻辑运算符:```&&```。你可以在元素中便利的使用它。
```javascript
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```
这总是管用，因为再JS中```true && expression```总是判断```expression```，而```false && expression```总是判断为```false```

因此，如果状态是```true```，这个在```&&```右侧的元素就会显示在输出界面中。如果是```false```，React将会忽略并跳过它。
# 在if-else中内联条件运算符
另一个条件渲染元素的方法就是使用JS条件运算符[condition ? true : false](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)
在下面的示例中，我们使用它来条件渲染一些文字：
```javascript
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```
这还可以用更庞大的表达式，但是稍微有些影响理解：
```javascript
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```
就像JS一样，选择什么方式取决于你和你的队伍觉得哪个更易读。但是要记住，如果条件太复杂的话，也许提取组件会是一个更好的选择。
# 阻止组件渲染
有一小部分情况下，即使组件被另一个组件渲染出来了，你依然想让它不可见。这时可以再渲染输出时返回一个```null```

如下例所示，```<WarningBanner />```组件是否渲染取决于```warn```prop，如果他的值为```false```，那么这个组件就不会被渲染出来：
```javascript
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true}
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(prevState => ({
      showWarning: !prevState.showWarning
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);
```
[尝试一下](https://codepen.io/gaearon/pen/Xjoqwm?editors=0010)

组件的```render()```方法返回一个```null```不会影响组件的生命周期方法。比如说```componentWillUpdate```和```componentDidUpdate```还是会被调用。