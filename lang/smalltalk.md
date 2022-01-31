# SmallTalk

## 특징
- 문법적인 요소를 최소한으로 줄이고, 가능한 모든 기능들은 객체와 메세지를 주고 받는 ''메세지 교환''을 통해서 구현된다.
- 존재하는 모든 것이 객체: Class, 클로져 함수(클로져 블록) 등 모든 것이 객체로 구현되어 있다.
- 객체의 타입(Class)을 확인하지 않는다. 모든 변수는 단지 객체에 대한 레퍼런스일 뿐이고, 단지 해당 객체가 이 메세지를 이해할 수 있는가? 로만 판단한다.
  - 서로 다른 Class의 객체라 할 지라도 A라는 메세지를 처리할 수만 있다면, 이 경우에 한해서는 같은 타입의 객체라고 간주해도 무방하다.
  - 자연스레 인터페이스 중심의 프로그래밍이 유도된다.
- VM환경에서 동작하며, 가비지 컬렉션을 지원한다.

## 문법

### 지정어(Reserved Words)
Smalltalk에서는 특별 상수 세 개와 특별 변수 세 개, 모두 여섯 개의 '값'(value)을 나타내는 데에 지정어가 쓰인다.
 
- true

True 클래스의 유일한 실체(instance). 논리값이다.
```smalltalk
result := true
```

- false

False 클래스의 유일한 실체. 논리값이다.
```smalltalk
result := false
```

- nil

UndefinedObject 클래스의 유일한 실체.
상태가 정의되지 않은 객체. Null값.
변수를 초기화하지 않으면 nil을 가리킨다.
```smalltalk
garbage := nil
```

- self

현재 메서드가 실행되고 있는 객체를 가리킨다.
```smalltalk
self blahBlah
```

- super

self와 같은 객체를 가리키지만, 메서드(method)를 찾을 때 자기보다 한 단계 높은 객체로부터 찾는다.
```smalltalk
super new initialize
```

### 연산자(Operators)
Smalltalk에는 다음의 두 가지 연산자가 존재한다.

- :=

변수에 값을 대입한다. 오른쪽에서 왼쪽으로 결합한다.
Smalltalk의 모든 변수는 '참조 의미'(reference sementic)를 갖는다.
```smalltalk
count := sum := 0.
```

- ^

메서드에서 발신자(sender)로 객체를 돌려주고, 메서드의 실행을 끝낸다.
이 연산자가 없는 메서드는, self를 돌려준다.
 ^results

### 문법 구성 요소 (Syntactical components)
Smalltakl에서는 특수한 기호를 사용하여 기초적인 객체를 표현한다.

- \[ \]

코드의 블럭. 이 안에 Smalltalk의 코드가 들어간다.
들어간 코드는 바로 실행되지 않고 미뤄진다(deferred).
BlockClosure 클래스의 실체가 된다.
```smalltalk
[ :a b | | sum | sum := a + b ]
```

- \#

Symbol 클래스의 실체를 만든다.
공백이 포함된 symbol을 만들려면 #' ' 를 사용한다.
```smalltalk
#between:and:
```

- #( )

배열, 즉 Array 클래스의 실체를 만든다.
```smalltalk
#(1 2 'apple')
```

- #\[ ]

원소가 바이트인 배열, 즉 ByteArray 클래스의 실체를 만든다.
```smalltalk
#[16r0D 16r0A]
```

- \| |

임시변수를 정의한다.
```smalltalk
| count sum |
```

- $

문자(Character)의 실체를 만든다.
```smalltalk
$a
```

- ' '

문자열(String)의 실체를 만든다.
```smalltalk
'Hello world!'
```

- " "

무시한다. 설명문(comment)을 쓸 때 사용한다.
```smalltalk
"This is a comment."
```

- .

여러 개의 Smalltalk 문장을 분리한다.(문장 마침표가 아니다)
```smalltalk
self foo. self bar. self blahBlah
```

- ;

하나의 객체에 메시지를 연속해서(cascading) 보낸다.
```smalltalk
self foo; bar; blahBlah
```

- :

키워드(keyword)를 만든다.
```smalltalk
5 between: 1 and: 10
```
Block 매개변수(parameter)를 만든다.
```smalltalk
[ :each | each size ]
```

### 메시지의 종류(type of messages)
Smalltalk에는 모두 세 종류의 메시지가 있다.
 
- 단항 메시지(unary message)

인자가 없는 메시지
```smalltalk
200 factorial
```

- 이항 메시지(binary message)

특수기호를 사용한 메시지. 한 개의 인자를 가진다.
```smalltalk
3 + 4
```

- 키워드 메시지(keyword message)

여러 개의 인자를 키워드로 분리하여 전달한다.
```smalltalk
5 between: 1 and: 10
```


### 메시지의 우선순위

1. 단항 메시지
1. 이항 메시지
1. 키워드 메시지

같은 순위의 메시지는 무조건 왼쪽에서 오른쪽으로 실행된다.
```smalltalk
3 factorial + 1 max: 10

"-> 3 factorial + 1 max: 10"
"-> 6 + 1 max: 10"
"-> 7 max: 10"
"-> 10"
```

