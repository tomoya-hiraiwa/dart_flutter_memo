# DateTime

## 現在の日付のタイムスタンプを取得する

```dart
final timeStamp = DateTime.now().millisecondsSinceEpoch;
```

## タイムスタンプを文字列に変換する

### `intl`パッケージを導入する

[intl](https://pub.dev/packages/intl)

### フォーマットする

```dart
final timeString = DateFormat("yyyy/MM/dd HH:mm:ss").format(DateTime.fromMillisecondsSinceEpoch(DateTime.now().millisecondsSinceEpoch))
```
