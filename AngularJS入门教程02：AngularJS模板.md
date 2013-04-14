是时候给这些网页来点动态特性了——用AngularJS！我们这里为后面要加入的控制器添加了一个测试。

一个应用的代码架构有很多种。对于AngularJS应用，我们鼓励使用[模型-视图-控制器（MVC）模式][]解耦代码和分离关注点。考虑到这一点，我们用AngularJS来为我们的应用添加一些模型、视图和控制器。

请重置工作目录：

    git checkout -f step-2

我们的应用现在有了一个包含三部手机的列表。

步骤1和步骤2之间最重要的不同在下面列出。，你可以到[GitHub][]去看完整的差别。

### 视图和模板

在AngularJS中，一个**视图**是模型通过HTML**模板**渲染之后的映射。这意味着，不论模型什么时候发生变化，AngularJS会实时更新结合点，随之更新视图。

比如，视图组件被AngularJS用下面这个模板构建出来：

    <html ng-app>
    <head>
      ...
      <script src="lib/angular/angular.js"></script>
      <script src="js/controllers.js"></script>
    </head>
    <body ng-controller="PhoneListCtrl">

      <ul>
        <li ng-repeat="phone in phones">
          {{phone.name}}
        <p>{{phone.snippet}}</p>
        </li>
      </ul>
    </body>
    </html>

我们刚刚把静态编码的手机列表替换掉了，因为这里我们使用[ngRepeat指令][]和两个用花括号包裹起来的AngularJS表达式——`{{phone.name}}`和`{{phone.snippet}}`——能达到同样的效果。

- 在`<li>`标签里面的`ng-repeat="phone in phones"`语句是一个AngularJS迭代器。这个迭代器告诉AngularJS用第一个`<li>`标签作为模板为列表中的每一部手机创建一个`<li>`元素。
- 正如我们在第0步时学到的，包裹在`phone.name`和`phone.snippet`周围的花括号标识着数据绑定。和常量计算不同的是，这里的表达式实际上是我们应用的一个数据模型引用，这些我们在`PhoneListCtrl`控制器里面都设置好了。

![tutorial_02.png][]

### 模型和控制器
在`PhoneListCtrl`**控制器**里面初始化了**数据模型**（这里只不过是一个包含了数组的函数，数组中存储的对象是手机数据列表）：

**app/js/controller.js**:

    function PhoneListCtrl($scope) {
      $scope.phones = [
        {"name": "Nexus S",
         "snippet": "Fast just got faster with Nexus S."},
        {"name": "Motorola XOOM™ with Wi-Fi",
         "snippet": "The Next, Next Generation tablet."},
        {"name": "MOTOROLA XOOM™",
         "snippet": "The Next, Next Generation tablet."}
      ];
    }

尽管控制器看起来并没有起到什么控制的作用，但是它在这里起到了至关重要的作用。通过给定我们数据模型的语境，控制器允许我们建立模型和视图之间的数据绑定。我们是这样把表现层，数据和逻辑部件联系在一起的：

- `PhoneListCtrl`——控制器方法的名字（在JS文件`controllers.js`中）和`<body>`标签里面的[ngController][]指令的值相匹配。
- 手机的数据此时与注入到我们控制器函数的**作用域**（`$scope`）相关联。当应用启动之后，会有一个根作用域被创建出来，而控制器的作用域是根作用域的一个典型后继。这个控制器的作用域对所有`<body ng-controller="PhoneListCtrl">`标记内部的数据绑定有效。

AngularJS的作用域理论非常重要：一个作用域可以视作模板、模型和控制器协同工作的粘接器。AngularJS使用作用域，同时还有模板中的信息，数据模型和控制器。这些可以帮助模型和视图分离，但是他们两者确实是同步的！任何对于模型的更改都会即时反映在视图上；任何在视图上的更改都会被立刻体现在模型中。

想要更加深入理解AngularJS的作用域，请参看[AngularJS作用域文档][]。

### 测试
“AngularJS方式”让开发时代码测试变得十分简单。让我们来瞅一眼下面这个为控制器新添加的单元测试：

