# Carousel

## CarouselViewを使用する方法

```dart
ConstrainedBox(
              constraints: BoxConstraints(
                  maxHeight: MediaQuery.sizeOf(context).height * 0.5),
              child: CarouselView(
                  itemExtent: MediaQuery.sizeOf(context).width,
                  itemSnapping: true,
                  controller: _controller,
                  children: List<Widget>.generate(3, (int index) {
                    return ImageCardView(imageName: imageNameList[index]);
                  })),
            ),
```

`ConstrainedBox`でカルーセルの表示範囲を指定

・`itemExtent`: MainAxis(通常横)方向のサイズを指定

・`itemSnapping`: `true`の時、1アイテムずつ止まる、`false`の時、リストのようにスクロール

・`controller`: `CarouselController`をセットできる

### 1アイテム分のWidget

```dart
class ImageCardView extends StatelessWidget {
  final String imageName;

  const ImageCardView({super.key, required this.imageName});

  @override
  Widget build(BuildContext context) {
    return Image.asset(
      "$imagePath$imageName",
    );
  }
}
```

## インジケータを作成する

### 現在のページ数を取得する関数を作成する

```dart
//controllerと現在のIndexを保持する変数を作成
 late CarouselController _controller;
  int nowIndex = 0;
```

```dart
//コントローラを初期化してリスナを追加
 @override
  void initState() {
    super.initState();
    _controller = CarouselController()
      ..addListener(() {
        changeIndex();
      });
  }
```

```dart
//カルーセルの位置を画面幅で割ることでpositonを取得する
 void changeIndex() {
    setState(() {
      nowIndex =
          (_controller.position.pixels / (MediaQuery.sizeOf(context).width))
              .round();
    });
  }
```
### インジケータを作成する

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.center,
  children:
  //ドットインジケータを作成
  List<Widget>.generate(imageNameList.length, (int index) {
  return Padding(
    padding: const EdgeInsets.symmetric(horizontal: 4),
    child: Icon(
    Icons.circle,
    //現在のIndexに合わせて色を変更
    color: index == nowIndex ? Colors.red : Colors.black,
    ));
  }),
),
```




