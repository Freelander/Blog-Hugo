---
title: Android 与 Flutter 之间通信
date: 2020-04-02 23:17:58
tags: [Flutter]
categories: Flutter
draft: false
---

### 概述

Flutter 与原生之间的通信机制都是通过 Platform Channel 进行传递，如下图所示：

![image](/images/2020/04/flutter_channel_01.png)

原生 与 Flutter 之间的通信主要有有四种实现方式：

1. 在初始化 Flutter 页面时会传递一个字符串 Route，因此我们就可以通过 Route 从 ``原生`` 向 ``Flutter`` 端传递自己想要的数据；
2. 通过 MethodChannel 来实现，MethodChannel 支持数据双向传递，有返回值。
3. ~~通过 EventChannel 来实现，EventChannel 仅支持数据单向传递，无返回值。~~
4. ~~通过 BasicMessageChannel 来实现，BasicMessageChannel 支持数据双向传递，有返回值。~~


### Flutter 向原生通信


#### Flutter 端

```dart

class MethodChannelPage extends StatefulWidget {
  @override
  _MethodChannelPageState createState() => _MethodChannelPageState();
}

class _MethodChannelPageState extends State<MethodChannelPage> {
  // 创建通信渠道，通道名称必须唯一，且与原生那边统一
  MethodChannel methodsChannel = MethodChannel("com.chicdeals/methodsChannel");
  String _langText = '切换语言';

  @override
  Widget build(BuildContext context) {
    return Scaffold (
      backgroundColor: Color(0xFFFFFFFF),
      body: Align(
        alignment: Alignment.center,
        child: Container(
          height: 44,
          padding: EdgeInsets.only(left: 10, right: 10),
          alignment: Alignment.center,
          child: RaisedButton(
            child: Text(
              _langText,
            ),
            textColor: Colors.white,
            color: Color(0xFF49C9A7),
            onPressed: _sendMethodToNative,
          ),
        ),
      ),
    );
  }

  Future<void> _sendMethodToNative() async {
    String sendMsg = 'Flutter切换语言';
    // 向原生发送消息，标识符为 'flutter_change_language'
    // 原生那边接收到消息，并相应结果回来
    String result = await methodsChannel.invokeMethod('flutter_change_language', sendMsg);
    // 刷新页面按钮文案
    setState(() {
      _langText = result;
    });
  }
}

```

#### Android 端

```java
public class FlutterDemoActivity extends AppCompatActivity {

    private static final String TAG_FLUTTER_FRAGMENT = "flutter_fragment";
    private MaterialButton mBtnChangeLanguage;
    private FlutterFragment mFlutterFragment;

    private MethodChannel mMethodChannel;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activiy_flutter_demo);
        mBtnChangeLanguage = findViewById(R.id.btn_change_language);

        FragmentManager fragmentManager = getSupportFragmentManager();

        mFlutterFragment = (FlutterFragment) fragmentManager.findFragmentByTag(TAG_FLUTTER_FRAGMENT);

        // Create and attach a FlutterFragment if one does not exist.
        if (mFlutterFragment == null) {
            mFlutterFragment = FlutterFragment.withNewEngine()
                    .initialRoute("Flutter Demo")
                    .build();

            fragmentManager
                    .beginTransaction()
                    .add(R.id.container, mFlutterFragment, TAG_FLUTTER_FRAGMENT)
                    .commit();
        }
    }

    @Override
    protected void onPostCreate(@Nullable Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        // 通道名称需要与 Flutter 模块那边统一，并且唯一
        mMethodChannel = new MethodChannel(getBinaryMessenger(), "com.chicdeals/methodsChannel");
        
        mMethodChannel.setMethodCallHandler(new MethodChannel.MethodCallHandler() {
            @Override
            public void onMethodCall(@NonNull MethodCall call, @NonNull MethodChannel.Result result) {
                if (call.method.equals("flutter_change_language")) {
                    String callMsg = call.arguments.toString();
                    // 回调 Flutter 模块那边
                    result.success(callMsg);
                    mBtnChangeLanguage.setText(callMsg);
                }
            }
        });
    }

    protected FlutterEngine getFlutterEngine() {
        return mFlutterFragment.getFlutterEngine();
    }

    private BinaryMessenger getBinaryMessenger() {
        if (getFlutterEngine() == null) {
            return null;
        }

        return getFlutterEngine().getDartExecutor().getBinaryMessenger();
    }
}
````

### Android 向 Flutter 通信

#### Android 端

```
public class FlutterDemoActivity extends AppCompatActivity {

