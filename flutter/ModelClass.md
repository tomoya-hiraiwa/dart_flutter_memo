# ModelClass

## 基本形

```dart
class Event {
  int id;
  String title;
  String text;

  Event({required this.id, required this.title, required this.text});
}
```

・含まれる型、変数名のみを定義したクラスを作成する。

## Jsonとのやりとり

### Jsonからモデルクラス(fromJson)

```dart
 Event.fromJson(Map<String, dynamic> json)
      : id = json["Id"],
        title = json["Title"],
        text = json["Text"];
```

ただし、Jsonの構造が深いなど、単純なKey名のみで取得することができない場合、

```dart
  //factoryをつけて通常のreturnで返す
  factory Event.fromJson(Map<String, dynamic> json) {
    return Event(id: json["Id"], title: json["Title"], text: json["Text"]);
  }
```

### モデルクラスからJson(toJson)

```dart
  Map<String, dynamic> toJson() {
    //valueをモデルクラスの変数名で指定
    return {
      'Id': id,
      'Title': title,
      'Text': text
    };
}
```

## 出力(toString)

 モデルクラスは、そのままprintなどで出力すると、

 ```
Instance of 'Event'
```

のように表示される

### toString()をオーバーライドする

```dart
  @override
  String toString() {
    return "id: $id, title: $title, text: $text";
  }
```


## 実装例

```dart
class Event {
  int id;
  String title;
  String text;

  Event({required this.id, required this.title, required this.text});

  //シンプルな形の場合
  // Event.fromJson(Map<String, dynamic> json)
  //     : id = json["Id"],
  //       title = json["Title"],
  //       text = json["Text"];

  //直接値を代入できない場合
  factory Event.fromJson(Map<String, dynamic> json) {
    return Event(id: json["Id"], title: json["Title"], text: json["Text"]);
  }

  //出力用
  @override
  String toString() {
    return "id: $id, title: $title, text: $text";
  }
}
