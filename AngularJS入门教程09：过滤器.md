在这一步你将学习到如何创建自己的显示过滤器。

请重置工作目录：

    git checkout -f step-9

现在转到一个手机详细信息页面。在上一步，手机详细页面显示“true”或者“false”来说明某个手机是否具有特定的特性。现在我们使用一个定制的过滤器来把那些文本串图形化：√作为“true”；以及×作为“false”。来让我们看看过滤器代码长什么样子。

步骤8和步骤9之间最重要的不同在下面列出。你可以在[GitHub][]里看到完整的差别。

### 定制过滤器

为了创建一个新的过滤器，先创建一个`phonecatFilters`模块，并且将定制的过滤器注册给这个模块。

**app/js/filters.js**

    angular.module('phonecatFilters', []).filter('checkmark', function() {
      return function(input) {
        return input ? '\u2713' : '\u2718';
      };
    });

我们的过滤器命名为**checkmark**。它的输入要么是`true`，要么是`false`，并且我们返回两个表示true或false的unicode字符（`\u2713和\u2718`）。

现在我们的过滤器准备好了，我们需要将我们的`phonecatFilters`模块作为一个依赖注册到我们的主模块`phonecat`上。

**app/js/app/js**

    ...
    angular.module('phonecat', ['phonecatFilters']).
    ...

### 模板

由于我们的模板代码写在`app/js/filter.js`文件中，所以我们需要在布局模板中引入这个文件。

**app/index.html**

    ...
     <script src="js/controllers.js"></script>
     <script src="js/filters.js"></script>
    ...

在AngularJS模板中使用过滤器的语法是：

    {{ expression | filter }}

我们把过滤器应用到手机详细信息模板中：

**app/partials/phone-detail.html**

    ...
        <dl>
          <dt>Infrared</dt>
          <dd>{{phone.connectivity.infrared | checkmark}}</dd>
          <dt>GPS</dt>
          <dd>{{phone.connectivity.gps | checkmark}}</dd>
        </dl>
    ...

### 测试
过滤器和其他组件一样，应该被测试，并且这些测试实际上很容易完成。

**test/unit/filtersSpec.js**

    describe('filter', function() {

      beforeEach(module('phonecatFilters'));


      describe('checkmark', function() {

        it('should convert boolean values to unicode checkmark or cross',
            inject(function(checkmarkFilter) {
          expect(checkmarkFilter(true)).toBe('\u2713');
          expect(checkmarkFilter(false)).toBe('\u2718');
        }));
      });
    });

注意在执行任何过滤器测试之前，你需要为`phonecatFilters`模块配置我们的测试注入器。

执行`./scripts/test/sh`运行测试，你应该会看到如下的输出：

    Chrome: Runner reset.
    ....
    Total 4 tests (Passed: 4; Fails: 0; Errors: 0) (3.00 ms)
      Chrome 19.0.1084.36 Mac OS: Run 4 tests (Passed: 4; Fails: 0; Errors 0) (3.00 ms)

## 练习

*    现在让我们来练习一下[AngularJS内置过滤器][ng.$filter]，在**index.html**中加入如下绑定：
  *  `{{ "lower cap string" | uppercase }}`
  *  `{{ {foo: "bar", baz: 23} | json }}`
  *  `{{ 1304375948024 | date }}`
  *  `{{ 1304375948024 | date:"MM/dd/yyyy @ h:mma" }}`

*    我们也可以用一个输入框来创建一个模型，并且将之与一个过滤后的绑定结合在一起。在**index.html**中加入如下代码：

        <input ng-model="userInput"> Uppercased: {{ userInput | uppercase }}

## 总结

现在你已经知道了如何编写和测试一个定制化插件，在[步骤10][step_10]中我们会学习如何用AngularJS继续丰富我们的手机详细信息页面。

[GitHub]: https://github.com/angular/angular-phonecat/compare/step-8...step-9
[ng.$filter]: http://code.angularjs.org/1.1.0/docs/api/ng.$filter
[step_10]: http://angularjs.cn/A00d
