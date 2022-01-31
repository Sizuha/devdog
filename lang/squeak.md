# Squeak

## Install
- Download: http://www.squeak.org/Downloads

## 기본 조작
### Workspace
- Command + D: do it. 블럭으로 지정된 부분을 실행.
- Command + P: print it. 블럭으로 지정된 부분을 실행하고 결과값을 바로 Workspace에 표시.
- Command + I: inspect it.

## Transcript
Transcript 윈도우는 TranscriptStream 타입 객체로 ''Transcript''라는 유일한 인스턴스로 접근할 수 있다.

```smalltalk
Transcript show: 'Text Message'. "Transcript창에 메세지를 표시"
Transcript showln: 'Text Message'. "Transcript창에 메세지를 표시하고 개행."
Transcript clear. "Transcript창의 내용을 지운다."
```

## 클래스 생성
클래스를 만든다는 것은, 어떤 식으로든 모체가 되는 클래스로부터 하위 클래스를 만든다는 개념.

모체가 되는 클래스에 subclass 메세지를 날려서 새로운 하위 클래스를 만들 수 있다.

```smalltalk
"Integer 클래스의 정의"
Number subclass: #Integer
  instanceVariableNames: '' "인스턴스 변수"
  classVariableNames: 'LowBitPerByteTable' "클래스 변수"
  poolDictionaries: ''
  category: 'Kernel-Numbers'
```

### 메소드 생성
System Browser의 UI를 이용해서 생성하는게 간단하다.

메서드 리스트의 빈 부분을 클릭하면, 메소드 내용 표시가 다음과 같이 나오는데, 이 내용을 수정한 후에 Command + S 로 저장하면 된다.
```smalltalk
messageSelectorAndArgumentNames
  "comment stating purpose of message"

  | temporary variable names |
  statements
```

### 메소드 제거

```smalltalk
Class removeSelector: #selector
```

## 예외처리

### 에러발생 확인

```smalltalk
[123 / 0] ifError: [ :err | Transcript clear; show: err. ].
```

### 예외 확인

```smalltalk
[ ... ] on: 예외클래스 do: [ 예외처리 내용. ]

[123 / 0] on: ZeroDivide do: [ Transcript clear; show: 'Catch Error: Zero Divide'. ].
```

### 예외 던지기
임의로 예외를 던지고자 할 때는, 해당 Exception class에 signal 메세지를 던지면 된다.
```smalltalk
[ ^ZeroDivide signal ] on: ZeroDivide do: [ Transcript clear; show: 'Zero Divide Error !!' ]
```


## 예제

### Dice
#### One Dice

- 목표: a ~ b 사이의 숫자가 랜덤하게 나오는 주사위 객체를 구현한다.

Dice 클래스를 만든다. 먼저 베이스가 될 클래스가 필요한데, Random이 적당하겠다.
```smalltalk
Random subclass: #Dice
  instanceVariableNames: 'min max'
  classVariableNames: ''
  poolDictionaries: ''
  category: 'Kernel-Numbers'
```

초기화 매소드를 만들자.
```smalltalk
"Instance Method. Category: private"
setMin: minValue max: maxValue

  min := minValue.
  max := maxValue.

"Instance Method. Category: accessing"
min
  ^min

max
  ^max
```

```smalltalk
"class method. Category: instance creation"
newDice: minValue to: maxValue

  | dice |

  dice := super new.
  dice setMin: minValue max: maxValue.
  dice seed: Time totalSeconds.

  ^dice.

new

  ^self newDice: 1 to: 6
```

실제로 주사위를 굴리는 매서드를 추가.
```smalltalk
"Instance Method. Category: dice rolling"
roll

  ^ (self next * max) truncated + min
```

테스트.
```smalltalk
dice := Dice new.
Transcript clear.
30 timesRepeat: [ Transcript show: dice roll; show: ', ' ].
```

#### Advanced Dice
더 다양한 주사위의 패턴을 위해 Interval 객체를 이용.

클래스 정의 수정.
```smalltalk
Random subclass: #Dice
  instanceVariableNames: 'range'
  classVariableNames: ''
  poolDictionaries: ''
  category: 'Kernel-Numbers'
```

기존의 setMin:max: 대신에 다음과 같이 setRange: 매소드를 작성한다.
```smalltalk
setRange: anInterval

  range := anInterval
```

기존의 min, max 매소드는 제거.

기존의 newDice:to: 대신에 다음과 같이 new: 매소드를 작성한다. new 매소드도 같이 수정한다.
```smalltalk
new: anInterval

  | dice |

  dice := super new.
  dice setRange: anInterval.
  dice seed: Time totalSeconds.
  
  ^dice.

new

  ^self new: (1 to: 6)
```

기존의 roll 매소드를 수정한다.
```smalltalk
roll

  ^range at: (self nextInt: range size)
```

테스트.
```smalltalk
dice := Dice new: (10 to: 100 by: 5).
Transcript clear.
30 timesRepeat: [ Transcript show: dice roll; show: ', ' ].

"Interval 뿐만 아니라 Array로도 가능"
dice := Dice new: #(1 2 3 10 20 30).
Transcript clear.
30 timesRepeat: [ Transcript show: dice roll; show: ', ' ].
```
