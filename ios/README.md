# iOS

## 개발자 등록

1. http://developer.apple.com/ 접속.
1. iOS Dev Center( http://developer.apple.com/devcenter/ios/index.action ) 클릭.
1. Register 버튼 클릭.

### 주의 사항

* 등록된 이름과 신용카드 상의 이름이 일치해야 뒷탈이 없다. 문제가 생길경우, 애플에 직접 E-amil(koreadev@apple.com)로 문의. (한국어 됨)

> 애플 개발자 서포트에 최근 구입하신 프로그램에 관련한 문의를 주셔서 감사합니다.
> 
> 저희는 애플에서는 개발자 계정 정보와 프로그램 주문정보를 비교하는 기술을 사용하고 있습니다, 개발자님이 겪으신 액티베이션 코드 인증실패는 개발자님의 신용카드에 나와 있는 > 정보와, Apple Developer Program 계정에 있는 정보가 같지 않았기 때문입니다.
> 
> 저희 쪽에서 이 문제를 검토한 결과, 프로그램에 걸려있는 보류를 제거하였습니다. 개발자님께서 받으셨던 원래의 액티베이션 코드를 사용하셔서, 프로그램 인증을 다시 시도해 보시기 바랍니다.

불편을 끼쳐 드려 죄송합니다. 이 과정 후에도 문제가 있으신 경우에 저희에게 다시 알려 주시기 바랍니다.

## 인증서 및 Provisioning

1. [Apple Developer](http://developer.apple.com/) 접속.
1. [iOS Dev Center](http://developer.apple.com/devcenter/ios/index.action) 클릭.
1. **iOS Provisioning Portal** 클릭.

이하는 iOS Provisioning Portal에서 처리해야할 작업들이다.

### WWDR 인증서 받기

1. **Cerificates**에서 하단의 _click here to download now_를 클릭해서 WWDR 인증서를 다운 받는다.
1. 다운 받은 인증서 파일(AppleWWDRCA.cer)을 열어서 Mac에 등록한다.

WWDR 인증서는 유효기간이 있으므로, 유효기간이 만료되면 다시 받아야 한다.

### CSR 생성 및 인증서 요청

1. Mac에서 **키체인 접근** 프로그램을 실행.
1. 이전에 설치했던 _WWDR 인증서_를 선택하고, 메뉴에서 **키체인 접근** > **인증 지원** > **인증기관에서 인증서 요청...** 실행.
1. _인증 지원_ 창이 나타나면, 이메일 주소와 이름을 입력하고, _디스크에 저장됨_을 선택한 후, _계속_ 버튼을 클릭해서 파일 저장. (xxx.certSigningRequest 파일이 생성됨)

#### 개발용 인증서 생성

1. **iOS Provisioning Portal**에서, **Certificates** > **Development** > _Request Certificate_ 클릭.
1. _파일 선택_ 버튼을 누르고, 앞서 저장한 .certSigningRequest 파일을 선택한다.
1. _Submit_ 버튼을 클릭.

Submit 하면 인증 진행중 상태로 표시되는데, 잠시 기다렸다 다시 들어와보면 완료되어 있을 것이다. 처리가 완료되면 인증서를 다운 받아서 Mac에 등록한다.

#### 배포용 인증서 생성

**iOS Provisioning Portal**에서, **Certificates** > **Distribution** > _Request Certificate_ 클릭.

이하, 개발용 인증서 생성과 동일한 절차를 밟는다.

### 개발용 단말기 등록

**Devices** &gt; **Manage** 화면에서 _Add Devices_ 버튼을 클릭하여 단말기 등록.

※ Xcode에서 Organizer를 통해서도 할 수 있다.

### App ID 등록
iOS Provisioning Portal에서 **App IDs** 화면으로 이동.

_New App ID_ 버튼을 클릭해서 새 App ID 등록.

**입력 항목**
- Description : App ID에 대한 설명.
- Bundle Identifier : 역 도메인 방식으로 지정. 일반적으로 유니크한 네이밍으로 지정하게 되나, 와일드 카드(* 문자)를 이용해서 여러 App 그룹을 지정할 수도 있다. 단, 와일드 카드로 지정된 경우는 여러가지로 제약이 붙게 되니, 일단은 유니크한 네이밍으로 하자.

### Provisioning Profile 생성
새 App ID가 추가될 때 마다, 이에 대응되는 프로비져닝 파일을 생성해 준다.

#### 개발용 Provisioning
**Provisioning** > **Development** 탭에서 _New Profile_ 버튼으로 새 프로파일 생성.

프로파일 이름, 사용할 인증서, App ID, 테스트용 디바이스 등을 선택하고 _Submit_ 버튼을 클릭한다. 

※ 나중에 테스트용 디바이스가 추가될 경우에, 이 프로파일 내용을 수정해서 다시 다운 받아야 한다.

#### 배포용 Provisioning

**Distribution** 탭에서 _New Profile_ 버튼으로 새 프로파일 생성.

개발용 프로비져닝 생성과 거의 동일한 내용이나, **Distribution Method**라는 항목이 추가되 있다. App Store용 배포인지, 지정된 단말에 한정된 배포(Ad Hoc)인지 선택해 주면 된다.
:자체적으로 App Store에 등록하여 배포하는 경우는 App Store, 타사가 등록하는 경우는 Ad Hoc을 지정하면 되는 듯.

#### Provisioning 적용
개발용, 배포용 Provisioning을 다운받아서 Mac에 등록.

Xcode의 Organizer에서 **LIBRARY** > **Provisioning Profiles** 항목으로 들어가서, Provisioning이 제대로 설치되어 있는지 확인한다. 상태가 **Valid profile**로 되어 있어야 한다.

### Development Provisioning Assistant
iOS Provisioning Portal의 Home 화면에서 _Launch Assistant_ 버튼을 클릭하면, App ID 등록부터 개발용 Provisioning 생성까지 한번에 진행할 수 있는 마법사 형태의 인터페이스를 이용할 수 있다.

### Xcode 설정

#### settings
- Bundle Identifier: 위에서 작업한 App ID로 설정 함
- Build Settings > Code Signing Identity
  - Debug: 개발용 Provisioning으로 설정 (예: iPhone Developer: xxx)
  - Release: 배표용 Provisioning으로 설정 (예: iPhone Distribution: xxx)

#### Project Build

**테스트용 빌드**
1. Target을 iOS Device 혹은 Mac에 연결된 단말로 설정.
1. Run

**배포용 빌드**
1. Target을 iOS Device 혹은 Mac에 연결된 단말로 설정.
1. 메뉴 > Product > Archive

## App 등록: iTunes Connect
참고: [iTunes Connect Developer Guide](http://itunesconnect.apple.com/docs/iTunesConnect_DeveloperGuide.pdf)
 
### Manage Your Applications: 새 App 등록

**Add New App**버튼 클릭.

#### Step. 1
처음에 입력해야될 항목은 아래와 같다.
* Default Language: 기본 언어 선택.
* App Name: App Store에서 보여질 앱 이름.
* SKU Number: 앱에 대한 유니크한 이름. 문자 및 숫자, 점(.)과 하이픈(-), 밑줄(_) 기호 등을 조합해서 자유롭게 작성.
* Bundle ID: App ID 선택.
''Continue'' 버튼 클릭.

#### Step. 2
다음에 입력해야될 항목은 아래과 같다.
* Availability Date: 언제부터 활성화 할 것인지.
* Price Tier: 앱 가격 선택.
* Discount for Educational Institutions
_Continue_ 버튼 클릭.

#### Step. 3
마지막으로 입력해야될 항목.

- Version Information
  - Version Number
  - Copyright
  - 카테고리 정보

- Rating : App 연령제한 등급을 설정. 해당되는 곳에 체크.

- Metadata
  - Description
  - Keywords
  - Support URL
  - Marketing URL (Optional)
  - Privacy Policy URL (Optional)

- App Review Information
  - First Name
  - Last Name
  - Email Address
  - Phone Number
  - Review Notes (Optional)
  - Demo Account Information (Optional) / Username, Password

- Uploads
  - Large App Icon : App Store에서 보이는 아이콘.
    - A large version of your app icon that will be used on the App Store. It must be at least 72 DPI, in the RGB color space, and 1024 x 1024 pixels (it cannot be scaled up). The file type must be .jpeg, .jpg, .tif, .tiff, or .png. It must be flat artwork without rounded corners.
  - 3.5-Inch Retina Display Screenshots : 3.5인치 레티나 디스플레이용 스크린 샷. 반드시 1개 이상 등록되어야 한다.
    - Screenshots for 3.5-inch iPhone and iPod touch Retina display must be 960x640, 960x600, 640x960 or 640x920 pixels, at least 72 DPI, in the RGB color space, and in the JPG or PNG format.
  - 4-Inch Retina Display Screenshots : 4인치(아이폰5) 레티나 디스플레이용 스크린 샷.
    - Screenshots for 4-inch iPhone 5 and iPod touch (5th generation) Retina display must be 1136x640, 1136x600, 640x1136 or 640x1096 pixels, at least 72 DPI, in the RGB color space, and in the JPG or PNG format.
  - iPad Screenshots : 아이패드용 스크린 샷. 아이패드 지원일 경우.
    - iPad Screenshots must be .jpeg, .jpg, .tif, .tiff, or .png file that is 1024x768, 1024x748, 768x1024, 768x1004, 2048x1536, 2048x1496, 1536x2048 or 1536x2008 pixels, at least 72 DPI, and in the RGB color space.
  - Routing App Coverage File (Optional)

_Save_ 버튼 클릭.

### 바이너리 등록 및 검수 신청: App Store
테스트 동안에는 실행파일은 업로드 하지 않아도 됨.

1. App 정보화면(Manage Your Applications 페이지에서, 등록된 App Icon을 클릭하면 보이는 페이지)에서 _View Details_ 클릭.
1. _Ready to Upload Binary_ 버튼 클릭.

이제 Xcode를 통해서 바이너리를 등록할 수 있다.
1. Xcode에서 배포용 Archive 생성.
1. Organizer의 Archives 탭에서 등록할 Archive를 선택.
1. _Validate..._ 버튼을 클릭해서 유효성 체크.
1. 체크가 통과되면, _Distribute..._ 버튼을 클릭해서 App Store에 업로드.

바이너리를 업로드 상태는 **iTunes Connect** > **Manage Your Applications** 에서 확인.

바이너리 업로드가 완료되면 자동으로 검수(Review) 대기 과정까지 진행된다.

## In-App 결제

[iOS In-App Purchase](ios_inapp.md) 참조.

## 문제 및 해결 ==

[iOS 문제 및 해결](ios_faq.md) 참조

## iOS App 개발 ==

[iOS App 개발](ios_dev.md) 참조.
