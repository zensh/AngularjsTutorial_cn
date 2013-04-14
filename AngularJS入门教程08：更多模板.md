在这一步，你将实现手机详细信息视图，这个视图会在用户点击手机列表中的一部手机时被显示出来。

请重置工作目录：

    git checkout -f step-8

现在当你点击列表中的一部手机之后，这部手机的详细信息页面就会被显示出来。

为了实现手机详细信息视图我们将会使用[$http][ng.$http]来获取数据，同时我们也要增添一个`phone-detail.html`视图模板。

步骤7和步骤8之间最重要的不同在下面列出。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-7...step-8)里看到完整的差别。

### 数据

除了`phones.json`，`app/phones/`目录也包含了每一部手机信息的json文件。

**app/phones/nexus-s.json**（样例片段）

    {
      "additionalFeatures": "Contour Display, Near Field Communications (NFC),...",
      "android": {
          "os": "Android 2.3",
          "ui": "Android"
      },
      ...
      "images": [
          "img/phones/nexus-s.0.jpg",
          "img/phones/nexus-s.1.jpg",
          "img/phones/nexus-s.2.jpg",
          "img/phones/nexus-s.3.jpg"
      ],
      "storage": {
          "flash": "16384MB",
          "ram": "512MB"
      }
    }

这些文件中的每一个都用相同的数据结构描述了一部手机的不同属性。我们会在手机详细信息视图中显示这些数据。

### 控制器

我们使用`$http`服务获取数据，以此来拓展我们的`PhoneListCtrl`。这和之前的手机列表控制器的工作方式是一样的。

**app/js/controllers.js**

    function PhoneDetailCtrl($scope, $routeParams, $http) {
      $http.get('phones/' + $routeParams.phoneId + '.json').success(function(data) {
        $scope.phone = data;
      });
    }

    //PhoneDetailCtrl.$inject = ['$scope', '$routeParams', '$http'];

为了构造HTTP请求的URL，我们需要`$route`服务提供的当前路由中抽取`$routeParams.phoneId`。

### 模板

**phone-detail.html**文件中原有的TBD占位行已经被列表和构成手机详细信息的绑定替换掉了。注意到，这里我们使用AngularJS的`{{表达式}}`标记和`ngRepeat`来在视图中渲染数据模型。

**app/partials/phone-detail.html**

    <img ng-src="{{phone.images[0]}}" class="phone">

    <h1>{{phone.name}}</h1>

    <p>{{phone.description}}</p>

    <ul class="phone-thumbs">
      <li ng-repeat="img in phone.images">
        <img ng-src="{{img}}">
      </li>
    </ul>

    <ul class="specs">
      <li>
        <span>Availability and Networks</span>
        <dl>
          <dt>Availability</dt>
          <dd ng-repeat="availability in phone.availability">{{availability}}</dd>
        </dl>
      </li>
        ...
      </li>
        <span>Additional Features</span>
        <dd>{{phone.additionalFeatures}}</dd>
      </li>
    </ul>

### 测试

我们来写一个新的单元测试，这个测试和我们在步骤5中为`PhoneListCtrl`写的那个很像。

**test/unit/controllersSpec.js**

    ...
      describe('PhoneDetailCtrl', function(){
        var scope, $httpBackend, ctrl;

        beforeEach(inject(function(_$httpBackend_, $rootScope, $routeParams, $controller) {
          $httpBackend = _$httpBackend_;
          $httpBackend.expectGET('phones/xyz.json').respond({name:'phone xyz'});

          $routeParams.phoneId = 'xyz';
          scope = $rootScope.$new();
          ctrl = $controller(PhoneDetailCtrl, {$scope: scope});
        }));


        it('should fetch phone detail', function() {
          expect(scope.phone).toBeUndefined();
          $httpBackend.flush();

          expect(scope.phone).toEqual({name:'phone xyz'});
        });
      });
    ...

执行`./scripts/test.sh`脚本来执行测试，你应该会看到如下输出：

    Chrome: Runner reset.
    ...
    Total 3 tests (Passed: 3; Fails: 0; Errors: 0) (5.00 ms)
      Chrome 19.0.1084.36 Mac OS: Run 3 tests (Passed: 3; Fails: 0; Errors 0) (5.00 ms)

同时，我们也添加一个端到端测试，指向Nexus S手机详细信息页面并且验证页面的头部是“Nexus S”。

**test/e2e/scenarios.js**

    ...
      describe('Phone detail view', function() {

        beforeEach(function() {
          browser().navigateTo('../../app/index.html#/phones/nexus-s');
        });


        it('should display nexus-s page', function() {
          expect(binding('phone.name')).toBe('Nexus S');
        });
      });
    ...

你现在可以刷新你的浏览器，然后重新跑一遍端到端测试，或者你可以在[AngularJS的服务器](http://angular.github.com/angular-phonecat/step-4/test/e2e/runner.html)上运行一下。

## 练习

使用[AngularJS端到端测试API][dev_guide.e2e-testing]写一个测试，用它来验证我们在Nexus S详细信息页面显示的四个缩略图。

## 总结
现在手机详细页面已经就绪了，在[步骤9][step_09]中我们将学习如何写一个显示过滤器。

[ng.$http]: http://code.angularjs.org/1.1.0/docs/api/ng.$http
[dev_guide.e2e-testing]: http://code.angularjs.org/1.1.0/docs/guide/dev_guide.e2e-testing
[step_09]: http://angularjs.cn/A00c