    private static final String TAG_FLUTTER_FRAGMENT = "flutter_fragment";
    private MaterialButton mBtnChangeLanguage;
    private FlutterFragment mFlutterFragment;

    private MethodChannel mMethodChannel;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activiy_flutter_demo);
        mBtnChangeLanguage = findViewById(R.id.btn_change_language);

        FragmentManager fragmentManager = getSupportFragmentManager();

        mFlutterFragment = (FlutterFragment) fragmentManager.findFragmentByTag(TAG_FLUTTER_FRAGMENT);

        // Create and attach a FlutterFragment if one does not exist.
        if (mFlutterFragment == null) {
            mFlutterFragment = FlutterFragment.withNewEngine()
                    .initialRoute("Flutter Demo")
                    .build();

            fragmentManager
                    .beginTransaction()
                    .add(R.id.container, mFlutterFragment, TAG_FLUTTER_FRAGMENT)
                    .commit();
        }
    }

    @Override
    protected void onPostCreate(@Nullable Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        // 通道名称需要与 Flutter 模块那边统一，并且唯一
        mMethodChannel = new MethodChannel(getBinaryMessenger(), "com.chicdeals/methodsChannel");

        mBtnChangeLanguage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String sendMsg = "原生切换语言";
                // 向 Flutter 模块发送消息，标识符为 'native_change_language'，并带上消息
                mMethodChannel.invokeMethod("native_change_language", sendMsg);
                mBtnChangeLanguage.setText(sendMsg);
            }
        });
    }

    protected FlutterEngine getFlutterEngine() {
        return mFlutterFragment.getFlutterEngine();
    }

    private BinaryMessenger getBinaryMessenger() {
        if (getFlutterEngine() == null) {
            return null;
        }

        return getFlutterEngine().getDartExecutor().getBinaryMessenger();
    }
}

```

#### Flutter 端

```
class MethodChannelPage extends StatefulWidget {
  @override
  _MethodChannelPageState createState() => _MethodChannelPageState();
}

class _MethodChannelPageState extends State<MethodChannelPage> {
  // 创建通信渠道，通道名称必须唯一，且与原生那边统一
  MethodChannel methodsChannel = MethodChannel("com.chicdeals/methodsChannel");
  String _langText = '切换语言';

  @override
  Widget build(BuildContext context) {
    return Scaffold (
      backgroundColor: Color(0xFFFFFFFF),
      body: Align(
        alignment: Alignment.center,
        child: Container(
          height: 44,
          padding: EdgeInsets.only(left: 10, right: 10),
          alignment: Alignment.center,
          child: RaisedButton(
            child: Text(
              _langText,
            ),
            textColor: Colors.white,
            color: Color(0xFF49C9A7),
            onPressed: _sendMethodToNative,
          ),
        ),
      ),
    );
  }

  @override
  void initState() {
    // 监听原生传递消息回调
    methodsChannel.setMethodCallHandler(_nativeCallHandler);
    super.initState();
  }

  Future<dynamic> _nativeCallHandler(MethodCall call) async {
    if(call.method == 'native_change_language') {
      // 取出从原生带过来的信息
      String callMsg = call.arguments.toString();
      setState(() {
        _langText = callMsg;
      });
    }
  }
}
```

### Demo 展示

![image](/images/2020/04/flutter_channel_02.gif)

### 参考链接

* [撰写双端平台代码（插件编写实现）](https://flutter.cn/docs/development/platform-integration/platform-channels#step-3-add-an-android-platform-specific-implementation)
* [一篇看懂 Android 与 Flutter 之间的通信](https://juejin.im/post/6844903873358856200)
* [Flutter混合开发(三)：Android与Flutter之间通信详细指南](https://segmentfault.com/a/1190000021012102)