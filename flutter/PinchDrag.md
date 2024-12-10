# 画像のピンチ・ドラッグ動作

## `InteractiveViewer`を使用する

```dart
InteractiveViewer(maxScale: 2.0,
  minScale: 0.8,
  panEnabled: true,
  boundaryMargin: const EdgeInsets.all(4),
  child: Image.asset("assets/images/map.jpg"),
)
```
・`minScale`: ピンチアウト時の最小倍率を指定

・`panEnageld`:  ピンチ動作の有効・無効を指定

・`boundaryMargin`: `child`widgetと外周とのマージンを指定
