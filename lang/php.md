# PHP

## PHP.ini 설정

### 파일 업로드

**File Uploads** 주석 섹션을 찾아서 다음 항목들을 설정한다.

파일 업로드 활성화
```
file_uploads = On
```

업로드 가능한 최대 용량
```
upload_max_filesize = 2M
```

## 커맨드 라인 실행

커맨드 라인에서 스크립트 파일 실행
```
 $ php [-f] xxx.php [[--] 인수들...]
``` 
-f 옵션을 사용할 경우, -- 다음에 인수들이 와야 한다.

### Console

```
$ php -a
```

### 쉘 스크립트
스크립트 파일 맨 윗줄에 다음과 같이 적는다.
```bash
#!/usr/bin/php
```
그리고 스크립트 파일에 실행 속성을 부여하면, UNIX 쉡 스크립트처럼 실행이 가능.

### 인수 처리
전역변수 **$argc**, **$argv**를 사용.
- $argc : 인수 갯수
- $argv : 인수들을 담은 문자열 배열.
  - $argv\[0] ~ $argv\[n]

## 파일 처리

### 확장자 추출

```php
// 방법1
// return: 성공이면 확장자 문자열, 실패(확장자가 없는 경우)면 false를 반환.
function file_ext($file) 
{
  return substr(strrchr($file,"."),1);
}


// 방법2: pathinfo() 사용
$filename = "/upload/flower.image.jpg";
$path = pathinfo($filename);
$ext = $path['extension'];  
```

## 문제 및 해결
### MySQL No such file or directory 에러
mysqli_connect() 등으로 MySQL DB에 접속하려 할 때 에러가 발생하는 경우.

MySQL의 socket 파일의 경로가 php와 mysql 설정이 서로 맞지 않는 경우에 발생할 수 있다.

커맨드 라인에서 PHP와 MySQL 설정을 비교해 본다.
```
$ php --ri mysqli
mysqli

MysqlI Support => enabled
Client API library version => mysqlnd 5.0.11-dev - 20120503 - $Id: bf9ad53b11c9a57efdb1057292d73b928b8c5c77 $
Active Persistent Links => 0
Inactive Persistent Links => 0
Active Links => 0

Directive => Local Value => Master Value
mysqli.max_links => Unlimited => Unlimited
mysqli.max_persistent => Unlimited => Unlimited
mysqli.allow_persistent => On => On
mysqli.default_host => no value => no value
mysqli.default_user => no value => no value
mysqli.default_pw => no value => no value
mysqli.default_port => 3306 => 3306
mysqli.default_socket => /var/lib/mysql/mysql.sock => /var/lib/mysql/mysql.sock
mysqli.reconnect => Off => Off
mysqli.allow_local_infile => On => On
```

```
$ mysqladmin version
mysqladmin  Ver 8.42 Distrib 5.5.29, for osx10.6 on i386
Copyright (c) 2000, 2012, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version    5.5.29-log
Protocol version  10
Connection    Localhost via UNIX socket
UNIX socket   /tmp/mysql.sock
Uptime:     7 min 13 sec
```

틀린 경우는, php.ini에서 xxx.default_socket 부분을 다 찾아서 경로를 고쳐준다.

그리고 MySQL을 다시 시작한다.
```
$ sudo /usr/local/mysql/support-files/mysql.server stop
$ sudo /usr/local/mysql/support-files/mysql.server start
```
