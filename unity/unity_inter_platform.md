# Unity 플랫폼 연동

## Android 연동

### Android와 Unity의 연동: Android Studio
참고: http://www.thegamecontriver.com/2015/04/android-plugin-unity-android-studio.html

#### 라이브러리용 프로젝트 생성
1. 새 프로젝트 생성
   1. 이때 패키지 이름은 Unity 프로젝트의 Bundle ID와 맞춘다.
1. 유니티 jar 라이브러리 추가: PlaybackEngines/androidplayer/bin/classes.jar 
   1. 윈도우는 Unity 설치경로/Editor/Data 아래에 위치.
   1. 맥은 패키지 내용보기/Content 아래에 위치.
      1. Unity 5.3의 경우: Applications/Unity/PlaybackEngines/AndroidPlayer/Variations/{il2cpp or mono}/Release/Classes/
   1. classes.jar을 App/libs 폴더로 복사한다.
1. App/libs/classes.jar을 마우스 오른쪽 메뉴의 **Add as Library**를 선택해서 추가.

#### App/build.gradle 편집
##### AAR로 만들 경우
.aar 파일은 클래스 파일 뿐만 아니라 각종 리소스 파일 및 jar파일 까지도 같이 포함되서 빌드가 된다.

그런데, 유니티 클래스 파일인 classes.jar은 Unity에서 빌드할 때 당연히 중복이 되므로, AAR빌드에서 제외시켜야 한다.
```
apply plugin: 'com.android.library'

android {
    // . . .
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'], exclude: ['unity-classes.jar'])
    compileOnly fileTree(dir: 'libs', include: ['unity-classes.jar'])
    // . . .
}
```

