# Amazon Web Service

[공식 사이트](https://aws.amazon.com/)

## 서비스
### 서버: EC2
주의할 점:
* 인증서 파일({user-name}.pem)을 반드시 잘 보관해 둔다.
* 서버 인스턴스가 생성된 후에 '''Elastic IP'''를 등록/연결 해 둘 것.
* 앞으로 서버에 접속할 때 '''Elastic IP'''를 이용할 수 있다.

#### 보안설정
HTTP 접속허융:
```
インバウンド 
HTTP TCP 80 0.0.0.0/0
```
SSH/SFTP 허용:
```
インバウンド 
SSH TCP 22 0.0.0.0/0
```
MySQL 허용: 
```
インバウンド 
MYSQL/Aurora TCP 3306 0.0.0.0/0
```

#### SSH

```
$ chmod 400 {user-name}.pem
$ ssh -i "{user-name}.pem" {パブリック DNS}
```

#### SFTP
{user-name}.pem 인증서를 이용해서 SFTP로 접속 할 수 있다.

### DB: RDS
#### MySQL

```
$ mysql -u {db-user} -p -h {エンドポイント}
```

## Amazon Linux 설정
### Apache HTTPD
#### Install

```
$ sudo yum -y install httpd
$ sudo chkconfig httpd on
$ chkconfig
```

#### Config

```
$ sudo nano /etc/httpd/conf/httpd.conf
```
아파치에서 PHP관련 설정은 하지 않아도 됨.

#### 관리

```
$ sudo /etc/init.d/httpd start
$ sudo /etc/init.d/httpd stop
$ sudo /etc/init.d/httpd restart
```

### PHP
#### Install 

```
$ sudo yum -y install php
```

#### Config 

```
$ sudo nano /etc/httpd/conf.d/php.conf

short_open_tag = On
date.timezone="Asia/Tokyo"
expose_php = Off
```
