# MySQL

## PATH 설정
다음 경로를 PATH에 추가.
```
/usr/local/mysql/bin
```

## MySQL을 UTF-8로 설정=

우선 **/etc/my.cnf**가 필요하다. 

_/usr/local/mysql/support-files/_ 경로에서 _my-huge.cnf_, _my-large.cnf_, _my-medium.cnf_, _my-small.cnf_ 파일 중 하나를 **/etc/my.cnf**로 복사.

```
sudo cp /usr/local/mysql/support-files/my-huge.cnf /etc/my.cnf
```

**/etc/my.cnf** 내용에 다음 항목을 추가/수정.

```
[mysqld]
character-set-server=utf8

[mysql]
default-character-set=utf8
```

설정 저장 후, MySQL 재시작.
설정 확인은 다음과 같이 한다.
```
mysql> show variables like "%character%";
mysql> show variables like "%collation%";
```

## DB 서버 시작

```
sudo /usr/local/mysql/support-files/mysql.server start
```

## 관리

### User 관리

```
$ mysql -u root -pPASSWORD
mysql> use mysql;
```

#### 사용자 추가
내부에서 접속하는 사용자
```
mysql> insert into user (host, user, password) values ('localhost', 'dbuser', password('xxxxxxxx'));
```

외부에서 접속하는 사용자
```
mysql> insert into user (host, user, password) values ('%', 'dbuser', password('xxxxxxxx'));
```

#### 권한 설정
##### 권한 보기

```
mysql> show grants for USER@'%';
```

##### 권한 지정
```
mysql> grant select, insert, update, delete on DB_Name.TableName to USER@'%';
```

와일드 카드 사용:
```
mysql> grant select, insert, update, delete on DB_Name.* to USER@'%';
```

### root 암호 설정
#### mysqladmin 이용

```
$ mysqladmin -u root -p password
```

#### update문 이용
```
$ mysql -u root 
mysql> update user set password = password('새로운_암호') where user = 'root';
mysql> flush privileges;
```

MySQL 5.7 이상인 경우,
```
$ mysql -u root 
mysql> UPDATE mysql.user SET authentication_string=PASSWORD('my-new-password') WHERE User='root';
mysql> FLUSH PRIVILEGES;
```

#### set password를 이용

```
mysql> set password for root = password('새로운_암호');
```

#### root 암호 리셋

```
$ sudo /usr/local/mysql/support-files/mysql.server stop
$ sudo /usr/local/mysql/bin/mysqld_safe --skip-grant-tables
```

이후 다른 터미널 창에서,
```
$ mysql -u root
```

update문을 이용하여 root 암호를 설정한다.

MySQL을 다시 시작.
```
$ sudo /usr/local/mysql/support-files/mysql.server start
```

이후에, mysql 콘솔에서 만약 다음과 같은 에러가 나온다면...
```
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement. 
```

다음과 같이 암호를 다시 설정해 준다.
```
mysql> SET PASSWORD = PASSWORD('your_new_password');
```

#### 테스트

```
$ mysql -u root -p
```

### 로그인

```
$ mysql -u 계정이름(ex. root) -p
```

### DB 관리

#### DB 보기
데이터베이스 목록 표시.
```
mysql> show databases;
```

DB 열기.
```
mysql> use DB이름;
```

#### DB 생성

```
mysql> create database DB이름;
```
문자열 인코딩을 utf-8을 기본으로 하는 DB 생성.
```
mysql> CREATE DATABASE <dbname> CHARACTER SET utf8;
```

#### Dump
DB 백업
```
$ mysqldump -u {user} -p {database-name} > {dump-output}.sql
```

DB 복원
```
$ mysql -u {user} -p {database-name} < {dump-output}.sql
```

### Table 관리
테이블 구조 보기
```
mysql> DESC 테이블이름;
mysql> DESCRIBE 테이블이름;
mysql> EXPLAIN 테이블이름;
```

## SQL

### INSERT

