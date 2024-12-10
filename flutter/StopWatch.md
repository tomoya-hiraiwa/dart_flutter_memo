# StopWatchの作成

`StopWatch()`クラスを使用する


## 使い方

1。インスタンス生成
```dart
final timer = StopWatch();
```

2.計測開始
```dart
timer.start();
```

3.計測停止
```dart
timer.stop();
```

4.リセット
```dart
timer.reset();
```

## 表示方法


・一秒ごとに更新

```dart
 Future<void> showTime() async{
      await Future.delayed(const Duration(seconds: 1));
      if(timer.isRunning){
          setState(() {
            //mm:ssの形
            nowTime = "${timer.elapsed.inMinutes.toString().padLeft(2,"0")}:${(timer.elapsed.inSeconds %60).toString().padLeft(2,"0")}";
          });
      }
      showTime();
  }
```

・ms単位で表示

```dart
  Future<void> _showTime() async {
    await Future.delayed(const Duration(milliseconds: 10));
    if (timer.isRunning) {
      setState(() {
        //mm:ss.SSSの形
        _timeString =
            " ${timer.elapsed.inMinutes.toString().padLeft(2, "0")}:${(timer.elapsed.inSeconds % 60).toString().padLeft(2, "0")}.${(timer.elapsed.inMilliseconds % 1000).toString().padLeft(3, "0")}";
      });
      _showTime();
    }
  }
```

### 実装例

```dart
class TopPage extends StatefulWidget {
  const TopPage({super.key});

  @override
  State<TopPage> createState() => _TopPageState();
}

class _TopPageState extends State<TopPage> {
  final timer = Stopwatch();
  String nowTime = "00:00";

  Future<void> showTime() async{
      await Future.delayed(const Duration(seconds: 1));
      if(timer.isRunning){
          setState(() {
            nowTime = "${timer.elapsed.inMinutes.toString().padLeft(2,"0")}:${(timer.elapsed.inSeconds %60).toString().padLeft(2,"0")}";
          });
      }
      showTime();
  }

  @override
  void initState() {
    super.initState();
    showTime();
  }

  @override
  Widget build(BuildContext context) {
    return SafeArea(
      child: Scaffold(
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(nowTime, style: const TextStyle(fontSize: 30)),
              const SizedBox(height: 20),
              ElevatedButton(onPressed: (){
                if(!timer.isRunning) {
                  setState(() {
                    timer.start();
                  });
                }
                else null;
              }, child: const Text("Start")),
              const SizedBox(height: 10),
              ElevatedButton(onPressed: (){
                if(timer.isRunning) {
                  setState(() {
                    timer.stop();
                  });
                }
                else null;
              }, child: const Text("Stop")),
              const SizedBox(height: 10),
              ElevatedButton(onPressed: (){
                setState(() {
                  timer.stop();
                  timer.reset();
                  nowTime = "00:00";
                });
              }, child: const Text("Reset"))
            ],
          ),
        ),
      ),
    );
  }
}
```
