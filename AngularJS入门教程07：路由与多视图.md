在这一步，你将学习如何创建一个布局模板并且通过路由功能来构建一个具有多个视图的应用。

请重置工作目录：

    git checkout -f step-7

注意到现在当你转到`app/index.html`时，你会被重定向到`app/index.html#/phones`并且相同的手机列表在浏览器中显示了出来。当你点击一个手机链接时，一个手机详细信息列表也被显示了出来。

步骤6和步骤7之间最重要的不同在下面列出。你可以在[GitHub][]里看到完整的差别。

## 多视图，路由和布局模板

我们的应用正慢慢发展起来并且变得逐渐复杂。在步骤7之前，应用只给我们的用户提供了一个简单的界面（一张所有手机的列表），并且所有的模板代码位于`index.html`文件中。下一步是增加一个能够显示我们列表中每一部手机详细信息的页面。

为了增加详细信息视图，我们可以拓展`index.html`来同时包含两个视图的模板代码，但是这样会很快给我们带来巨大的麻烦。相反，我们要把`index.html`模板转变成**“布局模板”**。这是我们应用所有视图的通用模板。其他的“局部布局模板”随后根据当前的**“路由”**被充填入，从而形成一个完整视图展示给用户。

AngularJS中应用的路由通过[$routeProvider][ng.$routeProvider]来声明，它是[$route][ng.$route]服务的提供者。这项服务使得控制器、视图模板与当前浏览器的URL可以轻易集成。应用这个特性我们就可以实现[深链接](http://en.wikipedia.org/wiki/Deep_linking)，它允许我们使用浏览器的历史(回退或者前进导航)和书签。

### 关于依赖注入（DI），注入器（Injector）和服务提供者（Providers）

正如从前面你学到的，[依赖注入][guide/di]是AngularJS的核心特性，所以你必须要知道一点这家伙是怎么工作的。

当应用引导时，AngularJS会创建一个注入器，我们应用后面所有依赖注入的服务都会需要它。这个注入器自己并不知道`$http`和`$route`是干什么的，实际上除非它在模块定义的时候被配置过，否则它根本都不知道这些服务的存在。注入器唯一的职责是载入指定的服务模块，在这些模块中注册所有定义的服务提供者，并且当需要时给一个指定的函数注入依赖（服务）。这些依赖通过它们的提供者“懒惰式”（需要时才加载）实例化。

提供者是提供（创建）服务实例并且对外提供API接口的对象，它可以被用来控制一个服务的创建和运行时行为。对于`$route`服务来说，`$routeProvider`对外提供了API接口，通过API接口允许你为你的应用定义路由规则。

AngularJS模块解决了从应用中删除全局状态和提供方法来配置注入器这两个问题。和`AMD`或者`require.js`这两个模块（非AngularJS的两个库）不同的是，AngularJS模块并没有试图去解决脚本加载顺序以及懒惰式脚本加载这样的问题。这些目标和AngularJS要解决的问题毫无关联，所以这些模块完全可以共存来实现各自的目标。

### App 模块

**app/js/app.js**

    angular.module('phonecat', []).
      config(['$routeProvider', function($routeProvider) {
      $routeProvider.
          when('/phones', {templateUrl: 'partials/phone-list.html',   controller: PhoneListCtrl}).
          when('/phones/:phoneId', {templateUrl: 'partials/phone-detail.html', controller: PhoneDetailCtrl}).
          otherwise({redirectTo: '/phones'});
    }]);

为了给我们的应用配置路由，我们需要给应用创建一个模块。我们管这个模块叫做`phonecat`，并且通过使用`config`API，我们请求把`$routeProvider`注入到我们的配置函数并且使用`$routeProvider.when`API来定义我们的路由规则。

注意到在注入器配置阶段，提供者也可以同时被注入，但是一旦注入器被创建并且开始创建服务实例的时候，他们就不再会被外界所获取到。

**我们的路由规则定义如下**

*   当URL 映射段为`/phones`时，手机列表视图会被显示出来。为了构造这个视图，AngularJS会使用`phone-list.html`模板和`PhoneListCtrl`控制器。
*   当URL 映射段为`/phone/:phoneId`时，手机详细信息视图被显示出来。这里`:phoneId`是URL的变量部分。为了构造手机详细视图，AngularJS会使用`phone-detail.html`模板和`PhoneDetailCtrl`控制器。

我们重用之前创造过的`PhoneListCtrl`控制器，同时我们为手机详细视图添加一个新的`PhoneDetailCtrl`控制器，把它存放在`app/js/controllers.js`文件里。

`$route.otherwise({redirectTo: '/phones'})`语句使得当浏览器地址不能匹配我们任何一个路由规则时，触发重定向到`/phones`。

注意到在第二条路由声明中`:phoneId`参数的使用。`$route`服务使用路由声明`/phones/:phoneId`作为一个匹配当前URL的模板。所有以`:`符号声明的变量（此处变量为`phones`）都会被提取，然后存放在[$routeParams][ng.$routeParams]对象中。

为了让我们的应用引导我们新创建的模块，我们同时需要在[ngApp][ng.directive:ngApp]指令的值上指明模块的名字：

**app/index.html**

    <!doctype html>
    <html lang="en" ng-app="phonecat">
    ...

### 控制器

**app/js/controllers.js**

    ...
    function PhoneDetailCtrl($scope, $routeParams) {
      $scope.phoneId = $routeParams.phoneId;
    }

    //PhoneDetailCtrl.$inject = ['$scope', '$routeParams'];

### 模板

`$route`服务通常和[ngView][ng.directive:ngView]指令一起使用。`ngView`指令的角色是为当前路由把对应的视图模板载入到布局模板中。

**app/index.html**

    <html lang="en" ng-app="phonecat">
    <head>
    ...
      <script src="lib/angular/angular.js"></script>
      <script src="js/app.js"></script>
      <script src="js/controllers.js"></script>
    </head>
    <body>

      <div ng-view></div>

    </body>
    </html>

注意，我们把`index.html`模板里面大部分代码移除，我们只放置了一个`<div>`容器，这个`<div>`具有`ng-view`属性。我们删除掉的代码现在被放置在`phone-list.html`模板中：

**app/partials/phone-list.html**

    <div class="container-fluid">
      <div class="row-fluid">
        <div class="span2">
          <!--Sidebar content-->

          Search: <input ng-model="query">
          Sort by:
          <select ng-model="orderProp">
            <option value="name">Alphabetical</option>
            <option value="age">Newest</option>
          </select>

        </div>
        <div class="span10">
          <!--Body content-->

          <ul class="phones">
            <li ng-repeat="phone in phones | filter:query | orderBy:orderProp" class="thumbnail">
              <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
              <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
              <p>{{phone.snippet}}</p>
            </li>
          </ul>

        </div>
      </div>
    </div>

同时我们为手机详细信息视图添加一个占位模板。

**app/partials/phone-detail.html**

    TBD: detail view for {{phoneId}}

注意到我们的布局模板中没再添加`PhoneListCtrl`或`PhoneDetailCtrl`控制器属性！

### 测试

为了自动验证所有的东西都良好地集成起来，我们需要写一些端到端测试，导航到不同的URL上然后验证正确地视图被渲染出来。

    ...
      it('should redirect index.html to index.html#/phones', function() {
        browser().navigateTo('../../app/index.html');
        expect(browser().location().url()).toBe('/phones');
      });
    ...

     describe('Phone detail view', function() {

        beforeEach(function() {
          browser().navigateTo('../../app/index.html#/phones/nexus-s');
        });


        it('should display placeholder page with phoneId', function() {
          expect(binding('phoneId')).toBe('nexus-s');
        });
     });

你现在可以刷新你的浏览器，然后重新跑一遍端到端测试，或者你可以在[AngularJS的服务器](http://angular.github.com/angular-phonecat/step-4/test/e2e/runner.html)上运行一下。

## 练习

试着在`index.html`上增加一个`{{orderProp}}`绑定，当你在手机列表视图上时什么也没变。这是因为`orderProp`模型仅仅在`PhoneListCtrl`管理的作用域下才是可见的，这与`<div ng-view>`元素相关。如果你在`phone-list.html`模板中加入同样的绑定，那么这个绑定会按你设想的那样被渲染出来。

## 总结

设置路由并实现手机列表视图之后，我们已经可以进入[步骤8][step_08]来实现手机详细信息视图了。

[GitHub]: https://github.com/angular/angular-phonecat/compare/step-6...step-7
[ng.$routeProvider]: http://code.angularjs.org/1.1.0/docs/api/ng.$routeProvider
[ng.$route]: http://code.angularjs.org/1.1.0/docs/api/ng.$route
[guide/di]: http://code.angularjs.org/1.1.0/docs/guide/di
[ng.$routeParams]: http://code.angularjs.org/1.1.0/docs/api/ng.$routeParams
[ng.directive:ngApp]: http://code.angularjs.org/1.1.0/docs/api/ng.directive:ngApp
[ng.directive:ngView]: http://code.angularjs.org/1.1.0/docs/api/ng.directive:ngView
[step_08]: http://angularjs.cn/A00b
