这一步，你会为手机列表的手机添加缩略图以及一些链接，不过这些链接还不会起作用。接下来你会使用这些链接来分类显示手机的额外信息。

请重置工作目录：

    git checkout -f step-6

现在你应该能够看到列表里面手机的图片和链接了。

步骤5和步骤6之间最重要的不同在下面列出。你可以在[GitHub][]里看到完整的差别。

### 数据

注意到现在`phones.json`文件包含了唯一标识符和每一部手机的图像链接。这些url现在指向`app/img/phones/`目录。

**app/phones/phones.json**（样例片段）

    [
     {
      ...
      "id": "motorola-defy-with-motoblur",
      "imageUrl": "img/phones/motorola-defy-with-motoblur.0.jpg",
      "name": "Motorola DEFY\u2122 with MOTOBLUR\u2122",
      ...
     },
    ...
    ]

### 模板

**app/index.html**

    ...
            <ul class="phones">
              <li ng-repeat="phone in phones | filter:query | orderBy:orderProp" class="thumbnail">
                <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
                <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
                <p>{{phone.snippet}}</p>
              </li>
            </ul>
    ...

这些链接将来会指向每一部电话的详细信息页。不过现在为了产生这些链接，我们在`href`属性里面使用我们早已熟悉的双括号数据绑定。在步骤2，我们添加了`{{phone.name}}`绑定作为元素内容。在这一步，我们在元素属性中使用`{{phone.id}}`绑定。

我们同样为每条记录添加手机图片，只需要使用[ngSrc][ng.directive:ngSrc]指令代替`<img>`的`src`属性标签就可以了。如果我们仅仅用一个正常`src`属性来进行绑定（`<img class="diagram" src="{{phone.imageUrl}}">`），浏览器会把AngularJS的`{{ 表达式 }}`标记直接进行字面解释，并且发起一个向非法url`http://localhost:8000/app/{{phone.imageUrl}}`的请求。因为浏览器载入页面时，同时也会请求载入图片，AngularJS在页面载入完毕时才开始编译——浏览器请求载入图片时`{{phone.imageUrl}}`还没得到编译！有了这个`ngSrc`指令会避免产生这种情况，使用`ngSrc`指令防止浏览器产生一个指向非法地址的请求。

### 测试

**test/e2e/scenarios.js**

    ...
        it('should render phone specific links', function() {
          input('query').enter('nexus');
          element('.phones li a').click();
          expect(browser().location().url()).toBe('/phones/nexus-s');
        });
    ...

我们添加了一个新的端到端测试来验证应用为手机视图产生了正确的链接，上面就是我们的实现。

你现在可以刷新你的浏览器，并且用端到端测试器来观察测试的运行，或者你可以在[AngularJS服务器](http://angular.github.com/angular-phonecat/step-6/test/e2e/runner.html)上运行它们。

## 练习

将`ng-src`指令换成普通的`src`属性。用像Firebug，Chrome Web Inspector这样的工具，或者直接去看服务器的访问日志，你会发现你的应用向`/app/%7B%7Bphone.imageUrl%7D%7D`（或者`/app/{{phone.imageUrl}}`）发送了一个非法请求。

这个问题是由于浏览器会在遇到`img`标签的时候立刻向还未得到编译的URL地址发送一个请求，AngularJS只有在页面载入完毕后才开始编译表达式从而得到正确的图片URL地址。

## 总结

如今你已经添加了手机图片和链接，转到[步骤7][step_07]，我们将学习AngularJS的布局模板以及AngularJS是如何轻易地为应用提供多重视图。

[GitHub]: https://github.com/angular/angular-phonecat/compare/step-5...step-6
[ng.directive:ngSrc]: http://code.angularjs.org/1.1.0/docs/api/ng.directive:ngSrc
[step_07]: http://angularjs.cn/A00a