**test/unit/controllersSpec.js:**

    describe('PhoneCat controllers', function() {

      describe('PhoneListCtrl', function(){

        it('should create "phones" model with 3 phones', function() {
          var scope = {},
          ctrl = new PhoneListCtrl(scope);

          expect(scope.phones.length).toBe(3);
        });
      });
    });

这个测试验证了我们的手机数组里面有三条记录（暂时无需弄明白这个测试脚本）。这个例子显示出为AngularJS的代码创建一个单元测试是多么的容易。正因为测试在软件开发中是必不可少的环节，所以我们使得在AngularJS可以轻易地构建测试，来鼓励开发者多写它们。

在写测试的时候，AngularJS的开发者倾向于使用Jasmine行为驱动开发（BBD）框架中的语法。尽管AngularJS没有强迫你使用Jasmine，但是我们在教程里面所有的测试都使用Jasmine编写。你可以在Jasmine的[官方主页][]或者[Jasmine Wiki][]上获得相关知识。

基于AngularJS的项目被预先配置为使用[JsTestDriver][]来运行单元测试。你可以像下面这样运行测试：

1. 在一个单独的终端上，进入到`angular-phonecat`目录并且运行`./scripts/test-server.sh`来启动测试（Windows命令行下请输入`.\scripts\test-server.bat`来运行脚本，后面脚本命令运行方式类似）；
2. 打开一个新的浏览器窗口，并且转到<http://localhost:9876> ；
3. 选择“Capture this browser in strict mode”。

   这个时候，你可以抛开你的窗口不管然后把这事忘了。JsTestDriver会自己把测试跑完并且把结果输出在你的终端里。

4. 运行`./scripts/test.sh`进行测试 。

   你应当看到类似于如下的结果：

        Chrome: Runner reset.
        .
        Total 1 tests (Passed: 1; Fails: 0; Errors: 0) (2.00 ms)
          Chrome 19.0.1084.36 Mac OS: Run 1 tests (Passed: 1; Fails: 0; Errors 0) (2.00 ms)

    耶！测试通过了！或者没有...
    注意：如果在你运行测试之后发生了错误，关闭浏览器然后回到终端关了脚本，然后在重新来一边上面的步骤。

## 练习
- 为`index.html`添加另一个数据绑定。例如：

        <p>Total number of phones: {{phones.length}}</p>

- 创建一个新的数据模型属性，并且把它绑定到模板上。例如：

        $scope.hello = "Hello, World!"

    更新你的浏览器，确保显示出来“Hello, World!”

- 用一个迭代器创建一个简单的表：

        <table>
            <tr><th>row number</th></tr>
            <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7]"><td>{{i}}</td></tr>
        </table>

    现在让数据模型表达式的`i`增加1：

        <table>
            <tr><th>row number</th></tr>
            <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7]"><td>{{i+1}}</td></tr>
        </table>

- 确定把`toBe(3)`改成`toBe(4)`之后单元测试失败，然后重新跑一遍`./scripts/test.sh`脚本

## 总结
你现在拥有一个模型，视图，控制器分离的动态应用了，并且你随时进行了测试。现在，你可以进入到[步骤3][step_03]来为应用加入全文检索功能了。

[step_03]: http://angularjs.cn/A006
[模型-视图-控制器（MVC）模式]: http://en.wikipedia.org/wiki/Model%E2%80%93View%E2%80%93Controller
[GitHub]: https://github.com/angular/angular-phonecat/compare/step-1...step-2
[ngRepeat指令]: http://docs.angularjs.org/api/ng.directive:ngRepeat
[tutorial_02.png]: http://docs.angularjs.org/img/tutorial/tutorial_02.png
[ngController]: http://docs.angularjs.org/api/ng.directive:ngController
[AngularJS作用域文档]: http://docs.angularjs.org/api/ng.$rootScope.Scope
[官方主页]: http://pivotal.github.com/jasmine/
[Jasmine Wiki]: https://github.com/pivotal/jasmine/wiki
[JsTestDriver]: http://code.google.com/p/js-test-driver/
