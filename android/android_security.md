# Android Security

## 권한 설정

꼭 필요한 경우가 아니면, 아래 권한은 삭제
- READ_EXTERNAL_STORAGE
- WRITE_EXTERNAL_STORAGE

## 암호화 되지 않은 통신

AndroidManifest.xml에서, android:usesCleartextTraffic 설정을 false로 하면 HTTPS등 암호화된 통신만 허용된다.
```xml
<application
        . . .
        android:usesCleartextTraffic="false">
```

## ```file://``` 링크 무효화

WebSettings의 allowFileAccess 속성을 false로 설정.
```kotlin
mWebView?.settings?.apply {
    // ...
    allowFileAccess = false // セキュリティー問題により、非活性化
}
```

## SSL証明書のピン留め（Pinning）

https://developer.android.com/training/articles/security-config

SSL인증서의 공개키에 대한 해시값을 취득
```
openssl s_client -servername 도메인 -connect 도메인:443 | openssl x509 -pubkey -noout | openssl rsa -pubin -outform der | openssl dgst -sha256 -binary | openssl enc -base64
```

Example from the link above. Add to ```AndroidManifest.xml```:
```xml
<manifest ... >
    <application android:networkSecurityConfig="@xml/network_security_config" ... >
        ...
    </application>
</manifest>
```
Create a resource file ```xml/network_security_config.xml```:
```xml
<network-security-config>
    <domain-config>
        <domain includeSubdomains="true">example.com</domain>
        <pin-set expiration="2018-01-01">
            <pin digest="SHA-256">공개키 해시값</pin>
        </pin-set>
    </domain-config>
</network-security-config>
```

## Root化対策

https://re-engines.com/2018/12/26/safetynet-attestation-api-basic/

Google의 SafetyNet Attestation API를 활용.

1. [Google API Consoleのライブラリページ](https://console.developers.google.com/apis/library?hl=ja)に移動します。

1. **Android Device Verification API**を検索して選択します。Android Device Verification API ダッシュボードの画面が表示されます。

1. API がまだ有効になっていない場合は、[有効にする] をクリックします。

1. [認証情報を作成] ボタンが表示された場合は、それをクリックして API キーを生成します。表示されなかった場合は、[すべての API 認証情報] プルダウン リストをクリックしてから、Android Device Verification API を有効にしたプロジェクトに関連付けられている API キーを選択します。

1. 左のサイドバーで、[認証情報] をクリックします。表示された [API キー] をコピーします。


API결과값을 해석하기 위해서느 JWT라이브러가 필요하지만, 서버에 전송해서 결과를 해석하는 경우에 앱에서는 필요없다.

```kotlin
import com.nimbusds.jose.JWSObject // JWT 라이브러리 사용

// Root化確認
if (GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(this) == ConnectionResult.SUCCESS) {
    // The SafetyNet Attestation API is available.

    val key = getString(R.string.safety_net_api_key)
    val nonce = getRequestNonce("attest")

    SafetyNet.getClient(this).attest(nonce, key)
        .addOnSuccessListener(this) {
            val result = it.jwsResult

            var isOk = false
            try {
                val jwsObject = JWSObject.parse(result)
                //println("payload = " + jwsObject.payload.toJSONObject())

                val payload = jwsObject.payload.toJSONObject()

                // Package name
                val pk = payload["apkPackageName"] as? String ?: ""
                isOk = this.packageName == pk

                // basicIntegrity
                val chk_bi = payload["basicIntegrity"] as? Boolean == true

                // ctsProfileMatch
                val chk_cts = payload["ctsProfileMatch"] as? Boolean == true
                isOk = isOk && chk_bi && chk_cts

                // Nonce
                //val r_nonce = payload["nonce"] as? String ?: ""
                // . . .
            } catch (e: java.text.ParseException) {
                //e.printStackTrace()
                isOk = false
            }

            if (isOk) {
                // TODO 다음으로
            } else {
                // TODO 에러표시
            }
        }
        .addOnFailureListener(this) { e ->
            // An error occurred while communicating with the service.
            if (e is ApiException) {
                // An error with the Google Play services API contains some
                // additional details.
                val apiException = e as ApiException
                Log.e("ATTEST", e.message + e.statusCode)
            } else {
                // A different, unknown type of error occurred.
                Log.d("ATTEST", "Error: " + e.message)
            }

            // TODO 에러표시
        }
} else {
    // TODO Prompt user to update Google Play services.
}
```

## 難読化（ProGuard）

```gradle
    buildTypes {
        debug {
            minifyEnabled false
        }
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
```

JavaScript Interface 클래스는 난독화에서 예외처리
```
# proguard-rules.pro

# If your project uses WebView with JS, uncomment the following
# and specify the fully qualified class name to the JavaScript interface
# class:
-keepclassmembers class 패키지.클래스명 {
   public *;
}
```

Java JSON Web Token 라이브러리(JWT) 사용시
```
-keepattributes InnerClasses

-keep class io.jsonwebtoken.** { *; }
-keepnames class io.jsonwebtoken.* { *; }
-keepnames interface io.jsonwebtoken.* { *; }

-keep class org.bouncycastle.** { *; }
-keepnames class org.bouncycastle.** { *; }
-dontwarn org.bouncycastle.**
```