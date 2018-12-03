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

従来はJavascript のコードから直接DOMを操作し値の書き換えを行ったり、DOM 要素に対するイベントのハンドルが必要でした。  
ここで、DOM は何か？といった部分を説明すると主旨からだいぶ外れてしまうので割愛します。

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

天下り的になってしまいますが、とりあえず上のプログラムをAngularを用いて実装してみると以下のようになります。

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

※今回の例では、ng-controller を使ってテンプレートにコントローラの割り当てを行っていますが、通常?一般的に？よく見るの？はui-router を用いてルーティングの設定の一部としてコントローラの指定を行います。（その場合、テンプレート側ではng-controller を用いてコントローラの指定は行いません。）  
これについては5節にて軽く触れる。

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

```HTML
HTML:

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

HogeController.$inject = ['$scope',
    'HogeService'
];

function HogeController($scope,
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
