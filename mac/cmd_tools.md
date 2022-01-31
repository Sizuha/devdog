# macOS Command Line Tools

## Homebrew

* http://mxcl.github.com/homebrew/

### 설치 

```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"</nowiki>
```

### 패키지 설치/제거(예)

```
$ brew install wget
$ brew uninstall wget
```

### 설치된 패키지 확인(예)

```
$ cd /usr/local
$ find Cellar
Cellar/wget/1.12
Cellar/wget/1.12/bin/wget
Cellar/wget/1.12/share/man/man1/wget.1

$ ls -l bin
bin/wget -> ../Cellar/wget/1.12/bin/wget
```

### 문제 및 해결
OS X Yosemite에서 다음과 같은 에러가 나올 때,

> /System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby: bad interpreter: No such file or directory<br/>
/usr/local/bin/brew: line 21: /usr/local/Library/brew.rb: Undefined error: 0

다음과 같이 수정한다.
```
nano /usr/local/bin/brew
 
--- #!/System/Library/Frameworks/Ruby.framework/Versions/1.8/usr/bin/ruby -W0
+++ #!/System/Library/Frameworks/Ruby.framework/Versions/Current/usr/bin/ruby -W0
```

## rename
### 설치 

```
$ brew install rename
```

### 사용법

http://plasmasturm.org/code/rename/

``` 
rename {변환패턴 ...} 변환대상(files)
```

변환 패턴 옵션
- -x : 확장자를 제거한다
- -X : 확자자를 유지한다
- -a추가할문자열 : 파일 이름 뒤에 문자열을 추가한다. -X와 함께 사용되지 않으면 확장자 뒤에 문자열이 추가되므로 주의. 
- -A추가할문자열 : 파일 이름 앞에 문자열을 추가한다.
- -d제거할문자열 : 지정된 문자열을 제거한다. 처음에 발견되는 것만 제거하는 듯.
- -D제거할문자열 : 지정된 문자열을 모두 제거한다.

### Examples
foo を bar に、hello を ciao に変える場合はこんな感じです。
```
$ rename -s foo bar -s hello ciao *
```
正規表現で置換。
```
$ rename 's/\.htm$/.html/' *.htm
```
もちろん後方参照も使えます。
```
$ rename 's/img-(\d*).jpg/foo-$1-bar.jpg/' *
```

## 파일 정보
### Audio File Info
```
$ afinfo {파일명}
```

### 파일 비교(diff)
```
$ diff 파일들 ...
```
텍스트 파일을 행 단위로 비교한다. 다른 부분이 있는 경우에만 결과에 표시된다.
