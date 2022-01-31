# macOS

## Install
### USB 부팅/설치 디스크 만들기

* http://ottan.xyz/macos-sierra-bootable-disk-5007/
* https://support.apple.com/ja-jp/HT201372

간단히 설명하자면,
1. macOS Installer를 AppStore에서 다운 받은 다음
1. USB (8GB 이상)를 포맷하고
1. 콘솔창에서 다음과 같이 createinstallmedia 명령을 넣는다.

```
sudo /Applications/Install\ macOS\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/UNTITLED --applicationpath /Applications/Install\ macOS\ Sierra.app --nointeraction
```

#### USB 디스크를 이용한 설치

1. USB 설치디스크를 넣고
1. Mac을 부팅, 이때 Option(Alt)를 계속 누르고 있으면 시동 디스크 설치화면이 나온다.
1. 이때 USB 설치 디스크를 선택.

## PATH

### 주요 디렉토리 경로

* hosts: /private/etc/hosts

#### Web Server

- Web Root: /Library/WebServer/Documents
- User Web Root: /Users/사용자_이름/Sites
- Apache HTTPD 설정:
  - 전체 설정: /etc/apache2/httpd.conf 
    - 혹은 /etc/apache2/httpd.conf.pre-update
  - 사용자별 설정: /etc/apache2/users/사용자이름.conf

### PATH 설정
bash shell 기준.

임시로 PATH 경로를 추가할 때:
```bash
export PATH=$PATH:추가할PATH
```

영구적으로 PATH를 설정하고자 할 때는 **/Users/사용자_이름/.bash_profile** 파일에 export 명령을 추가/수정한다.

* .bash_profile 확인은 해당 경로에서 '''ls -a''' 로.

## Command Line Tools

[Command Line Tools](cmd_tools.md) 참조.

## Remote Desktop
### 화면공유(VNC)
Mac OS X는 VNC기능을 내장하고 있다.

#### VNC 접속
원격 관리 대상의 Mac에서, 환경설정 > 원격관리 옵션을 활성화 시킨다.

Finder 등에서 다음 형식의 URL로 접속.
```
vnc://{remote-address}/
```

## 콘솔

### rm
디렉토리 삭제(하위 내용까지 포함)
```
$ rm -rf {dir}
```

### find
```
$ find . type f -name "*.xxx"
```
현재 경로에서 하위 디렉토리를 포함하여 "\*.xxx"인 파일들을 모두 찾아낸다. xargs 명령을 파이프로 전달하면 찾아낸 파일들을 대상으로 원하는 작업을 할 수 있다.
```
$ find . type f -name "*.xxx" | xargs -I{} mv {} {}.temp
```

### xargs
단독으로 쓰이기 보단, 다른 명령을 표준출력 결과를 인자로 받아서 새로운 작업을 하고자 할 때 사용.
```
xargs -I{} command {}
```
**-I** 옵션으로 지정한 문자 **{}**를 명령행 인자 전체로 치환하게 해준다.

## Tips
### 터미널로부터 알림 받기

tput 명령을 사용해 터미널이 비활성 상태일 때 Dock에 있는 터미널에 알림을 보낼 수 있다.

수행하고자 하는 명령을 입력한 후 && tput bel 이라고 뒤에 덧붙여주기만 하면 된다.

예를 들어 구글 서버에 핑을 5번 보내는 작업을 한다고 할 때
```
ping -c 5 www.google.com && tput bel
```
이라고 한다면, 핑을 보내는 일을 하는 동안 웹서핑 등 다른 작업을 하고 있다보면, Dock에서 터미널이 통통 튀어 오르면서 알림을 보낸다. 이때 Dock을 보면 터미널 아이콘에 숫자배지가 붙어 있다.


### 디스크 이미지 관리
대상 폴더 이하의 내용들을 dmg로 묶는다.
```
hdiutil create "{output}.dmg" -volname "디스크 볼륨 이름" -srcfolder "대상 폴더" -ov -scrub
```

옵션
- -ov : 출력파일(dmg)이 이미 존재한는 경우, 새로 덮어 쓴다.
- -scrub : 임시 파일들(.DS_Store 등)은 디스크 이미지에 포함시키지 않는다.

### 자동 시작 프로그램 설정

로그인 할 때, 자동으로 실행시킬 프로그램들을 설정하는 방법.

1. [시스템 환경설정] > [사용자 및 그룹] 으로 들어가서 현재 사용자를 선택한 다음에 ''로그인 항목''을 선택.
1. + - 버튼으로 프로그램을 추가/삭제 할 수 있지만 .app 패키지 안에 있는 어플을 등록 하려면 ''Finder'' 에서 직접 끌어다 놓기로 해야 한다.

### 숨김파일 표시/비표시
표시
```
$ defaults write com.apple.finder AppleShowAllFiles -bool true
$ killall Finder
```
비표시
```
$ defaults write com.apple.finder AppleShowAllFiles -bool false
$ killall Finder
```

### Finder
#### Cut and Paste
1. Command + C
1. Command + Option + V

Windows에서 처럼 자른(Cut) 다음에 붙여넣는게 아니라, 우선 복사(Copy)한 다음에 붙여넣을 때 옵션키를 누르것이 포인트.

## 문제 및 해결
### 압출 풀때 주의

_tar_ 종류의 파일은 대게 UNIX 퍼미션 정보가 담겨있다. 이런건 다른 압축 유틸리티를 쓰지 말고 기본으로 제공하는 **아카이브 유틸리티**를 사용할 것!

### 런치패드 이상 동작
런치패드의 동작이 이상하다 싶을 때, 다음 명령으로 런치패드를 재실행 시킬 수 있다.
```
$ killall Dock
```
