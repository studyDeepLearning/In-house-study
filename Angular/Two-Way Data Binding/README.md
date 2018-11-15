# 双方向データバインディング

## 双方向データバインディングって何？

AngularJS<sup>[※1](#ref1)</sup> （以降はAngular）が持つ特徴的な機能の1つで、Javascript のオブジェクト（Model）が保持するデータと、HTML テンプレート（View）として画面に表示される内容を自動的に連携する機能です。

簡単に言えば、「画面での入力はJavascript 上の変数に自動的に反映される」、さらに「Javascript 上の変数が変化すると自動的に画面に反映される」といった動きをするので『**双方向**』と呼ばれているわけです。。

<a name="ref1"></a>※1 AngularJS で双方向データバインディングを実現するために最も重要なScope オブジェクトですが、**Angular2 以降は廃止**されています。そもそも、Anguar2 では原則単方向バインディングを利用することになっています（双方向も使えないことはないが）。

## 従来の場合

従来はJavascript のコードから直接DOMを操作し値の書き換えを行ったり、DOM 要素に対するイベントのハンドルが必要でした。  
注）ここで、DOM は何か？といった部分を説明すると主旨からだいぶ外れてしまうので割愛します。

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

## Angular の場合

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
    

```

通常、cng-controllerを使ってHTMLと

## scope オブジェクトとは？

## 実現方法
