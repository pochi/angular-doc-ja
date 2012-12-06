注意書き
----------------

* 公式ではありません
* 意訳も多いので詳しくは[本家](http://docs.angularjs.org/guide/)で確認してください


目次
----------------

* [What is Angular?](#what_is_angular)
* [Overview](#overview)
* [Bootstrap]
* [HTML Compiler]
* [Conceptual Overview]
* [Directives]
* [Expressions]
* [Forms]
* [i18n and i10n]
* [Internet Explorer Compatibility]
* [Introduction]
* [Modules]
* [Scopes]
* [Type]
* [Dependency Injection]
* [About MVC in Angular]
* [Understanding the Model Component]
* [Understanding the Controller Component]
* [Understanding the View Component]
* [E2E Testing]
* [Understanding Angular Templates]
* [Working With CSS in Angular]
* [Data Binding in Angular]
* [Understanding Angular Filters]
* [Creating Angular Filters]
* [Using Angular Filters]
* [Angular Services]
* [Using $location]
* [Creating Services]
* [Injecting Services into Controllers]
* [Managing Service Dependencies]
* [Test Angular Services]
* [Understanding Angular Services]
* [Unit Testing]


URL
===

http://docs.angularjs.org/guide/overview

<a=name"what_is_angular">What is Angular?
----------------

AngularJSは動的Webアプリのためのフレームワークです。HTML言語をベースにしており
HTML文法を拡張して記述することでアプリを簡潔に表現することができます。
また、多くのData binding及びDIのコードが大幅に削減することが可能になります。そしてこれらは
全てブラウザ内のJavaScriptで実行されるためサーバ側に依存せず利用することが可能です。

AngularJSはHTMLによって表現されるアプリのために設計されています。HTMLは動的ドキュメントのための
すばらしい言語です。しかしアプリを作るための機能の多くは含まれておらず結局Webアプリのブラウザで何をしたいかに
ついては記述する必要があります。

動的なアプリと動的ドキュメントには以下のような違いがあります

- ライブラリ - Webアプリを記述するため関数群です。コードが変わるとライブラリを呼び出します。（jQueryなど）
- フレームワーク - Webアプリの細かい部分を担当してくれるフレームワークです。knockoutやsroutcoreなどはアプリ開発を助けます。

Angularは別のアプローチをとっています。HTMLに新しい構造を与えドキュメントとアプリの差分を埋めようとする最小限の手段です。
Angularはブラウザにdirectivesと呼ばれる新しい構造を教えます。例えば以下のものが含まれます。

- Data bindingは{{}}を利用する
- DOMの制御はrepeating/hidingを指定したDOMフラグメントを使う
- formとformの検証をサポートする
- DOM要素のコードに追記します
- 再利用可能なコンポーネントの中でHTMLのグルーピングをします

End-to-end solution
-------------------

URL
===

http://docs.angularjs.org/guide/concepts

<a name="overview">Overview
---

このドキュメントはAngularがどのように動いているか素早く知るためのものです。以下のようなコンポーネントがあります。

- startup - hello worldを起動します
- runtime - angular runtimeの概要です
- scope - viewとcontrollerの間の接着剤
- controller - アプリケーションの振る舞い
- model - アプリケーションのデータ
- view - ユーザに何を見せるか
- directives - HTML拡張
- filters - ユーザ内のデータフォーマット
- injector - アプリケーションの組み立て
- module - injectorの設定
- $ - angularの名前空間

Startup
---

これは下の図の動きを示しています。

![start](http://docs.angularjs.org/img/guide/concepts-startup.png)

1. ブラウザはHTMLを読み込みその中のDOMを解析します
2. ブラウザはangular.jsを読み込みます
3. AngularはDOMContentLoaded eventを待ちます
4. Angularはアプリケーションのデザインをするng-app directiveを探します
5. ng-appで指定されたModuleが設定された$injectorを利用します
6. $injectorは$compileサービスを$rootScopeサービスと同じように作成しまs
7. $compile serviceはDOMをコンパイルし$rootScopeにリンクします。
8. ng-init directiveにscopeを設定します
9. {{name}}にHello World!が表示されます

ソースコード

```
<!doctype html>
<html ng-app>
  <head>
    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
  </head>
  <body>
    <p ng-init=" name='World' ">Hello {{name}}!</p>
  </body>
</html>
```

Runtime
---

下の図はどのようにAngularがイベントループしているかを示しています。

![diagram](http://docs.angularjs.org/img/guide/concepts-runtime.png)

1. ブラウザはイベントが到着するまでループして待ちます。イベントはユーザの動的処理、時間処理ないしネットワークイベントです
2. イベントコールバックが来たら実行します。これはJavaScriptの文脈をさします。コールバックはDOM構造を修正します。
3. 一度コールバックの実行が終わるとブラウザはJavaScriptの文脈から離れDOMの変更を元に再描画を行います

Angularは自分自身で作るイベントループを修正しています。
これはJavaScriptのクラスとAngularの実行文を分けることができます。もしAngularの実行文脈を利用することでデータバインディング、
例外ハンドリング、プロパティの監視等の利益を受けることができます。
$apply()を利用することでJavaScriptからAngular文を呼ぶことができます。
だいたいの場所(controllers, services)では$applyはイベントをハンドリングするdirectiveによって呼ばれていることを覚えておいてください。
$applyを呼ぶことが必要である場合カスタムイベントコールバックもしくはサードパーティコールバックの時のみ保存されます。

- scope.$apply(stimulusFn)を呼ぶことでAngularが実行されます。あなたはAngularを実行したい場所でstimulusFnを呼ぶ必要があります。
- Angularは一般的にアプリケーションの状態が変わったタイミングでstimulusFn()を呼び出します
- Angularは$digestループに入ります。このループは二つの小さいループを呼び出します。一つは$evalAyncキュー、もう一つは$watchリストです。
$digestループはmodelが変更発生するまで呼び出します。それは$evalAsyncキューになにもなく$watchリストに何も変化がない状態をさします。
- $evalAsyncキューは現在のスタックフレームをみてスケジューリングをしますがそれはブラウザのviewがレンダリングする前です。これは基本的にsetTimeout(0)で起動しますが
setTimeout(0)だと遅延やイベント後のフリックが起きてしまう影響があります。
- $watchリストは最後のイテレーションが終わってから変更されたかどうかの確認をします。もし変更を見つけた場合#watch関数はDOMに更新された値を渡します。
- 一度$digestループが終了するとAngularとJavaScript文脈から抜けます。これはブラウザが再描画しDOMを変更するためです。

ここでどのようにHelo wordの例がデータバインディングをどのように変更しているのか確認しましょう。

- コンパイルフェーズ
  - ng-modeとinput directiveがinputタグのkeydownリスナーをセットします
  - {{name}}に変更が入ると$watchがそれに気づいて名前を変更する
- runtimeフェーズ
  - 'X'を入力するとkeydownリスナーが発火される
  - input directiveが入力値の変更をとらえて$apply("name='X';")を呼び出す。それを受けてアプリケーションのmodelの中で実行が走る
  - Angularがnam='X';をmodel内で受ける
  - $digestループが始まります
  - $watchリストはname属性の変更を受けて{{name}}変更に気づいて、DOMの変更を行います
  - Angularは実行文脈から抜けJavaScriptの実行コンテキスト（keydown)からも抜けます
  - ブラウザは更新されたテキストを再描画します

ソースコード

```
<!doctype html>
<html ng-app>
  <head>
    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
  </head>
  <body>
    <input ng-model="name">
    <p>Hello {{name}}!</p>
  </body>
</html>
```

Scope
---

scopeはmodelから変更を受けAngular実行構文を与えます。スコープはDOM構造に似た階層構造にネストされる。
(directiveごとにscopeの種類が違うのでその辺は各ドキュメントを参照してください)

以下のデモではname属性がscpeに依存してどのように評価されるかを示します。
scopeの依存関係を示したものが以下の例になります。

```
<!doctype html>
<html ng-app>
  <head>
    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div ng-controller="GreetCtrl">
      Hello {{name}}!
    </div>
    <div ng-controller="ListCtrl">
      <ol>
        <li ng-repeat="name in names">{{name}}</li>
      </ol>
    </div>
  </body>
</html>
```

![hoge](http://docs.angularjs.org/img/guide/concepts-scope.png)

Controller
---

![controller](http://docs.angularjs.org/img/guide/concepts-controller.png)

controllerはViewの後ろにあるコードです。modelを構造化しViewにcallbackメソッド一緒に渡します。
viewはtemplateとscopeを移したものになります。
scopeはcontrollerから受けたeventを進めてviewとmodelの部品をくっつけます。

controllerとviewが別々であるのが重要な理由は次のとおりです。

- controllerはJavaScriptで書かれています。JavaScriptはさけられないですがアプリケーションの振る舞いにフィットしています。
なのでcontrollerにレンダリング情報を入れるべきではありません。
- view templateはHTMLで書かれています。HTMLは宣言的です。宣言することはUIにフィットしています。なのでViewに振る舞いを書いてはいけません。
- controllerがviewを意識しないようになると同じcontrollerでたくさんのviewが扱えるようになります。これはテストしやすく重要です。

ソースコード
---

```
<!doctype html>
<html ng-app>
  <head>
    <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div ng-controller="MyCtrl">
      Hello {{name}}!
      <button ng-click="action()">
        OK
      </button>
    </div>
  </body>
</html>

function MyCtrl($scope) {
  $scope.action = function() {
    $scope.name = 'OK';
  }
 
  $scope.name = 'World';
}
```




URL
===

http://docs.angularjs.org/guide/dev_guide.mvc

AngularはクライアントアプリケーションとしてオリジナルMVCの原則を組み込んでいます。

MVCパターンのすばらしいところは以下の点があります。

- アプリケーションをプレゼンテーション層、データ、ロジックコンポーネントに明確に分けることが可能。
- これらのコンポーネント間の疎結合を実現できます

servicesとdependency injectionにそってMVCはangularアプリケーションの構造化、メンテナンス性、テストのしやすさを
実現しています。

以下のトピックでangularがMVCで取り入れでWebアプリケーションを開発するための方法を記述しています。

- モデルコンポーネントの理解
- コントローラコンポーネントの理解
- Viewコンポーネントの理解

URL
===

http://docs.angularjs.org/guide/dev_guide.mvc.understanding_model

angularドキュメントのdiscussionの文脈によるとmodelは一つのエンティティもしくは空のデータモデルを表現します。
(例えば"phones"と呼ばれるモデルはphonesの配列値をもっています)

angularではmodelはいかなるデータもangular Scope objectとしてアクセスすることができます。
プロパティの名前はmodelごとにもち値はどんなJavaScriptオブジェクトでも持つことが可能です。

angularのモデルでJavaScriptオブジェクトに必要とされることはscopeオブジェクトのプロパティとしてangularのscope
オブジェクトから参照できることです。このプロパティは明示的にも参照されるし、さりげなく参照することも可能です。

以下の方法でJavaScriptオブジェクトへの参照を明示的に行うことが可能です。

- JavaScriptコード内にScopeオブジェクトを利用します。これは主にcontroller内で利用されます

```
 function MyCtrl($scope) {
     // create property 'foo' on the MyCtrl's scope
     // and assign it an initial value 'bar'
     $scope.foo = 'bar';
 }
```

- angular文法を利用してtemplates内に埋め込むことができます。

```
<button ng-click="{{foos='ball'}}">Click me</button>
```

- ngInit directiveを利用することもできます（非推奨）

```
<body ng-init=" foo = 'bar' ">
```

Angularは以下のように暗黙的にモデルを作ることも可能です。

- Form input, select, textareaや他のform要素

```
<input ng-model="query" value="fluffy cloud">
```

このコードで現在のscope上に"query"と呼ばれるモデルがつくられ、"fluffy cloud"と値が設定されます。

- ngRepeaterのイテレートで利用できます

```
 <p ng-repeat="phone in phones"></p>
```

このコードでは"phones"の中の要素のために一つのchild scopeが作成され配列の値がセットされた
"phone"オブジェクト(model)がそれぞれ作成されます。

angularがJavaScriptオブジェクトをモデルとしてみなさない時：

- オブジェクトがangularのスコープから参照できないとき
- GCで対象となったAngularオブジェクト

以下に簡単なデータモデルとテンプレートについて示します。

![sample](http://docs.angularjs.org/img/guide/about_model_final.png)

URL
===

http://docs.angularjs.org/guide/dev_guide.mvc.understanding_controller

angularではcontrollerはangular Scopeのインスタンスを引数にもった(root scopeは除外する)JavaScript関数（クラス）です。
あなたがscope.$new APIを利用してchild scopeを作成したときcontrollerにはmethod引数にchild scopeを渡します。
これはangularにcontrollerが新しいscopeと関連があるんだよということを伝えています。

controllerは以下の用途で利用されます。

- scopeオブジェクトの初期状態をセットアップ
- scopeオブジェクトに振る舞いを追加する

Setting up the initial state of a scope object
---------------------

一般的にアプリが作られたときangular scopeを初期設定する必要があります。

Angularは新しいscope objectに対してcontrollerのコンストラクタを呼び出します。
これはangularが決してcontrollerのインスタンスを作らないことを意味します。
コンストラクタは常に存在しているscopeを呼び出します。

モデルプロパティに設定されたscopeの初期設定を行ってみましょう。例えば:

```javascript
function GreetingCtrl($scope) { $scope.greeting = 'Hola!'; }
```

GreetingCtrlはtemplate内で参照されるgreetingモデルを作っています。

Adding Behavior to a Scope Object
---

angular scope objectの振る舞いはtemplate/viewのscopeが指定されたform内で利用できます。
これはこれはアプリケーションmodelが修正されたときもインタラクティブに動作します。

このガイドのmodel部分で話したようにどのオブジェクトで定義したscopeも全てはmodelのプロパティになります。
scopeに設定された関数は全てtemplate/view内で利用可能です。そしてそれはangular文法ないしng event handler
によって呼び出されます。

Using Controllers Correctly
---

一般的にcontrollerは機能を多くしすぎないように工夫すべきです。一つのviewに対して必要なビジネスロジックのみ定義すべきです。

controllerをスリムに保つ最も効果的な方法はcontrollerが所有すべきでないサービスをカプセル化することです。
そしてそのサービスはdependency injectionによって使うことになります。
これは"Dependency Injection Services"の中で説明されています。

以下のようなケースではcontrollerは利用されるべきではありません。

- DOMの操作 - controllerで記述すべきはbusiness logicのみです。DOM操作、すなわちアプリケーションのpresentation logicは
テストするのが難しいことが知られています。controller内にpresentation logicを持ち込むとテストに影響がでてしまいます。
Angularはdev_guide.templates.databindingで自動的なDOM操作を提供しています。
もしCOM操作を自分で書きたいのならdirectivesの中のpresentation logicにまとめるべきです。

- Input format - angular form controlsを代わりに利用してください
- Output filtering - angular filtersを代わりに利用してください
- 複数controllerをまたいだ処理 - angular servicesを代わりに利用してください
- コンポーネントのライフスタイル管理に使わないでください(serviceのインスタンス生成など)

Associating Controllers with Angular Scope Objects
---

scopeオブジェクトとの関連を明示的につけたい場合はscope.$new APIを利用します。間接的に利用する場合はngController directiveか
$route serviceを利用します。

Controller constructor and Methods Example
---

angular内でcontrollerがどのように動いているか確認するため以下のコンポーネントでアプリケーション

- ２つのボタンと一つのメッセージがついたtemplate
- spiceという名前のmodel
- spiceの値をセットした二つの関数をもったcontroller

template内のメッセージはspice modelにバインドされ、基本は"very"をセットします。
ボタンをクリックしたときにspice modelのメッセージは"chili"もしくは"jalapeno"に変更されそれはdata bindingによって
動的に変更されます。

A Spicy Controller Example
---


```
<body ng-controller="SpicyCtrl">
 <button ng-click="chiliSpicy()">Chili</button>
 <button ng-click="jalapenoSpicy()">Jalapeño</button>
 <p>The food is {{spice}} spicy!</p>
</body>
 
function SpicyCtrl($scope) {
 $scope.spice = 'very';
 $scope.chiliSpicy = function() {
   $scope.spice = 'chili';
 }
 $scope.jalapenoSpicy = function() {
  $scope.spice = 'jalapeño';
 }
}
```

注目してほしいのはSpicyCtrl controllerがspicyと呼ばれるメソッドのみを定義している点です。
templateは最初のボタンが押されるとcontrollerのメソッドを通して'chili'に変更され
二つ目のボタンではinput boxに依存するようになっています。

Controller Inheritance Example
---

controllerの以上はScopeの以上によって行われます。例をみてみましょう。

```
<body ng-controller="MainCtrl">
 <p>Good {{timeOfDay}}, {{name}}!</p>
 <div ng-controller="ChildCtrl">
   <p>Good {{timeOfDay}}, {{name}}!</p>
   <p ng-controller="BabyCtrl">Good {{timeOfDay}}, {{name}}!</p>
</body>
 
function MainCtrl($scope) {
 $scope.timeOfDay = 'morning';
 $scope.name = 'Nikki';
}
 
function ChildCtrl($scope) {
 $scope.name = 'Mattie';
}
 
function BabyCtrl($scope) {
 $scope.timeOfDay = 'evening';
 $scope.name = 'Gingerbreak Baby';
}
```


今度はtemplate内に３つngControllerをネストさせてみました。このViewは4つのscopeが作成されることになります。

- root scope
- timeOfDayとnameのmodelを持ったMainCtrl scope
- name modelのコピーとtimeOfDaymodelを継承したChildCtrl
- MainCtrlのtimeOfDay modelとChildCtrlのnname modelを利用したBabyCtrl scope

controller間はmodelと同様の働きをします。
なので前の例では全てのmodelは返却値を持ったcontroller関数に置き換えることができます。

Note:これは標準としてこうしたいという想いがあり実質うまく動くか確実ではありません。
ただscopeオブジェクトにはほぼ適用しているといって問題ありません。

Testing Controllers
---

controllerのテストの例として$rootScopeと$controllerのテストを以下に記述します。

Controller function:

```
function myController($scope) {
   $scope.spices = [{"name":"pasilla", "spiciness":"mild"},
                  {"name":"jalapeno", "spiceiness":"hot hot hot!"},
                  {"name":"habanero", "spiceness":"LAVA HOT!!"}];
 
   $scope.spice = "habanero";
}
```

Controller test:
```
describe('myController function', function() {
 
  describe('myController', function() {
    var scope;
 
    beforeEach(inject(function($rootScope, $controller) {
      scope = $rootScope.$new();
      var ctrl = $controller(myController, {$scope: scope});
    }));
 
    it('should create "spices" model with 3 spices', function() {
      expect(scope.spices.length).toBe(3);
    });
 
    it('should set the default value of spice', function() {
      expect(scope.spice).toBe('habanero');
    });
  });
});
```

もしネストしたcontrollerのテストをしたい場合DOMの存在するテストを行う必要があります。

```
describe('state', function() {
    var mainScope, childScope, babyScope;
 
    beforeEach(inject(function($rootScope, $controller) {
        mainScope = $rootScope.$new();
        var mainCtrl = $controller(MainCtrl, {$scope: mainScope});
        childScope = mainScope.$new();
        var childCtrl = $controller(ChildCtrl, {$scope: childScope});
        babyScope = childCtrl.$new();
        var babyCtrl = $controller(BabyCtrl, {$scope: babyScope});
    }));
 
    it('should have over and selected', function() {
        expect(mainScope.timeOfDay).toBe('morning');
        expect(mainScope.name).toBe('Nikki');
        expect(childScope.timeOfDay).toBe('morning');
        expect(childScope.name).toBe('Mattie');
        expect(babyScope.timeOfDay).toBe('evening');
        expect(babyScope.name).toBe('Gingerbreak Baby');
    });
});
```

URL
===

http://docs.angularjs.org/guide/dev_guide.mvc.understanding_view

angularではviewはtemplate, controller, modelの情報をベースにDOMに変換し
ブラウザ内でDOMをロードしレンダリングします。

![architecture](http://docs.angularjs.org/img/guide/about_view_final.png)

angularのMVC実装ではviewはcontrollerとmodel両方の知識を持っています。
viewはmodelからどこと双方向通信すべきなのかを知ります。viewはangular directive(ngControllerやngView)から
controllerの知識を知ります。

そしてフォームに{{someControllerFunction()}}でバインドします。viewではcontrollerで定義した関数を呼び出すことができます。

URL
===

http://docs.angularjs.org/guide/di

Dependncy Injection
---

DIは依存関係を記述するためのソフトウェアデザインパターンです。

DIに関する議論はWikipediaやMartin Fowler氏によるInversion of Controlもしくはあなたの気に入っている本で参照してみてください。

DI in a notshell
---

オブジェクトもしくは関数から依存関係を表すには以下の３つの方法があります。

- 新しい命令を利用して依存関係を作る
- グローバル変数を参照して依存関係を探す
- 依存関係が必要なところに注入することができる

最初の二つの方法はあまり望ましくありません。なぜならそれはハードコードしてしまうと作るのも難しいし直すのも簡単ではありません。
これは特にテストで問題となり粗結合テストをするための問題となり得ます。

３つ目の手段はコンポーネントからの依存性責任を除外できるため最も利用しやすいものです。
依存関係はコンポーネントに簡単に追加できます。

```
function SomeClass(greeter) {
  this.greeter = greeter
}
 
SomeClass.prototype.doSomething = function(name) {
  this.greeter.greet(name);
}
```

上の例ではSomeClassはgreeterの依存に興味を持ちません。実行時にgreeterによって依存されます。

これは好ましいですがSomeClassの構造上依存関係を記述しているコードに責任が生じてしまいます。
依存関係の責任を管理するためangularアプリケーションはinjectorを持っています。
injectorは依存関係を探す構造に責任をもったserviceなのです。

ここにinjector serviceを利用した例を記述します。

```
// Provide the wiring information in a module
angular.module('myModule', []).
 
  // Teach the injector how to build a 'greeter'
  // Notice that greeter itself is dependent on '$window'
  factory('greeter', function($window) {
    // This is a factory function, and is responsible for 
    // creating the 'greet' service.
    return {
      greet: function(text) {
        $window.alert(text);
      }
    };
  }).
  
// New injector is created from the module. 
// (This is usually done automatically by angular bootstrap)
var injector = angular.injector('myModule');
 
// Request any dependency from the injector
var greeter = injector.get('greeter');
```

依存関係をたずねることでハードコードする問題は解決しましたがinjectorはアプリケーションを通過させる必要があることも意味します。
injectorを通すことでデメテルの法則が崩れてしまう。これを改善するため下のような書き方をすることでinjectorが依存関係見つける
責任を変更した。

```
<!-- Given this HTML -->
<div ng-controller="MyController">
  <button ng-click="sayHello()">Hello</button>
</div>

// And this controller definition
function MyController($scope, greeter) {
  $scope.sayHello = function() {
    greeter('Hello World');
  };
}
 
// The 'ng-controller' directive does this behind the scenes
injector.instantiate(MyController);
```

ng-controllerがclassをインスタンス化するところに注目してほしい。
それはinjectorについて知っているcontrollerじゃなくても安全にMControllerの依存関係をコーディングできる。
これが一番の成果である。アプリケーションのコードはinjectorをどのように取り扱うのか知る必要がなく
簡単に依存関係が必要か聞くことができる。
このセットアップの方法であればデメテルの法則を壊すことはない。

Dependency Annotation
---

injectorはどのようにしてサービスが何をinjectするべきか知るのだろうか。
アプリケーション開発者は依存関係を解決するためのアノテーション情報を与えることが必要である。
考え抜いた結果Angular API関数はAPI ドキュメントごとにinjectorによって呼び出される。
injectorは関数内でサービスにinjectするものを知る必要がある。
以下の３つの方法でservice情報をコードからアノテーションを用いて知ることができる。
それらはあなたのコードでフィットするものを使うことができる。


Inferring Dependencies
---

依存関係を解決する最も簡単な方法は関数の引数に依存する名前を書いてしまうことである。

```
function MyController($scope, greeter) {
  ...
}
```

innjectorを関数に与えることでパラメータより注入すべきservice名を知ることができます。
上記の例では$scopeとgreeterの二つのサービスが関数内に注入することが必要ということになります。

これを進めていくとメソッド名の変更によりJavaSciprtが動かなくなるでしょう。
これらをpretotypingやデモアプリで必要なレベルです。

$inject Annotation
---

関数の引数名変更を許容し関数に正しいserviceを注入するために$injectプロパティを利用したアノテーとを使います。
この$injectプロパティは注入するサービスの配列になります。

```
var MyController = function(renamed$scope, renamedGreeter) {
  ...
}
MyController.$inject = ['$scope', 'greeter'];
```

$injectorアノテーションが関数を記述中に呼び出す引数と同期するよう十分注意してください。
宣言としてかけるためcontrollerの宣言には役立つ書き方です。

Inline Annotation
---

$injectアノテーションを利用する何回かはannotation directiveが適任ではないときがあります。

例えば

```
someModule.factory('greeter', function($window) {
  ...;
});
```

結果として一時変数が必要になってしまいます。

```
var greeterFactory = function(renamed$window) {
  ...;
};
 
greeterFactory.$inject = ['$window'];
 
someModule.factory('greeter', greeterFactory);
```

これを防ぐため以下のような書き方をすることができます。

```
someModule.factory('greeter', ['$window', function(renamed$window) {
  ...;
}]);
```

上記全てのコーディングスタイルがAngularがサポートしていることを覚えておいてください。

Where can I use DI?
---

DIはAngular経由で利用されます。それは一般的にcontrollerとfactoryメソッドで利用されます。

```
var MyController = function(dep1, dep2) {
  ...
}
MyController.$inject = ['dep1', 'dep2'];
 
MyController.prototype.aMethod = function() {
  ...
}
```

Factory methods
---

factoryメソッドはangular内でオブジェクトを作る時の責任を持ちます。
例えばservices,filtersなどのdirectiveです。
factoryメソッドはmoduleによって登録されます。推奨する書き方は以下になります。

```
angualar.module('myModule', []).
  config(['depProvider', function(depProvider){
    ...
  }]).
  factory('serviceId', ['depService', function(depService) {
    ...
  }]).
  directive('directiveName', ['depService', function(depService) {
    ...
  }]).
  filter('filterName', ['depService', function(depService) {
    ...
  }]).
  run(['depService', function(depService) {
    ...
  }]);
```


*** URL

http://docs.angularjs.org/tutorial/step_03

*** Filterling Repeaters

今まで基礎の部分を勉強してきました。次は簡単な全文検索を作ってみましょう。
ent-to-endのテストも書くことで見通しのよいアプリケーションを維持できます。

今アプリケーションは検索boxを持っています。検索boxの入力に応じて検索結果が変わっているのに注目してください。
最も重要な変更を以下に記載します。

*** Controller

変更ありません。

*** Template

app/index.html

```
<div class="container-fluid">
  <div class="row-fluid">
    <div class="span2">
      <!--Sidebar content-->
 
      Search: <input ng-model="query">
 
    </div>
    <div class="span10">
      <!--Body content-->
 
      <ul class="phones">
        <li ng-repeat="phone in phones | filter:query">
          {{phone.name}}
          <p>{{phone.snippet}}</p>
        </li>
      </ul>
 
    </div>
  </div>
</div>
```

ここで私たちはHTMLのinputタグを追加しngRepeatの途中に$filterを挟み込みました。
これでユーザが入力すると同時に検索することが可能になります。
このコードが内部で何をしているのか以下に説明します。

- Data-binding: これはAngularのコア機能の一つです。ページを読み込んだときAngularはinput boxの名前とデータモデルの名前が
同じものをバインドし同期するようにします。

今回のコードで考えるとユーザがタイプするinputフィールド（queryと呼ばれている）は直ちにfilter inputとして利用することができます。
入力が変化したときモデルの現在の状況をDOMに反映するようにしています。

- filter関数について: filter関数はたくさんのレコードからqueryをかけてマッチしたものに対して新しい配列を作ります。

ngRepeatはフィルターによってphonesのviewを動的に更新します。
これを開発者に透過的に与えることができます。

--------------------------------------------------------------------

*** URL

http://docs.angularjs.org/tutorial/step_07

*** Multiple Views, Routing and Layout Template

アプリをさらにより成長させて複雑にしてみよう。
step 7の前にアプリは全て単一ページ内でかつそれらのコードはindex.htmlに記述されていた。
次のステップは全てのデヴァイスの情報を追加してみよう。

詳細な画面を追加するためにindex.htmlとtemplate両方に手を加えるが一気にやると混乱を招いてしまう。
まずindext.html templateを"layout template"と呼ぶことにします。
このテンプレートはアプリケーション共通のものです。
別の"Partial Template"を現在の"route"に依存した形で含めるようにしてみましょう。

AngularJSでは$routeProvider経由でrouteを宣言しまし、実質その機能を提供するのが$routeです。	
このサービスは二つのコントローラ、テンプレートを現在のURLに簡単に統合します。
これを実現するためにdeep linkingの機能を実装し、bookmarkや履歴に利用しています。

*** DI, Injector及びProviderについて

既に説明した通りdependency injectionはAngularJSのコア機能でありそれらがどう動いているのか知ることは重要です。
アプリケーションを立ち上げたときAngularはこのアプリケーションが利用する全てのDIで使われるInjectorを作ります。
Injector自身は$httpや$routeが何をするかは知りません。事実、モジュールが定義されなければ存在すら気づきません。
唯一のInjectorの責任はモジュールを読み込み、モジュールに定義された全てのサービルプロバイダを登録し、プロバイダ経由で
遅延した依存関数をinjectするのか確認することです。

プロバイダー及びオブジェクトはサービス及びサービスの振る舞いで作られてたAPIをもったインスタンスを提供します。
$root serviceにおいては$routeProviderがアプリケーションにrouteを定義しAPIを利用できるようにする。

Angularのモジュールは与えられたInjectorやアプリケーションからグローバルな設定を削除しない。
AMDやrequire.jsと対立してAngularは遅延ロードや順番にScriptを読み込むようなことは解決しない。
お互いが共存できればそれで対応できるからだ。

*** The App Module

app/js/app.js:

angular.module('phonecat', []).
  config(['$routeProvider', function($routeProvider) {
  $routeProvider.
      when('/phones', {templateUrl: 'partials/phone-list.html',   controller: PhoneListCtrl}).
      when('/phones/:phoneId', {templateUrl: 'partials/phone-detail.html', controller: PhoneDetailCtrl}).
      otherwise({redirectTo: '/phones'});
}]);

アプリケーションにrouteを設定するためにはモジュールを作成する必要がある。私足しはこのモジュールをphonecatAppと呼び、
$routeProvider APIを利用してinjectし$routeProvider.when APIでrouteを定義する。

injectorの設定フェーズにprovidersがinjectできてもserviceインスタンスが立ち上がるまでは有効ではないことに注意してほしい。

私たちのアプリケーションに以下のようなrouteを設定してみる。

 - phoneリストviewはURLが/phonesの時にみれるようにする。このViewを構成するためAngularではphone-list.htmlテンプレートとPhoneListCtrl controllerを作成する
 - 細かいphone情報は/phones/:phoneIdでみれるようにする。:phoneIdはURLの一部として利用可能である。このViewを構成するためangularはphone-detail.htmlテンプレートとPhoneDetailCtrl controllerを利用する

 PhoneListCtrlは先ほど使ったものを再利用しPhoneDetailCtrlは今回phoneの詳細を表示するために利用する。

 '$route.otherwise({redirectTo: '/phones'})'という文はブラウザのアドレスがマッチしなかった場合/phonesにリダイレクトする定義です。

注意してほしいのは二行目にある:phoneIdパラメータです。$route serviceは'/phones/:phoneId'を現在のURLにそってマッチするか確認するテンプレートとして利用します。
これらの全ての有効な変数は$routeParamsオブジェクトに書かれています。

新しいモジュールを起動するためngAppディレクティブに以下のように記載する必要があります。

app/index.html:

    <!doctype html>
    <html lang="en" ng-app="phonecat">
    ...
    Controllers
    app/js/controllers.js:
    
    ...
    function PhoneDetailCtrl($scope, $routeParams) {
      $scope.phoneId = $routeParams.phoneId;
    }
     
    //PhoneDetailCtrl.$inject = ['$scope', '$routeParams'];


*** Controllers
app/js/controllers.js:

    ...
    function PhoneDetailCtrl($scope, $routeParams) {
      $scope.phoneId = $routeParams.phoneId;
    }
     
    //PhoneDetailCtrl.$inject = ['$scope', '$routeParams'];

*** Template

$route serviceは一般的にngView内で利用されます。ngViewの役割はlayoutテンプレートの中に埋め込むことでindex.htmlテンプレートを完璧にすることです。

app/index.html:

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

------------------------------

注目してほしいのはindex.htmlの大半はng-viewをdivに置き換えたことである。
これらを削除したのはphone-list.html templateに置き換えるためである。

app/partials/phone-list.html:

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

--------------

さらにphoneの詳細viewを追加します。

app/partials/phone-detail.html:

TBD: detail view for {{phoneId}}
Note how we are using phoneId model defined in the PhoneDetailCtrl controller.

Test
To automatically verify that everything is wired properly, we wrote end-to-end tests that navigate to various URLs and verify that the correct view was rendered.

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

You can now rerun ./scripts/e2e-test.sh or refresh the browser tab with the end-to-end test runner to see the tests run, or you can see them running on Angular's server.


