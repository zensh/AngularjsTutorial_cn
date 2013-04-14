在这一步中，我们会改进我们APP获取数据的方式。

请重置工作目录：

    git checkout -f step-11

对我们应用所做的最后一个改进就是定义一个代表[RESTful][]客户端的定制服务。有了这个客户端我们可以用一种更简单的方式来发送XHR请求，而不用去关心更底层的[$http][ng.$http]服务（API、HTTP方法和URL）。

步骤9和步骤10之间最重要的不同在下面列出。你可以在[GitHub][]里看到完整的差别。

### 模板

定制的服务被定义在**app/js/services**，所以我们需要在布局模板中引入这个文件。另外，我们也要加载**angularjs-resource.js**这个文件，它包含了`ngResource`模块以及其中的`$resource`服务，我们一会就会用到它们：

**app/index.html**

    ...
      <script src="js/services.js"></script>
      <script src="lib/angular/angular-resource.js"></script>
    ...

### 服务

**app/js/services.js**

    angular.module('phonecatServices', ['ngResource']).
        factory('Phone', function($resource){
          return $resource('phones/:phoneId.json', {}, {
            query: {method:'GET', params:{phoneId:'phones'}, isArray:true}
          });
        });

我们使用模块API通过一个工厂方法注册了一个定制服务。我们传入服务的名字`Phone`和工厂函数。工厂函数和控制器构造函数差不多，它们都通过函数参数声明依赖服务。Phone服务声明了它依赖于`$resource`服务。

[$resource][ngResource.$resource]服务使得用短短的几行代码就可以创建一个[RESTful][]客户端。我们的应用使用这个客户端来代替底层的[$http][ng.$http]服务。

**app/js/app.js**

    ...
    angular.module('phonecat', ['phonecatFilters', 'phonecatServices']).
    ...

我们需要把`phonecatServices`添加到`phonecat`的依赖数组里。

### 控制器

通过重构掉底层的[$http][ng.$http]服务，把它放在一个新的服务`Phone`中，我们可以大大简化子控制器（`PhoneListCtrl`和`PhoneDetailCtrl`）。AngularJS的[$resource][ngResource.$resource]相比于`$http`更加适合于与RESTful数据源交互。而且现在我们更容易理解控制器这些代码在干什么了。

**app/js/controllers.js**

    ...

    function PhoneListCtrl($scope, Phone) {
      $scope.phones = Phone.query();
      $scope.orderProp = 'age';
    }

    //PhoneListCtrl.$inject = ['$scope', 'Phone'];



    function PhoneDetailCtrl($scope, $routeParams, Phone) {
      $scope.phone = Phone.get({phoneId: $routeParams.phoneId}, function(phone) {
        $scope.mainImageUrl = phone.images[0];
      });

      $scope.setImage = function(imageUrl) {
        $scope.mainImageUrl = imageUrl;
      }
    }

    //PhoneDetailCtrl.$inject = ['$scope', '$routeParams', 'Phone'];

注意到，在`PhoneListCtrl`里我们把：

    $http.get('phones/phones.json').success(function(data) {
      $scope.phones = data;
    });

换成了：

    $scope.phones = Phone.query();

我们通过这条简单的语句来查询所有的手机。

另一个非常需要注意的是，在上面的代码里面，当调用Phone服务的方法是我们并没有传递任何回调函数。尽管这看起来结果是同步返回的，其实根本就不是。被同步返回的是一个“future”——一个对象，当XHR相应返回的时候会填充进数据。鉴于AngularJS的数据绑定，我们可以使用future并且把它绑定到我们的模板上。然后，当数据到达时，我们的视图会自动更新。

有的时候，单单依赖future对象和数据绑定不足以满足我们的需求，所以在这些情况下，我们需要添加一个回调函数来处理服务器的响应。`PhoneDetailCtrl`控制器通过在一个回调函数中设置`mainImageUrl`就是一个解释。

### 测试

修改我们的单元测试来验证我们新的服务会发起HTTP请求并且按照预期地处理它们。测试同时也检查了我们的控制器是否与服务正确协作。

[$resource][ngResource.$resource]服务通过添加更新和删除资源的方法来增强响应得到的对象。如果我们打算使用`toEqual`匹配器，我们的测试会失败，因为测试值并不会和响应完全等同。为了解决这个问题，我们需要使用一个最近定义的`toEqualData`[Jasmine匹配器][]。当`toEqualData`匹配器比较两个对象的时候，它只考虑对象的属性而忽略掉所有的方法。

**test/unit/controllersSpec.js：**

    describe('PhoneCat controllers', function() {

      beforeEach(function(){
        this.addMatchers({
          toEqualData: function(expected) {
            return angular.equals(this.actual, expected);
          }
        });
      });

     beforeEach(module('phonecatServices'));

      describe('PhoneListCtrl', function(){
        var scope, ctrl, $httpBackend;

        beforeEach(inject(function(_$httpBackend_, $rootScope, $controller) {
          $httpBackend = _$httpBackend_;
          $httpBackend.expectGET('phones/phones.json').
              respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

          scope = $rootScope.$new();
          ctrl = $controller(PhoneListCtrl, {$scope: scope});
        }));

        it('should create "phones" model with 2 phones fetched from xhr', function() {
          expect(scope.phones).toEqual([]);
          $httpBackend.flush();

          expect(scope.phones).toEqualData(
              [{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
        });

        it('should set the default value of orderProp model', function() {
          expect(scope.orderProp).toBe('age');
        });
      });

      describe('PhoneDetailCtrl', function(){
        var scope, $httpBackend, ctrl,
            xyzPhoneData = function() {
              return {
                name: 'phone xyz',
                images: ['image/url1.png', 'image/url2.png']
              }
            };

        beforeEach(inject(function(_$httpBackend_, $rootScope, $routeParams, $controller) {
          $httpBackend = _$httpBackend_;
          $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData());

          $routeParams.phoneId = 'xyz';
          scope = $rootScope.$new();
          ctrl = $controller(PhoneDetailCtrl, {$scope: scope});
        }));

        it('should fetch phone detail', function() {
          expect(scope.phone).toEqualData({});
          $httpBackend.flush();

          expect(scope.phone).toEqualData(xyzPhoneData());
        });
      });
    });

执行`./scripts/test.sh`运行测试，你应该会看到如下的输出：

    Chrome: Runner reset.
    ....
    Total 4 tests (Passed: 4; Fails: 0; Errors: 0) (3.00 ms)
      Chrome 19.0.1084.36 Mac OS: Run 4 tests (Passed: 4; Fails: 0; Errors 0) (3.00 ms)

## 总结

完工！你在相当短的时间内已经创建了一个Web应用。在[完结篇][the_end]里面我们会提起接下来应该干什么。

[RESTful]: http://en.wikipedia.org/wiki/Representational_State_Transfer
[ng.$http]: http://code.angularjs.org/1.1.0/docs/api/ng.$http
[GitHub]: https://github.com/angular/angular-phonecat/compare/step-10...step-11
[ngResource.$resource]: http://code.angularjs.org/1.1.0/docs/api/ngResource.$resource
[Jasmine匹配器]: http://pivotal.github.com/jasmine/jsdoc/symbols/jasmine.Matchers.html
[the_end]: http://angularjs.cn/A00f
