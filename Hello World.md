# Hello World
学习React的最简单的方法是使用[ this Hello World example code on CodePen](http://codepen.io/gaearon/pen/ZpvBNJ?editors=0010)网站。你不需要安装任何东西，只用打开另一个标签页然后跟着咱们的示例走就好了。如果你更喜欢使用本地开发环境，那就先看看[安装](./Installation.md)页面  
React最简单的示例是这样的
```javascript
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```
这段代码在页面上渲染了一个"Hello World"  
接下来的几节会逐渐引导你使用React，我们会检查React应用的building blocks：元素和组件。一旦你掌控了它们，你就可以利用一些可重复利用的小部件创建一个完整的应用。  
# 关于JavaScript的一些笔记
React是JavaScript的一个库，所以这篇文章是假定在你有JS基础上进行的。如果你不是很自信的话，我们建议你[复习](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)一下JS的知识点,一边之后的学习更加轻松。  
我们在例子里面还用上了一些ES6的语法。我们试图保守地使用它因为它实在是太新了，但是我们还是鼓励你去熟悉一下[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)、[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)、[模板字符串](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)、let和const声明。你可以在[Babel REPL](http://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact&experimental=false&loose=false&spec=false&code=const%20element%20%3D%20%3Ch1%3EHello%2C%20world!%3C%2Fh1%3E%3B%0Aconst%20container%20%3D%20document.getElementById('root')%3B%0AReactDOM.render(element%2C%20container)%3B%0A)上看看ES6的代码。