##### JAR로 만들 경우
[Android Studio: JAR export](/android/andorid_studio.md#JAR-export) 참조.

#### MainActivity class 수정

```java
import com.unity3d.player.UnityPlayerActivity;

public class MainActivity extends UnityPlayerActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
}
```

#### AndroidManifest.xml 수정
Unity 프로젝트에서 Android Project Export를 통해 생성된 AndroidManifest.xml 내용을 참고해서 수정한다.
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="jp.co.gngs.projectd" android:versionName="1.0" android:versionCode="1" android:installLocation="auto">
    <supports-screens android:smallScreens="true" android:normalScreens="true" android:largeScreens="true" android:xlargeScreens="true" android:anyDensity="true" />
    <application android:theme="@android:style/Theme.NoTitleBar.Fullscreen" android:icon="@drawable/app_icon" android:label="@string/app_name" android:debuggable="false" android:isGame="true" android:banner="@drawable/app_banner">
        <activity android:label="@string/app_name" android:screenOrientation="portrait" android:launchMode="singleTask" android:configChanges="mcc|mnc|locale|touchscreen|keyboard|keyboardHidden|navigation|orientation|screenLayout|uiMode|screenSize|smallestScreenSize|fontScale" android:name="jp.co.gngs.projectd.MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <meta-data android:name="unityplayer.UnityActivity" android:value="true" />
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="11" android:targetSdkVersion="22" />
    <uses-feature android:glEsVersion="0x00020000" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-feature android:name="android.hardware.sensor.accelerometer" android:required="false" />
    <uses-feature android:name="android.hardware.touchscreen" android:required="false" />
    <uses-feature android:name="android.hardware.touchscreen.multitouch" android:required="false" />
    <uses-feature android:name="android.hardware.touchscreen.multitouch.distinct" android:required="false" />
</manifest>
```

빠져있는 리소스(아이콘 등)도, Unity에서 생성한 안드로이트 프로젝트에 있는 것을 그대로 가져다 쓴다. (일단 빌드가 되게 하는게 목적)

### Unity3D C#과 Android Java 코드간의 상호 호출

#### Android에서 Unity의 메서드 호출

```java
UnityPlayer.UnitySendMessage("gameObjectName", "MethodName",  "argument");
```

#### Unity에서 Android 메서드 호출

```java
AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
activity = jc.GetStatic<AndroidJavaObject>("currentActivity");
activity.Call("method", arg1, arg2, ...);
```

#### Unity에서 Android 메서드 호출을 간편하게 하기위한 스크립트

```csharp
using UnityEngine;
using System.Collections;

public class AndroidManager 
{

  const string main_class_name = 
    "com.unity3d.player.UnityPlayer";
  
  private static AndroidManager _instance;

  public static AndroidManager Instance {
    get {
      if (_instance == null) {
        _instance = new AndroidManager();
        _instance.Init();
      }
      
      return _instance;
    }
  }
  

  #if UNITY_ANDROID
  // Unityが作動するActivityを保存...
  public AndroidJavaObject activity; 
  #endif
  
  public void Init() {
    #if UNITY_ANDROID
    // 健在のUnity PlayerのActivityを探がす...
    AndroidJavaClass jc = new AndroidJavaClass(main_class_name);
    activity = jc.GetStatic<AndroidJavaObject>("currentActivity");
    #endif
  }

  public static void Release() {
    _instance = null;
  }
  
  public static void AndroidCall(string method, params object[] args) {
    #if UNITY_ANDROID
    Instance.activity.Call(method, args);
    Debug.Log("AndroidCall: " + method);
    #endif
  }
  
}
```

#### 주의 사항
Unity에서 Android 코드를 실행 시킬 때, Android쪽에서는 UI 스레드에서 실행 시켜야 하는 경우가 제법 있다.<br />UI 스레드에서 실행되어야 하는 코드가 있다면 Android측 소스에서 다음과 같이 처리해 준다.
```java
runOnUiThread(new Runnable() {     
  public void run() {
    // TO DO ...
  } 
});
```

Java에서 Unity C#의 코드를 호출 할 때, 다음과 같이 오버로드 되어있는 메서드가 있을 경우, 인자가 없는 쪽의 메서드만 호출된다.<br />즉, Java에서 Callback으로 사용할 Unity C#쪽의 메서드는 절대로 오버로드 시키지 말것!!
```java5
void CallbackFunc() { ... } // Java에서 UnitySendMessage()로 호출하면 이것만 불림.
void CallbackFunc(string arg) { ... }
```

#### Unity에서 Android Activity 실행

```javascript
// Unity
void OnGUI() {
     if (GUI.Button(new Rect(0,0, 200, 200), "Login"))
     {
         // to get the activity
         var androidJC = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
         var jo = androidJC.GetStatic<AndroidJavaObject>("currentActivity");
         // Accessing the class to call a static method on it
         var jc = new AndroidJavaClass("com.example.fboomplug.StartActivity");
         // Calling a Call method to which the current activity is passed
         jc.CallStatic("Call", jo);
     }
}
```

```java
// Android

public class StartActivity extends Activity {
     private String TAG = "Plug.StartActivity";
     @Override
     public void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         Log.d(TAG,"Activity created");
     }
 
     public static void Call(Activity activity)
     {      
         // Creating an intent with the current activity and the activity we wish to start
         Intent myIntent = new Intent(activity, StartActivity.class);
         activity.startActivity(myIntent);
     }
}
```

## iOS 연동

### iOS에서 Unity의 메서드 호출
어떤 include도 필요없이, 그냥 다음과 같이 UnitySendMessage 호출.
```objc
UnitySendMessage("GameObjectName", "MethodName", "Message to send");
```

### Unity에서 iOS 코드 호출
Unity C#에서 iOS 코드(objective-c)를 바로 호출할 수는 없지만, iOS 코드를 C함수로 랩핑하면 가능하다.

먼저, Unity C#에서 사용하고자 하는 네이티브 함수를 선언한다.
```csharp
using System.Runtime.InteropServices;

[DllImport ("__Internal")]
private static extern float FooPluginFunction();
```

이제 .c, .cpp, .m, .mm 파일 등으로 네이티브 코드를 작성해서 Plugins 경로에 추가해 준다. (헤더 파일은 없어도 된다) 단, Unity C#에서 호출할 네이티브 코드는 C 함수 형태로 되어 있어야 하고, C++ 코드인 경우는 ''extern "C"'' 블록으로 감싸준다.
```objc
extern "C" {
  float FooPluginFunction ();
} 
```

#### 자동 플러그인 추가
**{Untity Project}/Assets/Plugins/iOS** 경로에 추가되는 .h, .c, .cpp, .m, .mm, .a 등의 파일은 Unity3D에서 iOS용 프로젝트를 생성할 때도 자동으로 함께 복사가 된다.

### Unity GL View

```objc
UIView* view = UnityGetGLView();
UIViewController* vc = UnityGetGLViewController();
```
