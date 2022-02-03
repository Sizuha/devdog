# iOS 개발관련 문제 및 해결

## 달력 문제
Calendar.current 로 달력을 획득하면, 로컬 설정에 영향을 받으므로, 달력을 정확히 지정해서 가져와야 한다.
```swift
let calendar = Calendar(identifier: .gregorian)
let date = calendar.date(from: DateComponents(year: 2016, month: 10, day: 1))
// -> 2016年10月1日 0時0分
```

## iOS 9 지원

### ATS: http 요청이 블록됨
정식 해결 방법은 http를 https 형식으로 바꾸면 된다.

위 방법이 불가하다면, Info.plist에 예외 설정을 추가.

참조: http://dev.classmethod.jp/smartphone/iphone/ios-9-intro-ats/

#### ATS を無効にする (非推奨)

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

#### 基本的にATS を有効にし、ATS の対象にしないドメインを Info.plist に記載する

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>insecure.example.com</key>
        <dict>
            <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
            <true/>
        </dict>
    </dict>
</dict>
```

### Bitcode 지원 문제
링크에서 Bitcode 관련 오류가 발생하면, **Build Settings**의 **Enable Bitcode** 항목을 **NO**로 바꿔서 다시 빌드.

그래도 왠만하면 Bitcode를 적용하자.

**Bitcode란?**<br/>
VM의 바이트 코드 처럼 중간단계의 코드를 생성한다. 하지만 중간코드 상태에서 실행되는 게 아니라, 앱을 인스톨 할 때 대상환경에 맞게 컴파일 되서 설치가 된다.

### iPad 멀티테스킹 지원 문제
아카이브를 업로드 하는 단계에서 iPad 멀티테스킹 지원 관련 오류가 발생할 수 있다.

이유는, iPad 멀티테스킹을 지원하려면 가로, 세로 모든 방향에 대한 레이아웃이 지원되어야 하기 때문인데, 한쪽 방향만 써야되는 경우라면  다음과 같이 멀티테스킹 관련 옵션을 끄면 된다.

**General** 탭에서 **Requires full screen** 항목을 체크 표시.

### 그 외
서드 파티 플러그인/라이브러리 등을 사용하고 있다면, iOS 9 지원 버전으로 업데이트 하는게 좋다.

## iOS 10 지원
### 녹음 기능을 사용하려고 하면 앱이 강제 종료
iOS10 이후, **Info.plist**에 권한 설정이 필요해짐.

```Privacy - Microphone Usage Description```

```xml
<dict>
  . . .
  <key>NSMicrophoneUsageDescription</key>
  <string>사용자에게 보여질 설명</string>
</dict>
```

## iOS 13 지원
### Xcode 11로 빌드할 때 
XCode 11에서 배포버전을 iOS12로 지정해도 iOS13 SDK가 적용되는 듯 함. 때문에 iOS13에서 자동으로 iOS13  UI에 대응을 해버리는데, 예를들면 기본UI에서 iOS13의 다크 모드가 바로 적용된다.

### 다크 모드 UI지원 
iOS은 설정에서 다크 모드를 ON/OFF 할 수 있다. 기본 UI들은 다크 모드일 때 자동으로 색상이 바뀐다.

**주의사항!!**
- 글자색을 검정색(black) 혹은 흰색 등으로 하나로만 지정하지 말 것! (배경색을 직접 지정한 경우 제외)
  - 다크 모드 일 때 글자가 안 보일 수 있다.

테마에 따라 달리 색상이 적용되는 색상 코드가 추가 되었다.
```swift
UIColor.label
UIColor.secondaryLabel
UIColor.tertiaryLabel
UIColor.quaternaryLabel

UIColor.placeholderText

UIColor.systemFill
UIColor.secondarySystemFill
UIColor.tertiarySystemFill
UIColor.quaternarySystemFill

UIColor.systemBackground
UIColor.secondarySystemBackground
UIColor.tertiarySystemBackground

UIColor.systemGroupedBackground
UIColor.secondarySystemGroupedBackground
UIColor.tertiarySystemGroupedBackground

