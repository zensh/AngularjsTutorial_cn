我们现在开始准备编写AngularJS应用——**phonecat**。这一步骤（步骤0），您将会熟悉重要的源代码文件，学习启动包含AngularJS种子项目的开发环境，并在浏览器端运行应用。

1.  进入angular-phonecat目录，运行如下命令：

        git checkout -f step-0

    该命令将重置phonecat项目的工作目录，建议您在每一学习步骤运行此命令，将命令中的数字改成您学习步骤对应的数字，该命令将清除您在工作目录内做的任何更改。

2.  运行以下命令：

        node scripts/web-server.js

    来启动服务器，启动后命令行终端将会提示**`Http Server running at http://localhost:8000`**,请不要关闭该终端，关闭该终端即关闭了服务器。在浏览器中输入<http://localhost:8000/app/index.html>来访问我们的**phonecat**应用。

现在，在浏览器中您应该已经看到了我们的初始应用，很简单，但说明我们的项目已经可以运行了。

应用中显示的“Nothing here yet!”是由如下HTML代码构建而成，代码中包含了AngularJS的关键元素，正是我们需要学习的。

**app/index.html**

    <!doctype html>
    <html lang="en" ng-app>
    <head>
        <meta charset="utf-8">
        <title>My HTML File</title>
        <link rel="stylesheet" href="css/app.css">
        <link rel="stylesheet" href="css/bootstrap.css">
        <script src="lib/angular/angular.js"></script>
    </head>
    <body>
    <p>Nothing here {{'yet' + '!'}}</p>
    </body>
    </html>


## 代码在做什么呢？

### `ng-app`指令：

    <html lang="en" ng-app>

`ng-app`指令标记了AngularJS脚本的作用域，在`<html>`中添加`ng-app`属性即说明整个`<html>`都是AngularJS脚本作用域。开发者也可以在局部使用`ng-app`指令，如`<div ng-app>`，则AngularJS脚本仅在该`<div>`中运行。

### AngularJS脚本标签：

    <script src="lib/angular/angular.js"></script>


这行代码载入angular.js脚本，当浏览器将整个HTML页面载入完毕后将会执行该angular.js脚本，angular.js脚本运行后将会寻找含有`ng-app`指令的HTML标签，该标签即定义了AngularJS应用的作用域。

### 双大括号绑定的表达式：

    <p>Nothing here {{'yet' + '!'}}</p>


这行代码演示了AngularJS模板的核心功能——绑定，这个绑定由双大括号`{{}}`和表达式`'yet' + '!'`组成。

这个绑定告诉AngularJS需要运算其中的表达式并将结果插入DOM中，接下来的步骤我们将看到，DOM可以随着表达式运算结果的改变而实时更新。

AngularJS表达式[Angular expression]是一种类似于JavaScript的代码片段，AngularJS表达式仅在AngularJS的作用域中运行，而不是在整个DOM中运行。

## 引导AngularJS应用

通过ngApp指令来自动引导AngularJS应用是一种简洁的方式，适合大多数情况。在高级开发中，例如使用脚本装载应用，您也可以使用[bootstrap][]手动引导AngularJS应用。

**AngularJS应用引导过程有3个重要点：**

1.  [注入器(injector)][$injector]将用于创建此应用程序的依赖注入(dependency injection)；
2.  注入器将会创建[根作用域]($rootScope)作为我们应用模型的范围；
3.  AngularJS将会链接根作用域中的DOM，从用ngApp标记的HTML标签开始，逐步处理DOM中指令和绑定。

一旦AngularJS应用引导完毕，它将继续侦听浏览器的HTML触发事件，如鼠标点击事件、按键事件、HTTP传入响应等改变DOM模型的事件。这类事件一旦发生，AngularJS将会自动检测变化，并作出相应的处理及更新。

上面这个应用的结构非常简单。该模板包仅含一个指令和一个静态绑定，其中的模型也是空的。下一步我们尝试稍复杂的应用！

![img_tutorial_00][]

## 我工作目录中这些文件是干什么的？

上面的应用来自于AngularJS种子项目，我们通常可以使用[AngularJS种子项目][angular-seed]来创建新项目。种子项目包括最新的AngularJS代码库、测试库、脚本和一个简单的应用程序示例，它包含了开发一个典型的web应用程序所需的基本配置。

对于本教程，我们对AngularJS种子项目进行了下列更改：

1.  删除示例应用程序；
2.  添加手机图像到app/img/phones/；
3.  添加手机数据文件(JSON)到app/phones/；
4.  添加[Twitter Bootstrap][Twitter Bootstrap]文件到app/css/ 和app/img/。

## 练习

试试把关于数学运算的新表达式添加到index.html：

    <p>1 + 2 = {{ 1 + 2 }}</p>

## 总结

现在让我们转到[步骤1][step_01]，将一些内容添加到web应用程序。

[img_tutorial_00]: http://docs.angularjs.org/img/tutorial/tutorial_00.png
[bootstrap]: http://angularjs.cn/A00o
[Angular expression]: http://angularjs.cn/A00s
[$injector]: http://docs.angularjs.org/api/AUTO.$injector
[$rootScope]: http://docs.angularjs.org/api/ng.$rootScope
[step_01]: http://angularjs.cn/A004
[angular-seed]: https://github.com/angular/angular-seed
[Twitter Bootstrap]: http://twitter.github.com/bootstrap/