**주의! 메시지 처리 우선 순위에 예외는 없다!**<br/>
산술 연산(+ - * /)의 우선 순위는 적용되지 않는다. 이들은 모두 같은 이항연산자로 취급되므로, 수식 안에서 곱셈이나 나눗셈이 먼저 적용되어야 한다면 반드시 괄호( )를 사용해서 처리해야 한다.

## 리터럴(literal)
문자열이나 배열 등, 문법 항목에서 설명한 것들은 생략.

- 정수

```smalltalk
123 "123"
16rFFFF "65535"
2r1010 "10"
```
10진 표현은 그냥 그대로 하면 되고, 2진법이나 16진법 등의 표현은 r 키워드를 이용한다.

- 실수

```smalltalk
123.456 "123.456"
16rFFFF.AB "65535.66796875"
2r1010.11 "10.75"
```
실수 표현도 소수점 찍는 것만 제외하면 정수랑 별반 다르지 않다.

지수 표현도 가능하다.
```smalltalk
10e5 "1000000"
1.5e10
```

## 제어 메세지
if나 for 같은 프로그램 흐름을 제어하는 것도 Smalltalk에서는 문법이 아닌 객체간의 메세지 형태로 처리한다.

### if
Boolean형 객체에 ifTrue: 혹은 ifFalse: 메세지를 전달해서 처리한다. 

```smalltalk
Transcript clear.
1 < 2 ifTrue: [ Transcript show: 'true.' ] ifFalse: [ Transcript show: 'false' ]
```

### 클로져 블록
Smalltalk에서 블록 객체\[ ]는 일종의 클로져 역할을 한다. 참조 범위가, 블록 내의 지역 변수 뿐만 아니라, 블록 밖의 변수들까지도 포함한다.

#### 블록 내용의 결과값을 가져오기
어떤 객체든 value메세지를 보내면 그 객체의 고유의 값을 반환하게 된다. 블록의 경우, value 메세지를 보내면 블록의 수행 결과값이 나온다.
```smalltalk
 [ 1 + 2 ] value "return: 3"
```

파라매터가 있는 블록의 경우:
```smalltalk
[ :msg | Transcript show: msg ] value: 'Hello World'

"파라매터가 2개 이상이면 value:value: ... 식으로 메세지 전달"
```

### 반복
#### n회 반복
정수형 객체에 timesRepeat: 메세지를 던지면, 인자로 넘긴 블록을 반복해서 수행한다.
```smalltalk
sum := 0.
10 timesRepeat: [ sum := sum + 1 ].
```

#### 컬렉션 순회
배열 등의 컬렉션 내용을 순차적으로 꺼내오면서 반복하고자 하는 경우.

컬렉션 객체에 do: 메세지를 던진다. 반복 내용을 수행할 블록에서는 인수 변수를 선언해서 컬렉션의 내용을 받아 올 수도 있다.
```smalltalk
(1 to: 10) do: [ :each | Transcript showln: each ].
```

#### while

```smalltalk
[조건 블록] whileTrue: [ 내용 ]. "조건이 참인 동안 반복."
[조건 블록] whileFalse: [ 내용 ]. "조건이 거짓인 동안 반복."

sum := 0.
[ sum < 100 ] whileTrue: [ sum := sum + 1 ].

Transcript clear; show: sum.
```

## 논리 연산

#### and / or
if 등에서 복합 조건을 설정하고자 할 때 and: 혹은 or: 메세지를 사용할 수 있다.
```smalltalk
condA and: condB ifTrue: [ Transcript show: 'true.' ]
condA or: condB ifTrue: [ Transcript show: 'true.' ]
```

and: 혹은 or: 대신에 연산자 메세지로 & 혹은 \| 를 사용할 수 있다. 단, and: 나 or: 에서는 블록\[ ] 내용을 처리할 수 있는데에 반해, 연산자로 할 경우에는 블록 내용을 처리하지 못한다.

```smalltalk
condA & condB ifTrue: [ Transcript show: 'true.' ]
condA | condB ifTrue: [ Transcript show: 'true.' ]

"아래의 경우는 에러"
condA & [ ... ] ifTrue: [ Transcript show: 'true.' ]
condA | [ ... ] ifTrue: [ Transcript show: 'true.' ]

"블록을 굳이 처리하고 싶다면 이렇게 한다"
condA & [ ... ] value ifTrue: [ Transcript show: 'true.' ]
condA | [ ... ] value ifTrue: [ Transcript show: 'true.' ]
```

### not
Boolean형 객체에 not 메세지를 던지면 논리부정 연산을 수행.
```smalltalk
true not. "false"
false not. "true"
```

### xor
Boolean형 객체 2개를 비교하는데 있어서, 양쪽 모두가 서로 다른 값을 가지고 있어야만 true가 됨.
```smalltalk
true xor: true. "false"
false xor: true. "true"
false xor: false. "false"
false xor: true. "true"
```
