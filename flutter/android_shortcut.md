# Android Shortcut

## ShortCutの作成

### initStateでのネイティブメソッドの呼び出し

```dart
await channel.invokeMethod("shortcut");
```

### intent-filterの追加

```xml
 <intent-filter>
                <action android:name="shortcut"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
```

・独自のインテントを作成し、呼び出しでアプリを起動できるようにする

###  ショートカットの作成と追加

```kotlin
private fun createShortCut() {
        //共通のインテントを作成
        val baseIntent = Intent(Intent.ACTION_VIEW).apply {
            setClassName(packageName, "$packageName.MainActivity")
            action = "shortcut"
        }
        //リスト形式必要数分のショートカットを作成
        val shortcuts = listOf(
            ShortcutInfoCompat.Builder(this, "s1")
                .setShortLabel("Page1")
               //intentにputExtraで値を渡す 
                .setIntent(Intent(baseIntent).putExtra("page", 0))
                .build(),
            ShortcutInfoCompat.Builder(this, "s2")
                .setShortLabel("Page2")
                .setIntent(Intent(baseIntent).putExtra("page", 1))
                .build(),
            ShortcutInfoCompat.Builder(this, "s3")
                .setShortLabel("Page3")
                .setIntent(Intent(baseIntent).putExtra("page", 2))
                .build(),
        )
        //ショートカットを表示する
        ShortcutManagerCompat.setDynamicShortcuts(this, shortcuts)
    }
```

### ショートカットによる起動

```kotlin
if (call.method == "shortcut"){
                createShortCut()
                result.success(null)
            }
            else{
                    //ショートカットからの起動時にインテントの値を取得する
                    val index = intent?.getIntExtra("page",0)
                    result.success(index)
                }
            }
```

・ネイティブコード例

```kotlin
class MainActivity: FlutterActivity(){
    private var index = 0
    private val channel = "native"
    override fun onCreate(savedInstanceState: Bundle?, persistentState: PersistableBundle?) {
        super.onCreate(savedInstanceState, persistentState)
        if (intent?.action == "shortcut"){
            index = intent?.getIntExtra("page",1)!!
        }
    }
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        MethodChannel(flutterEngine.dartExecutor.binaryMessenger, channel).setMethodCallHandler { call, result ->
            if (call.method == "shortcut"){
                createShortCut()
                result.success(null)
            }
            else{
                    val index = intent?.getIntExtra("page",0)
                    result.success(index)
                }
            }
        }

    private fun createShortCut() {
        val baseIntent = Intent(Intent.ACTION_VIEW).apply {
            setClassName(packageName, "$packageName.MainActivity")
            action = "shortcut"
        }

        val shortcuts = listOf(
            ShortcutInfoCompat.Builder(this, "s1")
                .setShortLabel("Page1")
                .setIntent(Intent(baseIntent).putExtra("page", 0))
                .build(),
            ShortcutInfoCompat.Builder(this, "s2")
                .setShortLabel("Page2")
                .setIntent(Intent(baseIntent).putExtra("page", 1))
                .build(),
            ShortcutInfoCompat.Builder(this, "s3")
                .setShortLabel("Page3")
                .setIntent(Intent(baseIntent).putExtra("page", 2))
                .build(),
        )

        ShortcutManagerCompat.setDynamicShortcuts(this, shortcuts)
    }

}
```

### Flutter側からの呼び出し

```dart
  @override
  void initState() {
    super.initState();
    controller = TabController(length: 3, vsync: this);
    shortcut();
  }

  Future<void> shortcut() async {
    await channel.invokeMethod("shortcut");
    //ショートカット作成後、indexを取得するメソッドを呼び出す
    nowIndex = await channel.invokeMethod("getIndex");
    setState(() {
      controller.index = nowIndex;
    });
  }
```

### 画面切り替えコード例

```dart
void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: const HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with TickerProviderStateMixin {
  int nowIndex = 0;
  static const channel = MethodChannel("native");
  late TabController controller;

  @override
  void initState() {
    super.initState();
    controller = TabController(length: 3, vsync: this);
    shortcut();
  }

  Future<void> shortcut() async {
    await channel.invokeMethod("shortcut");
    nowIndex = await channel.invokeMethod("getIndex");
    setState(() {
      controller.index = nowIndex;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: TabBarView(
        controller: controller,
        children: [
          Page1(),
          Page2(),
          Page3(),
        ],
      ),
    );
  }
}
```


