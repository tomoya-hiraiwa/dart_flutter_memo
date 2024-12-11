# AnimatedBuilder

## 概要

### `AnimatedBuilder`

子のWidgetにさまざまなアニメーションを与えるために使用するWidget

## 使い方

### 使用するアニメーションを準備する

```dart
//変数の定義
 late AnimationController controller;
  late CurvedAnimation animation;
```

```dart
  @override
  void initState() {
    super.initState();
    controller = AnimationController(vsync: this, duration: duration);

    animation = CurvedAnimation(parent: controller, curve: curve);
  }
```

### `AnimatedBuilder`を使用する

```dart
 AnimatedBuilder(
              animation: animation,
              builder: (BuildContext context, Widget? child) {
                return Transform.translate(
                  offset: Offset(0, 100 * animation.value),
                  child: const FlutterLogo(size: 60),
                );
              },
            ),
```

`animation`: 適用するアニメーションを指定

`builder`: アニメーションしたいwidgetを指定

・`Transform`を使用して、Widgetに変更を加えることができる

参考 

[Transform](Transform.md)

### `child`のwidgetを外に出す

```dart
 AnimatedBuilder(
              animation: animation,
              builder: (BuildContext context, Widget? child) {
                return Transform.translate(
                  offset: Offset(0, 100 * animation.value),
                  child: child,
                );
              },
              child: const FlutterLogo(size: 60),
            ),
```

このように、childで指定するWidgetを外に出すことも可能


### `AnimatedBuilder`を用いた基本構成例

```dart
class AnimationBuilderPage extends StatefulWidget {
  const AnimationBuilderPage({super.key});

  @override
  State<AnimationBuilderPage> createState() => _AnimationBuilderPageState();
}

class _AnimationBuilderPageState extends State<AnimationBuilderPage>
    with SingleTickerProviderStateMixin {
  late AnimationController controller;
  late CurvedAnimation animation;
  final duration = const Duration(seconds: 1);
  final curve = Curves.easeInOut;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(vsync: this, duration: duration);

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
                return Transform.translate(
                  offset: Offset(0, 100 * animation.value),
                  child: child,
                );
              },
              child: const FlutterLogo(size: 60),
            ),
            const SizedBox(height: 200),
            ElevatedButton(
                onPressed: () {
                  setState(() {
                    controller.reset();
                    controller.forward();
                  });
                },
                child: const Text("Anim"))
          ],
        ),
      ),
    );
  }
}
```



