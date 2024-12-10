# File

## `path_provider`の導入

pubspec.yaml
```
dependencies:
  flutter:
    sdk: flutter
  path_provider: ^2.1.5
```

## Fileを作成する

```dart
  Future<void> createLocalFile() async {
    final dirPath = await getApplicationDocumentsDirectory();
    file = File('${dirPath.path}/text.txt');
    //ファイルが作成されていない場合のみ作成
    if (!file.existsSync()) {
      file.create();
    }
  }
```

## データを取得する

```dart
Future<void> getDataFromFile() async {
    await createLocalFile();
    final text = await file.readAsString();
    if (text.isNotEmpty) {
      final textArray = text.split("\n");
      setState(() {
        wordList = textArray;
      });
    }
  }
```

## データを書き込む

```dart
Future<void> addTextToFile(String text) async {
    String saveText = "";
    setState(() {
      wordList.add(text);
    });
    for (var word in wordList) {
      saveText += "$word\n";
    }
    await file.writeAsString(saveText);
    await getDataFromFile();
  }
```




