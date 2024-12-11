# Transform

## 概要

### `Transform`

子のWidgetの傾き、サイズ、位置、見え方などを変化させるWidget

## 使用例

### Transform.rotate

・45度傾いたロゴを表示
```dart
 Center(
        child: Transform.rotate(
            angle: 45 * pi / 180,
            child: const FlutterLogo(size: 40),
        ),
      ),
```

> [!NOTE]
> 角度はラジアンで指定する

### Transform.scale

・2倍の大きさのロゴを表示

```dart
Center(
        child: Transform.scale(
            scale: 2.0,
            child: const FlutterLogo(size: 40),
        ),
      ),
```

`scaleX`,`scaleY`を用いることでX,Yそれぞれの倍率を変えることもできる

### Transform.flip

・ロゴをX,Yそれぞれ鏡写しに反転する

```dart
Center(
        child: Transform.flip(
          flipX: false,
          flipY: false,
          child: const FlutterLogo(size: 30),
        )
      ),
```

![flip1](/images/flip1.png)

・`flipX`: true, `flipY`: false

![flip2](/images/flip2.png)

・`flipX`: false, `flipY`: true

![flip3](/images/flip3.png)

・`flipX`: true, `flipY`: true

![flip4](/images/flip4.png)


### Transform.translate

・ロゴを右に30,下に30ずらす

```dart
Center(
        child: Transform.translate(
          offset: Offset(30, 30),
          child: const FlutterLogo(size: 60),
        )
      ),
```
## アニメーションと組み合わせる

`AnimatonController`と`AnimationBuilder`を組み合わせる


・ロゴを回転させる

```dart
class _SimpleTransformPageState extends State<SimpleTransformPage>
    with SingleTickerProviderStateMixin {
  late AnimationController controller;
  late CurvedAnimation animation;
  final duration = const Duration(seconds: 1);
  final curve = Curves.easeInOut;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(
      vsync: this,
      duration: duration,
    );
    //カーブを適用するためにCurvedAnimation使用
    animation = CurvedAnimation(parent: controller, curve: curve);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          AnimatedBuilder(
            animation: animation,
            builder: (BuildContext context, Widget? child) {
              return Transform.rotate(
                //0~2piの範囲で値が変わる
                angle: (animation.value * 2 * pi),
                child: child,
              );
            },
            child: const FlutterLogo(size: 60),
          ),
          const SizedBox(height: 100),
          ElevatedButton(onPressed: () {
            setState(() {
              controller.reset();
              controller.forward();
            });
          }, child: const Text("Rotate"))
        ],
      ),
    ));
  }
}
```

### 動作例

![rotate](/gif/transform_rotate.gif)

## Matrix4

4x4の行列になっており、3D空間での位置や奥行きの設定を行う


### 画面全体を回転させる

```dart
class Transform3DPage extends StatefulWidget {
  const Transform3DPage({super.key});

  @override
  State<Transform3DPage> createState() => _Transform3DPageState();
}

class _Transform3DPageState extends State<Transform3DPage> {
  @override
  Widget build(BuildContext context) {
    return Transform(
      transform: Matrix4.identity()
        ..setEntry(3, 2, 0.001)
        ..rotateX(0)
        //90度分回転
        ..rotateY(pi / 4),
      alignment: FractionalOffset.center,
      child: Scaffold(
        body: const Center(
          child: Text("Hello World"),
        ),
        floatingActionButton: FloatingActionButton(
          onPressed: () {},
          child: Icon(Icons.add),
        ),
      ),
    );
  }
}
```

・`Matrix4.identity()`: 初期の行列を生成する

・`setEntry`: 行列の指定座標の値を変更する

`3,2`に対して`0.001`を渡すと立体感が強調されるようになる

![matrix4](/images/matrix4.png)

## アニメーションとの組み合わせ

・基本は2Dのアニメーション実装と同じ

```dart
class Transform3DPage extends StatefulWidget {
  const Transform3DPage({super.key});

  @override
  State<Transform3DPage> createState() => _Transform3DPageState();
}

class _Transform3DPageState extends State<Transform3DPage>
    with SingleTickerProviderStateMixin {
  late AnimationController controller;
  late CurvedAnimation animation;
  final duration = const Duration(seconds: 3);
  final curve = Curves.easeInOut;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(duration: duration, vsync: this);
    animation = CurvedAnimation(parent: controller, curve: curve);
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: animation,
      builder: (context, Widget? child) {
        return Transform(
          alignment: FractionalOffset.center,
          transform: Matrix4.identity()
            ..setEntry(3, 2, 0.001)
            ..rotateY(animation.value * 2 * pi),
          child: child,
        );
      },
      child: Scaffold(
        body: const Center(
          child: Text("Hello World"),
        ),
        floatingActionButton: FloatingActionButton(
          onPressed: () {
            setState(() {
              controller.reset();
              controller.forward();
            });
          },
          child: const Icon(Icons.rotate_left),
        ),
      ),
    );
  }
}
```

### 動作例

![matrix4_animation](/gif/matrix4_animation.gif)



