# 安装
React是一个灵活的库，你可以将他使用在多种工程中，而且你还可以将他渐渐的部署在已经存在的代码库里而且不用做任何重写。
下面的哪个选项是你想要做的？  

<a herf="try">尝试React</a>  
<a herf="create">创建新应用</a>  
<a herf="add">添加到已存在的应用</a>  

<h2><a name="try">尝试React</a></h2>

如果你只是想玩玩而已的话，你可以使用[CodePen](http://codepen.io/gaearon/pen/rrpgNB?editors=0010)，这样你不用安装任何东西，你只需要修改代码然后看它是怎么工作的。 

  如果你想在自己的文本编辑器里工作的话，你也可以[下载](https://facebook.github.io/react/downloads/single-file-example.html)这个页面，编辑之后，用你本地的浏览器打开。这会有一个缓慢的翻译过程，所以不要在你的产品里面这么做。 

如果你想要用它做一个完整的应用程序的话，有两条路可以走：使用"create react app"命令，或者将其加入已存在的应用程序中。
<h2><a name="create">创建新应用</a></h2>  

[创建新的应用](http://github.com/facebookincubator/create-react-app)是构建一个新的React单页应用的最好的方法。这会设置你本地开发环境，以便你使用最新的JS特性，为您提供良好的开发体验，以及优化你的产品。

```
npm install -g create-react-app
create-react-app my-app

cd my-app
npm start
```
创建新的React应用不会触及后台逻辑或者数据库，它只会创建一个前台的管道，你可以用任何的后台与之配合。它使用类似于Babel和webpack的开发工具，但是可以不用任何设置即可运行。  

当你准备好部署你的产品时，运行
```
npm run build
```
即可为你的应用在build目录下创建一个优化后的工程。你可以在[ from its README](https://github.com/facebookincubator/create-react-app#create-react-app-)和[ User Guide](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#table-of-contents)获取更多有关于创建React应用的信息。
<h2><a name="add">添加到已存在的应用</a></h2>  