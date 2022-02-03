# App Store 검수

Apple App Store에 등록할 때, 검수 과정에서 유의 사항

## 앱 설명 및 스크린샷
- 타 플랫폼에 대한 언급이 있으면 안된다.
- 스샷의 경우에, status bar 등에서 타 OS UI가 있으면 안됨.
- 앱 내부에서도 타사 플랫폼에 대한 것이 보이면 안됨.
- 완전한 제품을 등록해야 한다.
  - Test, Trial, Beta 상태의 제품은 등록할 수 없다. 관련 표현도 삭제.

## 앱 이용에 관한
- 자기 앱을 사용하기 위해, 다른 앱이 **필수**로 필요한 상황이 되어서는 안된다.
  - 소셜 로그인(Twitter, Faceboook 등) 정도는 괜찮을지도 모르지만(웹에서도 처리 가능하므로), 카카오톡이나 라인 앱등이 필수가 되어서는 안될 듯.

- 로그인 혹은 회원등록을 웹브라우져를 통해서 하지 말 것
  - Details We noticed that the user is taken to Safari to sign in or register for an account, which provides a poor user experience.<br /><br />
  - 해결: App 안에서 WebView를 띄우거나, App 자체에서 가입 폼을 제공.

- 앱을 사용하기 위해, QR코드 스캔 및 프로모션 코드, 해제 코드 등을 요구해서는 안된다.
  - 아마도 아른 우회수단으로 결재를 요구할 수 있기 때문인듯.

## IPv6
2016-06-01부터 IPv6 지원이 의무사항이 되었다. 
- 직접 소켓 통신을 하는 등의 로우 레벨 API를 쓰지말고, iOS에서 권장하는 API 사용.
- 서버 통신에서 IP주소를 쓰지 말고, 도메인 이름을 사용한다.

## iCloud
### iCloud 자동백업 설정
> On launch and content download, your app stores 256 MB on the user's iCloud, which does not comply with the iOS Data Storage Guidelines.

기본적으로 Document 디렉토리에 있는 파일들은 자동 백업 대상이 된다. 이때 자동 백업되는 파일 용량이 256MB를 넘기면 안된다.

다음과 같이, 백업이 불필요한 디렉토리를 지정해 주자.
```objc
// Excluding a File from Backups on iOS 5.1 and later (Objective C)
- (BOOL)addSkipBackupAttributeToItemAtPath:(NSString *) filePathString
{
    NSURL* URL= [NSURL fileURLWithPath: filePathString];
    assert([[NSFileManager defaultManager] fileExistsAtPath: [URL path]]);
 
    NSError *error = nil;
    BOOL success = [URL setResourceValue: [NSNumber numberWithBool: YES]
                                  forKey: NSURLIsExcludedFromBackupKey error: &error];
    if(!success){
        NSLog(@"Error excluding %@ from backup %@", [URL lastPathComponent], error);
    }
    return success;
}
```

```swift
//Excluding a File from Backups on iOS 5.1 and later (Swift)
func addSkipBackupAttributeToItemAtURL(filePath: String) {
    let url = NSURL.fileURL(withPath: filePath) as NSURL
    do {
        try url.setResourceValue(true, forKey: URLResourceKey.isExcludedFromBackupKey)
    }
  catch let error as NSError {
        print("Error excluding \(url.lastPathComponent ?? "") from backup \(error)")
    }
}
```

Unity의 경우는 다음과 같이 간단히 처리된다.
```csharp
#if UNITY_IOS
UnityEngine.iOS.Device.SetNoBackupFlag(path);
#endif
```

참고: http://qiita.com/tototti/items/8646405f47cc56a59722

#### バックアップ対象外の確認方法
バックアップが対象外とされているか確認する方法としては、対象アプリをインストール、利用し、iTunesでアプリを同期した後、PCにバックアップされた中身を見るのが手っ取り早いです。

iTunesのバックアップは、実際には暗号化されてしまっていますが、それを復元するiphone backup extractorなどを利用すればすぐに複合化して取り出せます。

1. ツールをダウンロードし、インストール: http://supercrazyawesome.com/
1. アプリを起動
1. バックアップ端末を選択
1. アプリを選択
1. 抽出したバックアップの保存先を選択

この手順だけで、バックアップを抽出可能です。

バックアップを抽出したフォルダの中に、バックアップ対象外のファイルやフォルダが無ければ処理としては成功しているものと思います。(iCloudのバックアップもこれと同じ挙動、のはず)
