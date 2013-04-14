在这一步，你会在手机详细信息页面让手机图片可以点击。

请重置工作目录：

    git checkout -f step-10

手机详细信息视图展示了一幅当前手机的大号图片，以及几个小一点的缩略图。如果用户点击缩略图就能把那张大的替换成自己那就更好了。现在我们来看看如何用AngularJS来实现它。

步骤9和步骤10之间最重要的不同在下面列出。你可以在[GitHub][]里看到完整的差别。

### 控制器

**app/js/controllers.js**

    ...
    function PhoneDetailCtrl($scope, $routeParams, $http) {
      $http.get('phones/' + $routeParams.phoneId + '.json').success(function(data) {
        $scope.phone = data;
        $scope.mainImageUrl = data.images[0];
      });

     $scope.setImage = function(imageUrl) {
        $scope.mainImageUrl = imageUrl;
      }
    }

    //PhoneDetailCtrl.$inject = ['$scope', '$routeParams', '$http'];

在`PhoneDetailCtrl`控制器中，我们创建了`mainImageUrl`模型属性，并且把它的默认值设为第一个手机图片的URL。

### 模板

**app/partials/phone-detail.html**

    <img ng-src="{{mainImageUrl}}" class="phone">

    ...

    <ul class="phone-thumbs">
      <li ng-repeat="img in phone.images">
        <img ng-src="{{img}}" ng-click="setImage(img)">
      </li>
    </ul>
    ...

我们把大图片的`ngSrc`指令绑定到`mainImageUrl`属性上。

同时我们注册一个[ngClick][ng.directive:ngClick]处理器到缩略图上。当一个用户点击缩略图的任意一个时，这个处理器会使用`setImage`事件处理函数来把`mainImageUrl`属性设置成选定缩略图的URL。

### 测试

为了验证这个新特性，我们添加了两个端到端测试。一个验证主图片被默认设置成第一个手机图片。第二个测试点击几个缩略图并且验证主图片随之合理的变化。

**test/e2e/scenarios.js**

    ...
      describe('Phone detail view', function() {

    ...

        it('should display the first phone image as the main phone image', function() {
          expect(element('img.phone').attr('src')).toBe('img/phones/nexus-s.0.jpg');
        });

        it('should swap main image if a thumbnail image is clicked on', function() {
          element('.phone-thumbs li:nth-child(3) img').click();
          expect(element('img.phone').attr('src')).toBe('img/phones/nexus-s.2.jpg');

          element('.phone-thumbs li:nth-child(1) img').click();
          expect(element('img.phone').attr('src')).toBe('img/phones/nexus-s.0.jpg');
        });
      });
    });

你现在可以刷新你的浏览器，然后重新跑一遍端到端测试，或者你可以在[AngularJS的服务器](http://angular.github.com/angular-phonecat/step-4/test/e2e/runner.html)上运行一下。

## 练习

为`PhoneDetailCtrl`添加一个新的控制器方法：

        $scope.hello = function(name) {
            alert('Hello ' + (name || 'world') + '!');
        }

并且添加：

        <button ng-click="hello('Elmo')">Hello</button>

   到**phone-details.html**模板。

## 总结

现在图片浏览器已经做好了，我们已经为[步骤11][step_11]（最后一步啦！）做好了准备，我们会学习用一种更加优雅的方式来获取数据。

[GitHub]: https://github.com/angular/angular-phonecat/compare/step-9...step-10
[ng.directive:ngClick]: http://code.angularjs.org/1.1.0/docs/api/ng.directive:ngClick
[step_11]: http://angularjs.cn/A00e
