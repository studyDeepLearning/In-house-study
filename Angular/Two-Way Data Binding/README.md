# 双方向データバインディング

[1. 双方向データバインディングって何？](#user-content-1-双方向データバインディングって何)  
[2. 従来の場合](#user-content-2-従来の場合)  
[3. Angular の場合](#user-content-3-angular-の場合)  
[4. Scope オブジェクトとは？](#user-content-4-scope-オブジェクトとは)  
[5. 実現方法](#user-content-5-実現方法)


## 1. 双方向データバインディングって何？

AngularJS<sup>[※1](#ref1)</sup> （以降はAngular）が持つ特徴的な機能の1つで、Javascript のオブジェクトが保持するデータと、HTML テンプレート（View）として画面に表示される内容を自動的に連携する機能です。

簡単に言えば、「画面での入力はJavascript 上の変数に自動的に反映される」、さらに「Javascript 上の変数が変化すると自動的に画面に反映される」といった動きをするので『**双方向**』と呼ばれているわけです。

<a name="ref1"></a>※1 AngularJS で双方向データバインディングを実現するために最も重要なScope オブジェクトですが、**Angular2 以降は廃止**されています。そもそも、Anguar2 では原則単方向バインディングを利用することになっています（双方向も使えないことはないが）。

## 2. 従来の場合

従来はJavascript のコードから直接DOMを操作し値の書き換えを行ったり、DOM 要素に対するイベントのハンドルが必要でした。ここで、DOM は何か？といった部分を説明すると主旨からだいぶ外れてしまうので割愛します。

イベントのハンドリングやDOM操作を素のJavascriptのみで実装するのは大きな労力が必要なため、「**JQuery**」のようなライブラリを用いるのが一般的です。

例えば、はじめは'Hello, World!'とテキストが表示され、「push」ボタンをクリックすると'Goodbye, Everyone!'に変化するようなプログラムをJQueryを用いて実装してみます。（body要素の記述などは省きます。）

```HTML
HTML:

<div>
 <p id="message"></p>
 <button id="goodbye">push</button>
</div>
```

```javascript
javascript：

$(function() {
    $('#message').text('Hello, World!');
    $('#goodbye').click(function() {
        $('#message').text('Goodbye, Everyone!');
    })
});
```

イベントを受けて、色々と処理を実行できるので、とても便利です。

しかしながら、HTMLに都度IDを振って、監視するための処理を記述してとなるととても手間で、複雑で大きな画面になるほど、その影響は大きくなります。

## 3. Angular の場合

天下り的になってしまいますが、とりあえず2節で記述したプログラムをAngularを用いて実装してみると以下のようになります。

```HTML
HTML:

<div ng-controller="NormalController">
    <p>{{message}}></p>
    <button ng-click="goodbye()">push</button>
</div>
```

```javascript
javascript：

angular.module('app', []).controller('NormalController',['$scope', function($scope) { // ここら辺は定型句みたいなものです
    $scope.message = 'Hello, World!';
    $scope.goodbye = function () {
        $scope.message = 'Goodbye, Everyone!';
    }
}]);
```

message は表示される文言の変数で、コントローラ側での変更がテンプレート側に即座に反映されます。またテンプレートのbutton要素に対してng-clickディレクティブ（ディレクティブについては別でまとめます）を用いて、クリックイベントを受けて呼び出される関数を記述しています。

テンプレート側にid を振ることもなく、コントローラ側では変数と関数の定義のみを行い、**イベントを監視するような記述、DOMを直接操作するような記述は一切ありません**。

この程度のDOM操作であればほとんど差がないように感じるかもしれませんが、より多くのイベントを監視して、処理を記述していくことを考えれば、Angularのインパクトの大きさは想像に難くないと思います。

※今回の例では、ng-controller を使ってテンプレートにコントローラの割り当てを行っていますが、通常?一般的に？よく見るの？はui-router を用いてルーティングの設定の一部としてコントローラの指定（ui-router の設定において、指定とはニュアンスが異なるのですが、一旦はこの表現のままで）を行います。（その場合、テンプレート側ではng-controller を用いてコントローラの指定は行いません。）  
これについては5節にて解説します。

## 4. Scope オブジェクトとは？

前節で『イベントを監視するような記述、DOMを直接操作するような記述は一切ありません』と言いましたが、ではいったいどのようにして双方向データバインディングを実現しているのでしょうか？

ここで鍵となってくるのが「**Scope**」と呼ばれるオブジェクトです。

Scope とはHTML テンプレート（View）とコントローラ（ないし、Model）<sup>[※2](#ref2)</sup>の橋渡しをし、**テンプレートに対して公開するデータや、テンプレートで発生したイベントに対するアクションを保持するオブジェクト**です。

Scope が保持するプロパティは**内容の変更がAngular によって監視されており**、プロパティの値が変化すると、その変更内容がDOM ないしはコントローラの変数に反映されます。また、DOM イベントが発生した場合は、**Scope で保持している関数が呼び出されます**。

ここまでの説明を3節のプログラムに当てはめて考えてみます。以下は、テンプレートとScope 、コントローラの関係性を表したイメージ図になります。

![scope_1](https://user-images.githubusercontent.com/28583094/48980626-ec3c2180-f10e-11e8-9fb8-55cc1de6a00a.png)

コントローラでセットアップされたScope オブジェクトはmessage プロパティとgoodbye 関数を保持します。初期表示のタイミングではコントローラにてmessage 変数に'Hello, World!' が定義され、その設定がScope のmessage プロパティに反映し、最終的にテンプレートのmessage が更新されて画面上に「Hello, World!」が表示されます。

テンプレートのpush ボタンをクリックすると、イメージ図は以下のように変化します。

![scope_2](https://user-images.githubusercontent.com/28583094/48980797-0e36a380-f111-11e8-89d1-7611bb1e7218.png)

push ボタンをクリックするとScope に登録されているgoodbye 関数が呼び出されます。goodbye 関数はコントローラのmessage 変数の値を'Goodbye, Everyone!' に変更するため、Scope のmessage プロパティ、そしてテンプレートのmessage まで反映されて、画面には「Goodbye, Everyone!」が表示されるというわけです。

<a name="ref2"></a>※2 MVCモデルにおけるコントローラは、あくまでDOM 操作に対する処理を記述するもので、ビジネスロジックに相当する処理はModel で作成されるべきであり、それはAngularにおけるコントローラモジュールにおいても同様です。
つまり、**コントローラではScope のセットアップ処理に注力**し、肥大化を防ぐことでテスタビリティやメンテナンス性を損なわないようにすることが理想的です。
## 5. 実現方法

最後に、以下のような例をAngular で実現する方法について説明したいと思います。
- HTMLにはテキストボックスと保存ボタンを用意  
- 初期表示でサーバから取得した文字列をテキストボックスに表示し、手入力で書き換えた内容を保存ボタンを押下してコミットする

コードは以下のようになります。（3節のプログラムの記述よりも、ui-router などを用いてより実践的な書き方にしています。

```HTML
// templateUrl: hoge/fuga.html

<div>
    <input type="text" ng-model="hoge.message" />
    <button ng-click="hoge.save()">保存</button>
</div>
```

```javascript
ui-router：

$stateProvider.state('frame.Frame', {
    url: '/hoge',
    templateUrl: 'hoge/fuga.html',
    controller: 'HogeController',
    controllerAs: 'hoge'
})
```

```javascript
controller：

angular.module('app', []).controller('HogeController', HogeController);

// $injectを用いてDIを行う
HogeController.$inject = [
    'HogeService'
];

function HogeController(
    HogeService
) {

    init();
    
    function init() {
        // Scope に変数と関数を登録
        this.message = HogeService.getMessage();
        this.save = save;
    }
    
    function save() {
        // 引数の値をDBに登録するための関数
        HogeService.save(this.message);
    }
}

```

まずは3節の記述方法から変わった点について説明します。

#### ①テンプレートにng-controller の記述がない

3節ではng-controller ディレクティブを用いてテンプレートとコントローラを結び付けていましたが、今回はその記述を用いていません。どうやってテンプレートとコントローラ（Scope）を結びつけているのかを解説します。

まずは、ui-router にて「**ルーティング**」の設定を行っています。ルーティングとはSPA(Single Page Application)を実装するために必要な機能で、テンプレートとURLを紐付けることができます<sup>[※3](#ref3)</sup>。また、この設定の中で、テンプレートが開かれたときに**実行されるコントローラの定義**と、コントローラに**別名をつける**ことも同時に行えます。

「別名」をつけるためにはcontrollerAs プロパティを用います。そして、「別名」をつけたコントローラは、テンプレートの中から[オブジェクト名].[プロパティ名]と明示することで、**Scope のように参照できる**ようになります。今回の例では、テンプレートに対して「HogeController」を紐付けて、「HogeController」には「hoge」という別名をつけています。したがって、テンプレートからは「hoge.[プロパティ名]」とすることで参照しているわけです。

#### ②コントローラに$scope の記述がない

3節では、コントローラにて「$scope.[変数名]」とすることで、Scope オブジェクトにプロパティや関数を登録していましたが、今回は「this.[変数名]」という記述で登録を行っています。

これは、実は①でcontrollerAs を用いてコントローラに別名を付けたと説明しましたが、その効果によってコントローラでは$scope を使わずに**this で登録が行える**ようになったんですね。
　
#### ③$inject を用いてサービスのDI している

Angular ではコントローラで使用するサービスなどの定義は**DI** (Dependency Injection)<sup>[※4](#ref4)</sup>で行います。3節ではcontroller 関数の第二引数に['サービス名1','サービス名2'...,'コントローラ関数'] のような配列を渡すことでインジェクトしていました。

今回は、コントローラー関数のみを引数に渡して、$inject を用いてコントローラにサービスをインジェクトしています。

さらに、3節ではcontroller 関数の第二引数内でコントローラ関数を無名関数で定義していましたが、可読性を向上させるために、HogeController という名前で関数を定義して引数に渡しています。
　  
　  
**最後にここまでの解説を踏まえた上で、このプログラムの挙動について説明します。**

まず、テンプレートfuga.hetml を開くと、HogeController のHogeController 関数が呼ばれます。HogeController 関数はinit 関数を呼び出し、Scope オブジェクトにmessage 変数とsave 関数を登録します。

message 変数にはHogeService サービスのgetMessage 関数から取得した値が設定されます。message プロパティに値が設定されたので、テンプレートのテキストボックスには、取得してきた文字列が表示され、これで初期表示の処理は終了となります。

その後、テキストボックスに表示された文字列を編集すると、フォーカスアウトされたタイミングでScope オブジェクトのmessage プロパティが更新され、コントローラのmessage 変数の値にも反映されます。

最後に、保存ボタンをクリックすると、コントローラのsave 関数が呼び出され、message 変数の値、つまりテキストボックスに入力された文字列を引数に、HogeService サービスのsave 関数を呼び出します。

以上が、プログラムの挙動の説明になります。

<a name="ref3"></a>※3 [SPA・ルーティングについて](https://qiita.com/Yamamoto0525/items/e870713d9d05d2d36a80)  
<a name="ref4"></a>※4 DIについて： [Dependency Injection](https://docs.angularjs.org/guide/di)
