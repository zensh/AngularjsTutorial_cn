学习AngularJS的一个好方法是逐步完成本教程，它将引导您构建一个完整的AngularJS web应用程序。 该web应用是一个Android设备清单的目录列表，您可以筛选列表以便查看您感兴趣的设备，然后查看设备的详细信息。

![PhoneApp][PhoneApp]

**本教程将向您展示AngularJS怎样使得web应用更智能更灵活，而且不需要各种扩展程序或插件。 通过本教程的学习，您将：**

1.  阅读示例学习怎样使用AngularJS的客户端数据绑定和依赖注入功能来建立可立即响应用户操作的动态数据视图。
2.  学习如何使用AngularJS创建数据侦听器，且不需要进行DOM操作。
3.  学习一种更好、更简单的方法来测试您的web应用程序。
4.  学习如何使用AngularJS创建常见的web任务，例如更方便的将数据引入应用程序。

而且这一切可在任何一个浏览器实现，无需配置浏览器！

**当你完成了本教程后，您将学会：**

1.  创建一个可在任何浏览器中的工作的动态应用。
2.  了解AngularJS与其它JavaScript框架之间的区别。
3.  了解AngularJS如何实现数据绑定。
4.  利用AngularJS的种子项目快速创建自己的项目。
5.  创建和运行测试。
6.  学习更多AngularJS标识资源(API)。

本教程将指导您完成一个简单的应用程序创建过程，包括编写和运行单元测试、不断地测试应用。 教程的每个步骤为您提供建议以了解更多有关AngularJS和您创建的web应用程序。
您可能会在短时间内快速读完本教程，也可能需要花大量时间深入研究本教程。 如果想看一个简短的AngularJS介绍文档，请查看[快速开始][ Getting Started]文档。

## 搭建学习环境

无论是Mac、Linux或Windows环境中，您均可遵循本教程学习编程。您可以使用源代码管理版本控制系统Git获取本教程项目的源代码文件，或直接从网上下载本教程项目源代码文件的镜像归档压缩包。

1.  您需要安装Node.js和Testacular来运行本项目，请到[Node.js][Node.js]官方网站下载并安装最新版，然后把node可执行程序路径添加到系统环境变量`PATH`中，完成后在命令行中运行一下命令可以查看是否安装成功：

        node -version

    然后安装[Testacular][Testacular]单元测试程序，请运行如下命令：

        npm install -g testacular

2.  安装[Git][Git]工具，然后用以下命令从Github复制本教程项目的源代码文件：

        git clone git://github.com/angular/angular-phonecat.git

    您也可以直接从网上[下载][angular-phonecat]本教程项目源代码的镜像归档压缩包。这个命令会在您当前文件夹中建立新文件夹`angular-phonecat`。

3.  最后一件事要做的就是确保您的计算机安装了web浏览器和文本编辑器。

4.  进入教程源代码文件包angular-phonecat，运行服务器后台程序，开始[学习AngularJS][step_00]！

        cd angular-phonecat
        node scripts/web-server.js

[step_00]:  http://angularjs.cn/A003
[PhoneApp]: http://docs.angularjs.org/img/tutorial/catalog_screen.png
[Getting Started]: http://angularjs.cn/A002
[Git]: http://git-scm.com/download
[angular-phonecat]: https://github.com/angular/angular-phonecat
[Node.js]: http://nodejs.org/
[Testacular]: http://vojtajina.github.com/testacular
