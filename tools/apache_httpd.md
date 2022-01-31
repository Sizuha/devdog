# Apache HTTP Server

## 설치 경로
Mac OS X에서 기본으로 설치된 apache2 경로
```
/usr/libexec/apache2
```

## HTTP 서버 시작 및 종료

```
시작
$ sudo apachectl start

중지
$ sudo apachectl stop

재시작
$ sudo apachectl restart
``` 

### 부팅할 때 자동 시작: macOS
부팅시 자동으로 시작되게 등록
```
$ sudo launchctl load -w /System/Library/LaunchDaemons/org.apache.httpd.plist
```

등록 해제
```
$ sudo launchctl unload /System/Library/LaunchDaemons/org.apache.httpd.plist
```

## 설정
설정파일 경로
```
$ cd /etc/apache2/users
$ sudo nano 사용자이름.conf
```

## 웹 공유 폴더 설정
사용자 웹 사이트 루트는 **사용자_계정홈/사이트**(\~/Sites)

사이트 루트에 conf 파일을 생성해 줘야 한다.
```
cd /etc/apache2/users
cp Guest.conf 사용자계정명.con
sudo nano 사용자계정명.conf
```

그리고 conf 파일 내용을 다음과 같이 수정.
```xml
### For OSX 10.10 Apache 2.4 ###
<Directory "/Users/username/Sites/">
AllowOverride All
Options Indexes MultiViews FollowSymLinks
Require all granted
</Directory>
```

```xml
### For OSX 10.9 Apache 2.2 ###
<Directory "/Users/username/Sites/">
    Options Indexes MultiViews FollowSymLinks
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
```

**/etc/apache2/httpd.conf** 파일에서 다음과 같이 수정
```
#LoadModule php5_module libexec/apache2/libphp5.so
```
위 부분을, 아래와 같이 주석을 제거
```
LoadModule php5_module libexec/apache2/libphp5.so
``` 

그리고 또 다음 항목을 수정
```xml
<Directory "/Library/WebServer/Documents">
    ...
    AllowOverride All
    ...
```

## MySQL과 PHP 연동

### 문제
MySQL 서버가 동작 중일 때, tmp/mysql.sock 파일이 생성된다.

그런데 PHP 설정이 **var/mysql/mysql.sock** 으로 되어 있어서 PHP에서 MySQL 접속이 불가능한 경우가 있다.

### 해결

**/etc/php.ini.default** 파일에서 **var/mysql/mysql.sock**으로 되어 있는 부분(세 군데)을 전부 **tmp/mysql.sock**으로 변경.

그리고 다음과 같이 php.ini 파일을 생성.
```
sudo cp /etc/php.ini.default /etc/php.ini
```

## mod_wsgi

- https://code.google.com/p/modwsgi/
  - 여기서 빌드된 바이너리를 얻을 수 있다.

소스를 직접 컴파일 할 경우, (macOS)
```
$ tar xvfz mod_wsgi-X.Y.tar.gz  # x,y는 버전
$ cd mod_wsgi-X.Y
$ ./configure --with-apxs=/usr/sbin/apxs --with-python=/usr/bin/python
$ make
$ sudo make install
```

### make error

> env: /Applications/Xcode.app/Contents/Developer/Toolchains/OSX10.9.xctoolchain/usr/bin/cc: No such file or directory

이런식의 에러가 나온다면,
```
$ sudo ln -s /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain /Applications/Xcode.app/Contents/Developer/Toolchains/OSX10.9.xctoolchain
```

### apache 설정

**/etc/apache2/httpd.conf**에 다음 설정을 추가.
```
LoadModule wsgi_module     libexec/apache2/mod_wsgi.so
```
아파치 서버를 재시작 한다.
