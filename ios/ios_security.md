# iOS Security

## Root化対策

```swift
// MARK: - Root化確認

/// Root化確認
/// - Returns: Root化された場合「true」を返す
func checkJailBroken() -> Bool {
    if DEBUG_MODE && TARGET_OS_SIMULATOR == 1 {
        // iOSシミュレーターでは脱獄のチェックを行わない
        return false
    }
    return checkCydia() || checkPath() || checkWritable()
}

// Cydiaのチェック
fileprivate func checkCydia() -> Bool {
    FileManager.default.fileExists(atPath: "/Applications/Cydia.app")
        || UIApplication.shared.canOpenURL(URL(string: "cydia://")!)
}

// Pathのチェック
// ファイルが存在するか確認を行う。iOS端末では存在しない。
fileprivate func checkPath() -> Bool {
    FileManager.default.fileExists(atPath: "/Library/MobileSubstrate/MobileSubstrate.dylib")
        || FileManager.default.fileExists(atPath: "/bin/bash")
        || FileManager.default.fileExists(atPath: "/usr/sbin/sshd")
        || FileManager.default.fileExists(atPath: "/etc/apt")
        || FileManager.default.fileExists(atPath: "/private/var/lib/apt/")
}

// ファイルの書き込み権限のテスト
fileprivate func checkWritable() -> Bool {
    // 本来であれば書き込みの権限がない箇所に書き込みができるかチェック
    let stringToWrite = "Jailbreak"
    do {
        try stringToWrite.write(toFile:"/private/JailbreakTest.txt", atomically: true, encoding:String.Encoding.utf8)
        //書き込めたため脱獄と判断
        return true
    }
    catch {
        return false
    }
}
```

## SSL証明書ピン留め

[証明書の公開キーのHash値確認](/android/android_security.md#ssl証明書のピン留めpinning)

서버의 SSL인증서에서 공개키 부분을 취득 후, SHA-256으로 해시화시켜서 앱 내에 저장되어 있는 해시값과 비교한다.
```swift
func urlSession_withPinning(didReceive challenge: URLAuthenticationChallenge, completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void) {
    let urlCredential: URLCredential?
    if let serverTrust = challenge.protectionSpace.serverTrust {
        urlCredential = URLCredential(trust: serverTrust)
    }
    else {
        urlCredential = nil
    }

    // Making sure the challende is about the domain we want to pin
    guard challenge.protectionSpace.host == "도메인" else {
        completionHandler(.useCredential, urlCredential)
        return
    }

    guard
        // Checking if auth method needs pinning
        challenge.protectionSpace.authenticationMethod == NSURLAuthenticationMethodServerTrust,

        // Evaluating server trust
        let serverTrust = challenge.protectionSpace.serverTrust,
        evaluateServerTrust(serverTrust),

        // Generate key data from public key of the server
        let serverPublicKeyData = generateServerPublicKeyData(from: serverTrust),

        // Comparing hashed key of the server to the pinning public key of the app
        isServerPublicKeyData(serverPublicKeyData, equalTo: "앱 내에 저장된 SSL공개키 해시값")
    else {
        // SSL Pinning failed.
        completionHandler(.cancelAuthenticationChallenge, nil)

        return
    }

    completionHandler(.useCredential, urlCredential)
}
```

위 함수를 URLSession 혹은 WebView의 Delegate안에서 호출하게 하면 된다.

예) WebView의 경우:
```swift
extension MainViewController: WKUIDelegate, WKNavigationDelegate {
  func webView(_ webView: WKWebView, didReceive challenge: URLAuthenticationChallenge, 
    completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void
  ) {
      urlSession_withPinning(didReceive: challenge, completionHandler: completionHandler)
  }
  
  // . . .
}
```