UIColor.systemIndigo
```

### modalPresentationStyle 지정
UIViewController를 present할 경우에 지정하는 modalPresentationStyle 속성이 기본값이 _.automatic_ 으로 되어 있다. 이전 버전(iOS12)에서는 전체화면(_.fullscreen_)으로 표시 됐지만, iOS13에서는 페이지 시트(_.pageSheet_)로 표시된다.

modalPresentationStyle을 항상 직접 지정하도록 하자.

```swift
let vc = UIViewController()
// 遷移方法にフルスクリーンを指定
vc.modalPresentationStyle = .fullScreen
self.present(vc, animated: true, completion: nil)
```

### WebKit
보안문제로 로컬 경로를 지칭하는 URL(_file://_)을 사용할 수 없게 됐다. 단, 웹 페이지를 표시하는 뷰의 경우에만 해당되는 듯.

### StatusBar
iOS12 이하에서 사용한 코드로 StatusBar의 View를 가져오려고 하면 에러가 발생한다.

> Terminating app due to uncaught exception 'NSInternalInconsistencyException', 
> reason: 'App called -statusBar or -statusBarWindow on UIApplication: this code must be changed as there's no longer a status bar or status bar window.
Use the statusBarManager object on the window scene instead.'

해결 방법은 다음 링크 참조.
* https://velog.io/@gwangyonglee/iOS13-StatusBar-Crash-%EC%9D%B4%EC%8A%88-gmk0pc92v3
* https://freakycoder.com/ios-notes-13-how-to-change-status-bar-color-1431c185e845

이 위키 내에서는, [IOS App 개발(UI)](ios_dev_ui.md#Statusbar) 참조.

## 빌드 및 배포

우선 확인 사항
1. 빌드 전에 타겟을 **Generic iOS Device**으로 지정해 둘 것.
1. Build Settings/Build for Active Architecture only = YES

### 코드 사인 문제
#### 코드 사인이 올바르지 않다
Xcode에서 빌드 중에 코드 사인이 올바르지 않다는 메세지와 함께 빌드가 실패한다.
##### 해결
**iOS Provisioning Portal**에서 현재 인증서를 폐기(Revoke)하고 다시 만든다.

새 인증서로 Provisioning도 다시 생성.

#### The identity used to sign the executable is no longer valid ====
다음과 같은 에러 메세지
> The identity used to sign the executable is no longer valid
> Please verify that your device's clock is properly set,
> and that your signing certificate is not expired.

원인은 아마도 프로비져닝에 새로운 App ID를 추가했거나, 기존의 App ID의 유효기간이 만료된 경우.

##### 해결
Xcode＞Preferences＞Accounts 로 가서, Apple ID를 지웠다가 다시 추가해 본다.

#### XXX has denied the launch request
Xcode에서 iOS 기기를 연결하고 실행시켰을 때, 이런 메세지가 나온다면, 개발용 프로비져닝이 아닌 AdHoc 등의 배포용 프로비져닝으로 실행시킨 경우.

코드 사인을 개발용 프로비져닝으로 바꿔서 할 것.

### App Store에 Upload시, 인증에 실패하는 경우
Xcode를 오랫동안 종료하지 않고 쓰고 있는 경우

App Store 배포 권한이 없다는 식의 메세지가 나올 경우가 있는데, 계정과 프로비져닝에 딱히 문제가 없다면, Xcode를 껐다 켜서 다시 시도하면 된다.

### iTunes Connect에 빌드가 안보임
iOS10 이후, **Info.plist**에 접근권한 설정이 필요해 졌는데, App Store에 업로드 하는 단계에서도 체크가 들어간다. 그런데 이 단계에서 문제가 발견된 경우, iTunes Connect에 아무런 표시도 없고 개발자 계정의 이메일로만 통보를 한다.

대체로 NSCameraUsageDescription 등이 설정이 안되어 있는 경우일 것이다.

> This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSCameraUsageDescription key with a string value explaining to the user how the app uses this data.