```sql
INSERT  INTO  テーブル名  VALUES (‘値1′ [ , ‘値2’ ]・・・);
```

### UPDATE

```sql
UPDATE テーブル名 SET フィルド=値[, ...] WHERE 条件;
```

### DELETE
테이블에서 조건에 맞는 행을 삭제한다.
```sql
DELETE FROM テーブル名 WHERE 条件;
```
WHERE절을 생략하면 테이블 안의 모든 행이 삭제된다.


### MySQL: 함수

#### 날짜/시간

- now() : 현재 날짜와 시간
- date() : 날짜 부분만 추출한다

| Nam | Description |
| - | - |
|ADDDATE() | Add dates |
|ADDTIME() | Add time |
|CONVERT_TZ() | Convert from one timezone to another |
|CURDATE() | Return the current date |
|CURRENT_DATE(), CURRENT_DATE | Synonyms for CURDATE() |
|CURRENT_TIME(), CURRENT_TIME | Synonyms for CURTIME() |
|CURRENT_TIMESTAMP(), CURRENT_TIMESTAMP | Synonyms for NOW() |
|CURTIME() | Return the current time |
|DATE_ADD() | Add two dates |
|DATE_FORMAT() | Format date as specified |
|DATE_SUB() | Subtract two dates |
|DATE() | Extract the date part of a date or datetime expression |
|DATEDIFF() | Subtract two dates |
|DAY() | Synonym for DAYOFMONTH() |
|DAYNAME() | Return the name of the weekday |
|DAYOFMONTH() | Return the day of the month (1-31) |
|DAYOFWEEK() | Return the weekday index of the argument |
|DAYOFYEAR() | Return the day of the year (1-366) |
|EXTRACT | Extract part of a date |
|FROM_DAYS() | Convert a day number to a date |
|FROM_UNIXTIME() | Format date as a UNIX timestamp |
|HOUR() | Extract the hour |
|LAST_DAY | Return the last day of the month for the argument |
|LOCALTIME(), LOCALTIME | Synonym for NOW() |
|LOCALTIMESTAMP, LOCALTIMESTAMP() | Synonym for NOW() |
|MAKEDATE() | Create a date from the year and day of year |
|MAKETIME | MAKETIME() |
|MICROSECOND() | Return the microseconds from argument |
|MINUTE() | Return the minute from the argument |
|MONTH() | Return the month from the date passed |
|MONTHNAME() | Return the name of the month |
|NOW() | Return the current date and time |
|PERIOD_ADD() | Add a period to a year-month |
|PERIOD_DIFF() | Return the number of months between periods |
|QUARTER() | Return the quarter from a date argument |
|SEC_TO_TIME() | Converts seconds to 'HH:MM:SS' format |
|SECOND()  | Return the second (0-59) |
|STR_TO_DATE() | Convert a string to a date |
|SUBDATE() | When invoked with three arguments a synonym for DATE_SUB() |
|SUBTIME() | Subtract times |
|SYSDATE() | Return the time at which the function executes |
|TIME_FORMAT() | Format as time |
|TIME_TO_SEC()   | Return the argument converted to seconds |
|TIME() | Extract the time portion of the expression passed |
|TIMEDIFF() | Subtract time |
|TIMESTAMP() | With a single argument, this function returns the date or datetime expression. With two arguments, the sum of the arguments |
|TIMESTAMPADD() | Add an interval to a datetime expression |
|TIMESTAMPDIFF() | Subtract an interval from a datetime expression |
|TO_DAYS() | Return the date argument converted to days |
|UNIX_TIMESTAMP() | Return a UNIX timestamp |
|UTC_DATE() | Return the current UTC date |
|UTC_TIME() | Return the current UTC time |
|UTC_TIMESTAMP() | Return the current UTC date and time |
|WEEK() | Return the week number |
|WEEKDAY() | Return the weekday index |
|WEEKOFYEAR()  | Return the calendar week of the date (1-53) |
|YEAR() | Return the year |
|YEARWEEK() | Return the year and week |
