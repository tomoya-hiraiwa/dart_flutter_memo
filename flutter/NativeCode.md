# ネイティブコードの呼び出し

## Andorid(バッテリー残量取得の場合)

### 1.メソッドチャンネルの作成

```dart
static const platform = MethodChannel('samples.flutter.dev/battery')
```

channel名の文字列はなんでもOK

### 2.ネイティブコード呼びだしメソッドの作成

```dart
//結果を受けとる変数を作成しておく
String _batteryLevel = 'Unknown battery level.';

//ネイティブコードを呼び出すメソッドを作成
 Future<void> _getBatteryLevel() async {
    String batteryLevel;
    try {
      final result = await platform.invokeMethod<int>('getBatteryLevel');
      batteryLevel = 'Battery level at $result % .';
    } on PlatformException catch (e) {
      batteryLevel = "Failed to get battery level: '${e.message}'.";
    }

    setState(() {
      _batteryLevel = batteryLevel;
    });
  }
```

```dart
final result = await platform.invokeMethod<int>('getBatteryLevel');
```

ここでネイティブコードを呼び出し、その結果を取得する

`invokeMethod`メソッドが非同期のため、必ず呼び出し元のメソッドも非同期になる

`invokeMethod`の型には、結果として受け取るデータの型を指定する

### kotlin-dart間での型変換表

kotlin | Dart | 備考
 --- | --- | ---
 null|null
 Boolean|bool
 Int|int|32bit以下
 Long|int|32bitよりより大きい
 Double|double|
 String|String
 ByteArray|Uint8List
 IntArray|Int32List
 LongArray|Int64List
 FloatArray|Float32List
 DoubleArray|Float64List
 List|List
 HashMap|Map

 ### 3.ネイティブコードの作成

 `adnroid/app/sorce/main/kotlin/$package_name/MainActivity`に記述

※`android`ディレクトリを新しくAndroidStudioで開く

> [!IMPORTANT]
> Flutterプロジェクトで開いているAndroidStudioとは別で開かないと、コード補完やオートインポートが効かなくなる

```dart
class MainActivity: FlutterActivity(){
    //Dartのコードで作成したチャンネル名と合わせる
    private val CHANNEL = "samples.flutter.dev/battery"

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        MethodChannel(flutterEngine.dartExecutor.binaryMessenger, CHANNEL).setMethodCallHandler { call, result ->
            //invokeMethodで呼び出したメソッド名(文字列)で分岐
            if (call.method == "getBatteryLevel"){
                val batteryLevel = getBatteryLevel()
                if (batteryLevel != -1){
                    result.success(batteryLevel)
                }
                else {
                    result.error("UNAVAILABLE","Battery level not available.",null)
                }
            }else {
                result.notImplemented()
            }
        }
    }
    //バッテリー残量を取得するネイティブコード
    private fun getBatteryLevel(): Int {
        val batteryLevel: Int
        if(VERSION.SDK_INT >= VERSION_CODES.LOLLIPOP){
            val batteryManager = getSystemService(Context.BATTERY_SERVICE) as BatteryManager
            batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY)
        } else {
            val intent = ContextWrapper(applicationContext).registerReceiver(null, IntentFilter(Intent.ACTION_BATTERY_CHANGED))
            batteryLevel = intent!!.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100/ intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1)
        }
        return batteryLevel
    }
}
```

`result`の値が呼び出し元のDartコードに返される

・成功時の値：`result.success()`

・失敗時の値：`result.error()`

・メソッドが存在しない場合：`result.notImplemented()`


## その他

### ネイティブコードでActivity依存のメソッドなどを呼び出したい場合

→継承元を`FlutterActivity`から`FlutterFragmentActivity`に変更

ex) パーミッションチェック時に`registerForActivityResult`を使用

```dart
class MainActivity: FlutterFragmentActivity(){
    private lateinit var file: File
    private val channel = "get_image/"
    private var resultData: MethodChannel.Result? = null
    private val permissionLauncher = registerForActivityResult(ActivityResultContracts.RequestPermission()){bool ->
        if (bool){
          //Do something
        }
        else {
        resultData?.success("permission denied")
        }
    }
   

    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        MethodChannel(flutterEngine.dartExecutor.binaryMessenger,channel).setMethodCallHandler{call, result ->
            if (call.method == "getImage"){
                resultData = result
                permissionLauncher.launch(android.Manifest.permission.READ_MEDIA_IMAGES)
            }
            else {
                result.notImplemented()
            }
        }
    }
}
```

### 任意の場所で結果を返すようにする

`MethodChannel.Result`型の変数を作成し、`success()`メソッドなどを呼び出すことで、任意の場所で結果を返すことができる

```dart
//結果用の変数を作成
 private var resultData: MethodChannel.Result? = null

    //パーミッションチェック
    private val permissionLauncher = registerForActivityResult(ActivityResultContracts.RequestPermission()){bool ->
        if (bool){
            //パーミッションチェック後に結果を返す
            resultData?.success("permission granted")
        }
        else {
        resultData?.success("permission denied")
        }
    }
```



 
