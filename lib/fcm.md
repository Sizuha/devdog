# Firebase Cloud Messaging

- 참고: [구글 문서](https://firebase.google.com/docs/cloud-messaging/?hl=ja)

## 프로젝트 생성
제일 먼저 할 일은, [Firebase Console](https://console.firebase.google.com/)에서 프로젝트를 생성하는 것.

### 디버그 서명 인증서 SHA-1(선택사항)

```
keytool -exportcert -list -v -alias androiddebugkey -keystore ~/.android/debug.keystore 
```
디버그 키의 기본 암호는 'android' 이다.

## Android

### 메세지 처리

- [참고](https://firebase.google.com/docs/cloud-messaging/android/receive#sample-receive)

| アプリの状態 | notificationキーのみ含む | dataキーのみ含む | 両方のキーを含む |
| - | - | - | - |
| フォアグラウンド  | onMessageReceived | onMessageReceived | onMessageReceived |
| バックグラウンド  | システムトレイ | onMessageReceived | システムトレイ |

#### 포그라운드 일때
onMessageReceived 이베트 발생. 앱에서 Notification을 만들어 줘야 함.

#### 백그라운드 일때
onMessageReceived 이벤트를 거치지 않고 시스템에서 알아서 Notification을 만들어 버림.
<br/>단, Data Payload부분은 Activity에서 Intent의 Extra 영역으로 전달된다.

#### BroadcastReceiver로 수신

포그라운드, 백그라운드 상관없이 푸시 이벤트를 받을 수 있다.

**AndroidManifest.xml**에 BroadcastReceiver 추가.

```xml
<receiver
    android:name=".MyReceiver"
    android:exported="false"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    </intent-filter>
</receiver>
```

BroadcastReceiver의 onReceive 이벤트 구현.
```kotlin
class FcmReceiver: BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        val TAG = "FcmReceiver"

        Log.d(TAG, intent?.extras.toString())
        if (context == null || intent?.extras == null) return

        for (k: String in intent.extras!!.keySet()) {
            Log.d(TAG, "$k => ${intent.extras!![k].toString()}")
        }

        // Data Payloadから（例）
        // var title = intent.getStringExtra("title") ?: ""
        
        // Notificationから
        var title = intent.getStringExtra("gcm.notification.title") ?: ""        
        var body = intent.getStringExtra("gcm.notification.body") ?: ""

        // ...
    }
}
```

