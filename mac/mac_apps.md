# macOS App 활용

## Remote Desktop
### 화면공유(VNC)
Mac OS X는 VNC기능을 내장하고 있다.

#### VNC 접속
원격 관리 대상의 Mac에서, 환경설정 > 원격관리 옵션을 활성화 시킨다.

Finder에서 Command + K 후, ```vnc://{remote-address}/``` 접속.


## Finder
### Cut and Paste
1. Command + C
1. Command + Option + V

Windows에서 처럼 자른(Cut) 다음에 붙여넣는게 아니라, 우선 복사(Copy)한 다음에 붙여넣을 때 옵션키를 누르것이 포인트.

## Mail

### 메일 링크

URL 형식: ```message://<Message-ID>``` <br/>
※주의! < > 까지 입력해야 한다.

#### 메세지 ID 획득 방법

일단 macOS 메일 앱으로, 링크르 걸고 싶은 메일을 열람한 상태에서,

**(1) Drag & Drop**
메일을 ___Text Edit(テキストエディット)___ 혹은 ___캘린터___ 앱으로 끌어 놓는다. 그러면 자동으로 message 링크가 작성됨.

**(2) 소스 보기**
1. 메일 앱 메뉴 → 표시（表示） → 메세지（メッセージ） → 소스（ソース）
   1. 혹은 Option + Command + U
1. ```Message-ID:``` 키워드로 검색

