# Android

## Version History

[참고](https://ja.wikipedia.org/wiki/Android%E3%81%AE%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E5%B1%A5%E6%AD%B4)

| API Level | Android Ver. | Code Name |
| 8  | 2.2.x | Froyo |
| 9  | 2.3 | Gingerbread |
| 10 | 2.3.3 | Gingerbread |
| 11 | 3.0 | Honeycomb |
| 12 | 3.1 | Honeycomb |
| 13 | 3.2 | Honeycomb |
| 14 | 4.0 | Ice Cream Sandwich |
| 15 | 4.0.3 | Ice Cream Sandwich |
| 16 | 4.1 | Jelly Bean |
| 17 | 4.2 | Jelly Bean |
| 18 | 4.3 | Jelly Bean |
| 19 | 4.4 | KitKat |
| 20 | 4.4W | KitKat Wear |
| 21 | 5.0 | Lollipop |
| 22 | 5.1 | Lollipop |
| 23 | 6.0 | Marshmallow |
| 24 | 7.0 | Nougat |
| 25 | 7.1.1 | Nougat |
| 26 | 8.0 | Oreo  |
| 27 | 8.1 | Oreo  |
| 28 | 9.0 | Pie   |
| 29 | 10 | Q |
| 30 | 11 | R |
| 31 | 12 | S |

## 마켓별 상품 링크
### Google Play

http://developer.android.com/distribute/tools/promote/linking.html

* Web Site: ```http://play.google.com/store/apps/details?id=<package_name>```
* Android App: ```market://details?id=[패키지명]```

## 마켓

### Google Play

- [Google Play In-App 결재](googleplay_inapp.md)
  - 참조: http://developer.android.com/guide/google/play/billing/billing_integrate.html
  - 앱 등록: https://play.google.com/apps/publish/

#### APK 확장 파일

- 참조
  - http://d.hatena.ne.jp/kinokorori/20130710/1373467687
  - http://developer.android.com/google/play/expansion-files.html

구글 플레이에서 업로드 가능한 apk가 50MB 이하로 제한이 되어 있기에, 추가 데이터를 확장 데이터 형식(obb 포맷)으로 업로드 해야 한다. (zip 형식도 가능하지만, 확장자는 obb 여야 한다)

단 obb 파일 이름은 다음과 같은 형식으로 되어 있어야 한다.
```[main | patch].[versionCode].[package-name].obb```

**APK 100MB 지원**<br/>
Android 4.0 이상만 지원하는 경우, Google Play에 APK를 100MB까지 올릴 수 있다.

## Android 개발

- [Android 개발](andorid_dev.md)
- [Android Studio](andorid_studio.md)
- [Unity3D와 Android 연동하기](/unity/unity_android.md)
- [Android NDK](ndk.md)
- [Android Wear](android_wear.md)

## 문제 및 해결 (Install 관련)

### INSTALL_FAILED_DUPLICATE_PERMISSION perm=${applicationId}.C2D_MESSAGE
1. Go to Settings
1. Apps
1. At bottom of the list you will find YourApp with a "NOT INSTALLED" Tag
1. Open
1. Click on OptionMenu and Select "Uninstall for all Users"

### 패키지 구문분석 오류
APK 설치 중에 패키지 구문분석 오류가 뜨는 경우.

원인은 다음 중 하나:
1. APK 파일을 완전히 다운 받지 못했거나 손상됨.
1. '알 수 없는 소스에서 앱 설치' 옵션이 꺼짐.
1. 지원되지 않는 OS 버전 혹은 하드웨어 스펙.
