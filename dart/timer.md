# 一定間隔で動作するタイマー

### `dart:sync`ライブラリを使用

```dart
//タイマー型の変数を定義
Timer? _timer;

//initStateでタイマーを作成
  @override
  void initState() {
    super.initState();
    _timer = Timer.periodic(const Duration(seconds: 1), (timer) {
      setState(() {});
    });
  }

//disposeで停止
  @override
  void dispose() {
    _timer?.cancel();
    super.dispose();
  }
```

・`Duration`で間隔を定義可能

・設定された間隔ごとにコールバックの内容が動作する
