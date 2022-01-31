# Xcode

## Debug

### Keys

* Step Over: F6
* Step Into: F7

## 프로젝트 설정
### 개발언어 변경
xxxxxxx.xcodeproj 안에 있는 project.pbxproj 파일에서 **developmentRegion** 항목을 검색한다.

기본 언어코드가 영어(en)로 되어 있는데, 이걸 다른 언어 코드로 변경할 수 있다. (ex. ko, ja 등)

## 문제 및 해결

### 빌드가 실행이 안될 때
> The certificate used to sign **APP_NAME** has either expired or has been revoked. An updated certificate is required to sign and install the application.

1. macOS에서 키체인 액세스 프로그램 실행
1. (기간이 지났거나 해서) 무효화된 인증서를 삭제
1. Xcode에서, 클린 한 다음에 다시 빌드
