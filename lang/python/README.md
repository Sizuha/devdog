# Python

## 설정

### MySQL 지원

http://sourceforge.net/projects/mysql-python/

```
tar xfz MySQL-python-x.y.z.tar.gz
cd MySQL-python-x.y.z
  
# edit site.cfg if necessary
 
python setup.py build
sudo python setup.py install # or su first
```

Mac OS X에서는 다음과 같이 라이브러리 경로를 지정해 줘야한다. (.bash_profile 등에 적용)
```
export DYLD_LIBRARY_PATH=/usr/local/mysql/lib:$DYLD_LIBRARY_PATH
```
또는 다음과 같이 링크를 만들어 둔다.
```
$ sudo ln -s /usr/local/mysql/lib/libmysqlclient.18.dylib /usr/lib/libmysqlclient.18.dylib
```
 
**DYLD_LIBRARY_PATH**를 추가해도, sudo 명령을 쓸때는 이 설정이 무시되면서 아래와 같은 메세지가 나온다. 
> dyld: DYLD_ environment variables being ignored because main executable (/usr/bin/sudo) is setuid or setgid

그냥 ln 명령으로 링크를 걸어두는게 속편하겠다.

#### virtualenv 환경에서

virtualenv에서는 직접 \_mysql.so 안에서 참조되는 libmysqlclient.18.dylib 경로를 변경해 주는 방법으로 해결했다.

- 참고: http://stackoverflow.com/questions/6383310/python-mysqldb-library-not-loaded-libmysqlclient-18-dylib

```
$ otool -L /opt/local/lib/mysql55/mysql/libmysql/libmysqlclient.18.dylib
/opt/local/lib/mysql55/mysql/libmysql/libmysqlclient.18.dylib:
  libmysqlclient.18.dylib (compatibility version 18.0.0, current version 18.0.0)
  /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 169.3.0)
```
\_mysql.so에서 libmysqlclient.18.dylib는 경로가 지정되어 있지 않다.

다음 명령으로 경로를 직접 지정.
```
$ sudo install_name_tool -change libmysqlclient.18.dylib /opt/local/lib/mysql55/mysql/libmysqlclient.18.dylib /Users/invite_imac/.virtualenvs/mypython/lib/python2.7/site-packages/_mysql.so
```

### 확인
#### 설치된 Python 경로 확인

```python
>>> import sys
>>> print sys.prefix
```

## 문법
[Python 문법](python_syntax.md) 참조

## 날짜/시간
date, time, datetime 객체 사용.

### 날짜 시간 포맷팅

```python
time.strftime()
```

C함수를 쓰기 때문에 유니코드를 전달할 때는 포맷팅 문자열을 utf-8로 인코딩을 해야함.
```python
time.strftime(u'%d\u200f/%m\u200f/%Y %H:%M:%S'.encode('utf-8'), t).decode('utf-8')
```

### 현재 날짜/시간

```python
from datetime import datetime

datetime.today()
datetime.now([tz])
```

### 요일 확인

```python
 datetimeObj.weekday()
```
Return the day of the week as an integer, where Monday is 0 and Sunday is 6. The same as self.date().weekday().

```python
datetimeObj.isoweekday()
```
Return the day of the week as an integer, where Monday is 1 and Sunday is 7. The same as self.date().isoweekday().

### Sleep

```python
import time
time.sleep(sec)
```

## CSV
### Read

```python
import csv

with open('some.csv', 'rb') as f:
    reader = csv.reader(f)
    for row in reader:
        print row
```

### Write

```python
import csv

with open('filename.csv', 'w') as f:
    writer = csv.writer(f, lineterminator='\n')
    writer.writerow(list)
    writer.writerows(array2d)
```

## JSON

http://docs.python.org/2/library/json.html

### JSON Encoding

```python
import json

json.dumps(data)
json.dumps(data, sort_keys=True)
```

### JSON Decoding

```python
import json

data = json.loads(json_string)
```

## Http, URL
### GET

```python
import urllib

url = 'http://dna.daum.net'
data = urllib.urlopen(url).read()
```

```python
import urllib2
try:
  data = urllib2.urlopen(url).read()
except urllib2.HTTPError, e:
  print "HTTP error: %d" % e.code
except urllib2.URLError, e:
  print "Network error: %s" % e.reason.args[1]
```

인증이 필요한 경우
```python
import urllib2

url = 'http://www.abc.com/index.html'
username = 'user'
password = 'pass'
p = urllib2.HTTPPasswordMgrWithDefaultRealm()

p.add_password(None, url, username, password)

handler = urllib2.HTTPBasicAuthHandler(p)
opener = urllib2.build_opener(handler)
urllib2.install_opener(opener)

page = urllib2.urlopen(url).read()
```

### POST

```python
import urllib

url = "http://apis.daum.net/search/knowledge?&;output=xml&q=OpenAPI"

apikey = "DAUM_SEARCH_DEMO_APIKEY"
output = "xml"
q = "OpenAPI"

params = urllib.urlencode({
  'apikey': apikey,
  'output': output,
  'q': q
})

data = urllib.urlopen(url, params).read()
```

## OS
### 명령행 인수

```python
import sys

argc = len(sys.argv)

print 'argument 1: ' + sys.argv[1]
print 'argument 2: ' + sys.argv[2]
# . . .
```

### 현재 파일의 경로 가져오기

```python
import os.path

os.path.dirname(__file__)
```

### Command 실행

```python
import subprocess
subprocess.call("command1")
subprocess.call(["command1", "arg1", "arg2"])
```

## IO

### File IO

```python
f = open("file.name", "r", encoding="utf-8")
s = f.read()
f.close()
```

#### 파일 존재여부 확인

```python
import os.path
os.path.exists(path)
```

#### 파일 및 디렉토리 목록 

```python
for root, dirs, files in os.walk(directory):
    for file in files:
        if file.endswith('.txt'):
            print file
```

#### 파일 이름만 가져오기

```python
>>> print os.path.splitext(os.path.basename("hemanth.txt"))[0]
hemanth
```

## Random

```python
>>> import random
>>> random.random()
0.90389642027948769

>>> random.randrange(1,7)
6

>>> abc = ['a', 'b', 'c', 'd', 'e']
>>> random.shuffle(abc)
>>> abc
['a', 'd', 'e', 'b', 'c']

>>> abc
['e', 'd', 'a', 'c', 'b']
>>> random.choice(abc)
'a'
```
