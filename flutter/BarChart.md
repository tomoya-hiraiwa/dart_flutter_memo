# BarChart

## 前提条件

model.dart
```dart
class ChartData{
  String name;
  int value;
  ChartData({required this.name, required this.value});

  factory ChartData.fromJson(Map<String,dynamic> json){
    return ChartData(name: json["name"], value: json["value"]);
  }

  @override
  String toString() {
    return "name: ${name}, value: ${value}";
  }
}
```
`name`にデータ名、`value`に値の入ったモデルクラスのデータを使用

## グラフ一つ分のWidget

```dart
class ChartWidget extends StatelessWidget {
  final ChartData data;

  const ChartWidget({super.key, required this.data});

  @override
  Widget build(BuildContext context) {
    return Expanded(
        child: Column(
      mainAxisAlignment: MainAxisAlignment.end,
      children: [
        Container(
          height: data.value * 3,
          width: 50,
          color: Colors.black,
        ),
        const Divider(),
        const SizedBox(height: 5),
        Text(data.name)
      ],
    ));
  }
}
```

## 画面全体

```dart
class TopPage extends StatefulWidget {
  const TopPage({super.key});

  @override
  State<TopPage> createState() => _TopPageState();
}

class _TopPageState extends State<TopPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        //描画範囲を指定する
        child: ConstrainedBox(
          constraints: BoxConstraints(
              maxHeight: 500, maxWidth: MediaQuery.sizeOf(context).width),
          child: Row(
              children: List<Widget>.generate(dataList.length, (int index) {
            return ChartWidget(data: dataList[index]);
          })),
        ),
      ),
    );
  }
}
```

## 完成イメージ

![barchart](/images/bar_chart.png)
