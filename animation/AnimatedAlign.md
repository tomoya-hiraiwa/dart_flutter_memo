# AnimatedAlign

## 概要

・`child`で指定されたWidgetのAlign(位置)の変更をアニメーションするWidget

### `AnimatedAlign`

・`alignment`: WidgetのAlignment(位置)を指定する

・`duration`: アニメーション時の経過時間を指定する

・`curve`: アニメーションの補完曲線を指定する

## 使用例

```dart
class AlignAnimatePage extends StatefulWidget {
  const AlignAnimatePage({super.key});

  @override
  State<AlignAnimatePage> createState() => _AlignAnimatePageState();
}

class _AlignAnimatePageState extends State<AlignAnimatePage> {
  //秒数とカーブを指定
  static const Duration duration = Duration(seconds: 1);
  static const Curve curve = Curves.fastOutSlowIn;
  bool isClicked = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Container(
          width: MediaQuery.sizeOf(context).width * 0.8,
          height: MediaQuery.sizeOf(context).width * 0.8,
          decoration:
              BoxDecoration(border: Border.all(color: Colors.blue, width: 10)),
          child: Padding(
            padding: const EdgeInsets.all(8.0),
            child: AnimatedAlign(
              //isClickedフラグによって位置を決定
              alignment: isClicked ? Alignment.topRight : Alignment.bottomLeft,
              duration: duration,
              curve: curve,
              //クリック可能にする
              child: InkWell(
                  onTap: () {
                    setState(() {
                      isClicked = !isClicked;
                    });
                  },
                  child: const FlutterLogo(size: 60.0)),
            ),
          ),
        ),
      ),
    );
  }
}
```

## 動作例

![animated_align](/gif/animated_align.gif)
