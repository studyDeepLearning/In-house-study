
コードスニペット（markdown.json）には以下のスニペットが保存されている

参考記事：
[会議を爆速にする、Visual Studio Code 超便利スニペット集](https://qiita.com/kitfactory/items/31cdf80cf1d3d8b42de8)

---

呼び出しコード：**meeting**

## xxxx会議録
日時:2018/10/xx
場所:xxx会議室
出席者:xxx,xxx,xxx,xxx

### 1.目的
* xxxxの対応を決める。
* xxxxの対応を決める。

### 2.結論
* xxxxをxxxとする。
* xxxxをxxxとする。

### 3.A.I.
* xxxxxする。(担当: xxx 期日： x/xまで)
* xxxxxする。(担当: xxx 期日： x/xまで)
* xxxxxする。(担当: xxx 期日： x/xまで)

### 4.議論
* xxxxはxxxではないか？(Aさん)
→ xxxにする。

---

呼び出しコード：**pros_cons**

|案|メリット|デメリット|決定|
|:--|:--|:--|:--|
|案A:xxxする| | | |
|案B:xxxする| | | |
|案C:xxxする| | | |

---

呼び出しコード：**workflow**

```plantuml
@startuml
|__担当A__|
:xxxする;
|__担当B__|
:xxxする;
|#ccf|__担当C(部署X)__|
:xxxする;
|__担当B__|
:xxxする;
fork
|__担当B__|
:xxxする;
forkagain
|#cfc|__担当D(部署Y)__|
:xxxする;
end fork
|__担当A__|
:xxxする;
@enduml
```

---

呼び出しコード：**pdpc**

__PDPC__
```plantuml
@startuml
(*)-->"xxxする"
if "A問題ができた?" then
-->[Yes]"yyyする"
-->"zzzする"
-->"cccする"
else
-->[No]"aaaする"
-->"bbbする"
-->"cccする"
endif
"cccする"-->(*)
@enduml
```

---

呼び出しコード：**pfd**

__開発プロセス(PFD)__
```plantuml
@startuml
agent 企画書
agent 要求仕様書
agent 設計書
agent ソースコード

(企画)-->[企画書]
[企画書]-right->(要件定義)
(要件定義)-right->[要求仕様書]
[要求仕様書]-right->(設計)
[企画書]-right->(設計)
(設計)-down->[設計書]
[企画書]-right->(実装)
[要求仕様書]-right->(実装)
[設計書]-->(実装)
(実装)-->[ソースコード]
@enduml
```

---

呼び出しコード：**system_component**

```plantuml
@startuml
actor A

node "社内"{
    A -up->[xxxサーバー]: アクセス
}

node "AWS"{
    database DB
    [xxxサーバー]-right->[xxxAPI] : hoge
    [xxxAPI]-right->DB : xxxする
}
@enduml
```
---

呼び出しコード：**uml_sequence**

```plantuml
@startuml
actor Foo
activate Foo
activate Bar
Foo-->Bar:barMethod
activate Baz
Bar-->Baz:bazMethod
Baz-->Bar:response
deactivate Baz
Bar-->Foo:response
deactivate Bar
@enduml
```

[help](http://yohshiy.blog.fc2.com/blog-entry-153.html)

---

呼び出しコード：**uml_activity**

```plantuml
@startuml
(*) --> "Foo"
"Foo" -> "Bar"
if "Bar?" then
->[ok] "Baz"
else
 ->[no] "Qux"
endif
"Baz" --> ==gate==
"Qux" --> ==gate==
==gate== --> (*)
@enduml
```

---

呼び出しコード：**uml_state**

```plantuml
@startuml
[*] --> Foo
Foo -right-> Bar : disable
Bar -left-> Foo  :enable
Foo --> Baz  : close
Bar --> Baz  : close
Baz --> [*]
@enduml
```

---


