# Python 문법

## 자료형

### 숫자
int형
```python
>>> print 123
```

long형
```python
>>> print 123L
```

float형
```python
>>> print 123.4
```

복소수형 : 허수(i) 부분을 j문자로 표현한다.
```python
>>> print 1+2j
```

### 문자열
C 문자열 스타일과 비슷하다. 백슬러시(\\)로 이스케이프 문자를 넣을 수 있는 것도 마찬가지. 다만 " "와 ' ' 두가지 형식의 따옴표를 모두 사용할 수 있다.
```python
>>> print "문자열"
>>> print '문자열'
```
 
여러줄로 구성되는 문자열의 경우, 따옴표 3개(<nowiki>""" 혹은 '''</nowiki>)로 묶을 수 있다.
```python
>>> print """
... 여러줄
... 문자열
... """
```

문자열 내용은 수정 불가능.

#### 유니코드 문자열
```python
>>> print u'유니코드 문자열'
유니코드 문자열
```

**Python 코드에 유니코드 문자가 포함될 경우**
소스 파일의 첫번째나 두번째 줄에 다음과 같이 적어둔다.
```
# -*- coding: utf-8 -*-
```
유니코드는 왠만하면 utf-8로 인코딩하는게 정신건강에 좋다.

#### Raw String
백슬러시(\\)를 이스케이프 문자로 인식하지 않고 그대로 인식.
```python
>>> print r'\n\r'
\n\r
```

**버그?**
인터프리트 환경에서, 문자열 마지막에 백슬러시(\\)문자가 오는 경우는 제대로 인식하지 못하고 오류를 낸다. 다른 경우는 아직 확인을 못함.

#### Unicode + Raw String

```python
>>> print ur'유니코드\n'
유니코드\n
```

#### 문자열 합치기

```python
>>> print 'help' + ' me'
help me
```

#### 문자열 반복 연산

```python
print "=" * 20
print " Title"
print "=" * 20
```
```
====================
 Title
====================
```

#### 인덱싱

##### 순방향 인덱싱
C처럼 0 base 인덱싱.
```python
>>> sample = '0123456789'
>>> print sample[0]
0
```

##### 역방향 인덱싱
역방향은 1 base가 된다.
```python
>>> sample = '0123456789'
>>> print sample[-1]
9
```

#### 슬라이싱
인덱싱을 범위형으로 지정하면, 문자열에서 부분 문자열을 추출해 낼 수 있다.

**\[시작인덱스:끝인덱스]**
```python
>>> sample = '0123456789'
>>> print sample[1:4]
123
```
시작인덱스 ~ 끝인덱스-1 까지의 문자열이 추출된다.

**\[시작인덱스:]**
```python
>>> sample = '0123456789'
>>> print sample[3:]
3456789
```
시작인덱스부터 마지막까지의 문자열을 추출.

**\[:끝인덱스]
```python
>>> sample = '0123456789'
>>> print sample[:-3]
0123456
```
처음부터 끝인덱스-1 까지의 문자열 추출.

**\[:]**
```python
>>> sample = '0123456789'
>>> print sample[:]
0123456789
```
문자열 전부.

#### Formatting (new)

```python
"format-string".format(var, ...)
```
format string은 C# 스타일로 지정.
```python
 "{0} ... {1} ...".format(v1, v2, ...)
```

#### Formatting (old)
문자열에 % 연산을 쓰면 C의 printf 형식의 포맷팅을 사용할 수 있다.
```python
>>> num = 7
>>> message = "lucky number is %d" % num
>>> print message
lucky number is 7
```

다수의 인자를 포맷팅할 경우, % 다음에 튜플 형태로 인자를 넣어야 한다.
```python
>>> print "Copy from %s to %s." % ("A", "B")
Copy from A to B.
```

##### 포맷 코드

| format | 説明 |
| ------ | ---- |
| %s | 문자열 (String) |
| %c | 문자 한개(character) |
| %d | 정수 (Integer) |
| %f | 부동소수 (floating-point) |
| %o | 8진수 정수 |
| %x | 16진수 정수 |
| %% | %문자 |

#### 문자열 함수

| 함수 | 설명 |
| --- | --- |
| a.upper()	 | 문자열 a를 모두 대문자로 바꾸어 준다. |
| a.count(x) | 문자열 a중 x와 일치하는 것의 갯수를 반환한다. |
| a.find(x) | 문자열 a중 문자 x가 처음으로 나온 위치를 반환한다. 없으면 -1을 반환한다. |
| a.index(x) | 문자열 a중 문자 x가 처음으로 나온 위치를 반환한다. 없으면 에러를 발생시킨다. |
| a.join(s) | s라는 문자열의 각각의 요소 문자사이에 문자열 a를 삽입한다. |
| a.lower() | 문자열 a를 모두 소문자로 바꾸어 준다. |
| a.lstrip() | 문자열 a의 왼쪽 공백을 모두 지운다. |
| a.rstrip() | 문자열 a의 오른쪽 공백을 모두 지운다. |
| a.strip()	| 문자열 a의 양쪽 공백을 모두 지운다. |
| a.replace(s, r) | 문자열 a의 s라는 문자열을 r이라는 문자열로 치환한다. |
| a.split(\[s]) | 문자열 a를 공백으로 나누어 리스트값을 돌려준다. |
| a.swapcase() | 문자열 a의 대문자는 소문자로, 소문자는 대문자로 각각 바꾸어 준다. |

### List
리스트는 \[ ] 안에 배열처럼 열거한다.
```python
>>> a = [1, 2, 3]
>>> print a
[1, 2, 3]
```

리스트에는 어떤 자료형이든 담을 수 있다.

리스트 내용은 수정 가능하다.

#### 리스트 합치기
```python
>>> a = [1, 2, 3]
>>> b = [4, 5, 6]
>>> a + b
[1, 2, 3, 4, 5, 6]
```

#### 리스트 반복
```python
>>> a = [1, 2, 3]
>>> a * 3
[1, 2, 3, 1, 2, 3, 1, 2, 3]
```

#### 인덱싱, 슬라이싱
문자열의 인덱싱과 슬라이싱 방식을 그대로 쓸 수 있다.
```python
>>> a = [1, 2, 3, 4, 5]
>>> b = a[:2]
>>> c = a[2:]
>>> b
[1, 2]
>>> c
[3, 4, 5]
```

#### 함수

| 함수 | 설명 |
| --- | --- |
|a.append(x) | 리스트 a의 마지막에 x추가 |
|a.sort() | 리스트 a를 정렬 |
|a.reverse() | 리스트 a의 순서를 거꾸로 만든다. |
|a.index(x) | 리스트 a에서 x를 찾아서 그 위치 반환 |
|a.insert(i, x) | 리스트 a에서 i 위치에 x 삽입 |
|a.remove(x) | 리스트 a에서 처음 나오는 x 삭제 |
|a.pop() | 리스트 a의 맨 마지막 요소 반환하고 마지막 요소 삭제 |
|a.pop(i) | i위치의 요소를 반환하고 해당 요소를 삭제 |
|a.count(x) | 리스트 a 안에 x가 몇 개 있는지를 반환 |
|a.extend(x) | 리스트 a에 리스트 x를 더함(확장) |

### Tuple
리스트와 거의 동일한 형태지만, 내용 수정이 불가능한 자료형.

#### Tuple 정의

```python
>>> t1 = ()
>>> t2 = (1,)
>>> t3 = (1,2,3)
>>> t4 = 1,2,3
>>> t5 = ('a', 'b', ('ab', 'cd'))
```
내용이 하나만 있는 tuple을 정의할 때 반드시 끝에 콤마(,)를 붙여야 한다.

바깥의 괄호( )는 경우에 따라 생략 할 수 있다.

#### 합치기

```python
>>> t2 = (3, 4)
>>> t1 + t2
(1, 2, 'a', 'b', 3, 4)
```

#### 반복

```python
>>> t2 * 3
(3, 4, 3, 4, 3, 4)
```

#### 인덱싱

```python
>>> t1 = (1, 2, 'a', 'b')
>>> t1[0]
1
>>> t1[3]
'b'
```

#### 슬라이싱

```python
>>> t1 = (1, 2, 'a', 'b')
>>> t1[1:]
(2, 'a', 'b')
```

### Dictionary
사전 자료형은 다음과 같은 형식으로 정의된다.
```
{Key1:Value1, Key2:Value2, Key3:Value3,,,,}
```

Key는 상수형 자료형이어야 한다.

Value는 어떤 자료형이어도 된다.

#### 값 참조
오로지 Key에 의해서만 값을 참조할 수 있다.
```python
>>> a = {1:'a', 2:'b'}
>>> a[1]
'a'
>>> a[2]
'b'
```

#### 키(key) 확인
in 연산자로 딕셔너리에 key가 존재하는지 확인할 수 있다. 결과는 True or Fasle로 반환.
```python
'key' in dic_var
```

#### 함수

| 함수 | 설명 |
| --- | --- |
|a.keys() | 딕셔너리 a의 Key들을 모아놓은 리스트를 돌려준다. |
|a.values() | 딕셔너리 a의 Value들을 모아놓은 리스트를 돌려준다. |
|a.items() | 딕셔너리 a의 (Key, Value)쌍의 터플을 모아놓은 리스트를 돌려준다. |
|a.clear() | 딕셔너리 a의 모든 Key:Value 쌍들을 삭제한다. |
|a.get(x) | 딕셔너리 a의 Key가 x인 것의 Value를 돌려준다. |
|a.has_key(x) | 딕셔너리 a에 x라는 Key가 있는지 조사하여 참, 거짓을 돌려준다. (v2.7) |

### 형변환

#### to Int

```python
int([x[, radix]])
```

여기서 x는 문자열(string), int, float 등의 아무 타입.

## 참/거짓

| 값 | 결과 |
| - | - |
| True | 참
| False | 거짓
|"python" | 참
|"" | 거짓
| \[1, 2, 3] | 참
| \[] | 거짓 
|() | 거짓
|{} | 거짓 
|1 | 참 
|0 | 거짓 
| None | 거짓 

정리: 1과 True와  비어있지 않는 자료형에 대해서 참(True)으로 판정된다.


## 연산자

#### 산술 연산자

```
산술연산: + - * / **
몫: // 
나머지: % 
```

몫과 나머지를 동시에 구하려면
```python
>>> divmod( 5, 3 ) # (1, 2)
```

### 할당 연산자

```
=
+= -= /= *= //= %= **=
```

C언어처럼 += 이나 -= 같은 대입 연산자와 결합된 산술연산을 제공한다. 단, ++ 이나 -- 같은 증감연산은 제공하지 않으니 주의!!

### 비트 연산자

```
& | ^ ~ << >>
```

### 비교 연산자

```
> < >= <= == !=
is
```

### 논리 연산자

```
not, and, or
```

**and**와 **or**가 포함된 논리식은 식의 결과 값을 판정하는데 최종적으로 기여한 객체의 값을 식의 값으로 리턴

### 멤버 연산자

```
in
not in
```

```python
list1 = [0, 1, 2, 3, 4, 5]
print( 3 in list1) # True
print( 7 not in list1) # True
```

문자열과 리스트, 튜플, 딕셔너리 등의 컬렉션에 쓸 수 있다.

## 변수

### 변수 제거

```
del(변수이름)
```

```python
>>> a = 3 
>>> b = 3
>>> del(a)
>>> del(b)
```

### 변수 대입 방법

tuple로 대입
```python
>>> a,b = 'aaa', 'bbb'
>>> a
'aaa'
>>> b
'bbb'
```

list로 대입
```python
>>> [a,b] = [10,20]
>>> a
10
>>> b
20
```

여러 변수에 같은 값을 대입
```python
>>> a = b = 100
>>> a
100
>>> b
100
```

#### list 대입
변수에 리스트를 대입하는 것은 참조 형태로 대입된다.
```python
>>> a = [1,2,3]
>>> b = a
>>> a += [4]
>>> a
[1, 2, 3, 4]
>>> b
[1, 2, 3, 4]
```

#### 리스트 복사

```python
>>> b = a[:]
</syntaxhighlight>
<syntaxhighlight lang="python">
>>> from copy import copy 
>>> b = copy(a)
```

## 제어문
### if
#### if ~ else

```
if 조건문:
    ...
    ...
else:
    ...
    ...
```
이때 주의할 것은, 들여쓰기가 반드시 필요하고 들여쓰는 방식 또한 일치해야 한다.

수행할 문장이 하나일 경우는 다음과 같은 형식으로 쓸 수 있다.
```
if 조건문: ...
else: ...
```

#### if ~ elif ~ else

```
if 조건문:
    ...
    ...
elif 조건문:
    ...
    ...
elif 조건문:
    ...
    ...
...
else:
    ...
    ... 
```

#### if 표현식
C에서의 삼항연산자(a ? b : c)와 비슷한 기능을 하나, 문법에 주의.
```python
 expression_if_true if condition else expression_if_false
```
if와 else가 반드시 같이 나와야 하고, 조건식이 가운데에 온다는 점이 특징.

### while
조건이 참일 동안 반복 실행.

```
while 조건문:
    ...
    ...
```

C에서 처럼, while이나 for 같은 반복문 안에서 **continue**, **break** 키워드를 쓸 수 있다.

### for

list, tuple, string의 요소를 반복
```python
for 변수 in list(or tuple, string):
    ...
    ...
```

dictionary의 요소를 반복
```python
for key, value in dictionary:
    ...
    ...    
```

tuple로 구성된 리스트를 반복
```python
li = [(e1, e2, ...), ...]
for el1, el2, ... in li:
    ...
    ...
```

list안의 list
```python
li = [[e1, e2, ...], ...]
for el1, el2, ... in li:
    ...
    ...
```
tuple이나 list 결국 본질은 list.

숫자 범위를 반복
```python
 for i in range(start, end[, step]):
```
i가 end값 이상이 될때까지 반복. 즉, i가 end 이상이 되는 순간에 루프 종료.

### pass
아무것도 하지 않고 다음 문장으로 넘긴다.
```python
pass
```

## Object

### Attribute
해당 object에 어느 속성이 존재하는지 확인
```python
if hasattr(obj, 'attr'):
    doStuff(obj.attr)
else:
    otherStuff()
```

## Modules
모듈은 python 코드로 작성된 **.py** 파일이다.

### import

```python
# 기본형: 사용할 때는 모듈이름을 네임스페이스로 표기해 줘야 한다.
import 모듈이름

# 모듈이름을 네임스페이스로 쓰지 않고, 함수나 변수명으로 바로 참조.
from 모듈이름 import 함수_or_변수

# 위와 동일하나, 모듈내의 모든 함수와 변수들에 적용됨. 왠만하면 자제를...
from 모듈이름 import *

# 모듈을 별칭(alias)으로 참조
# import하는 대상을 as 이후에 나오는 별칭으로 참조한다.
import 모듈이름 as 별칭
from 모듈이름 import 함수_or_변수 as 별칭
```

#### 모듈 경로 추가
import시, 기본적으로 현재 경로나 미리 등록된 경로에서만 모듈을 찾는다.

다른 경로에 있는 모듈을 import하려면, 사전에 경로를 등록해 두자.

```python
import sys

sys.path.append(모듈_경로)
```
