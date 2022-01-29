# Coding Style

## 목적
누구나 쉽게 코드를 이해하고 고칠 수 있도록 하는, 일관된 코드 작성 방법에 대한 가이드.
## 기본 원칙
- 보기 좋고, 읽기 편해야 한다. 
  - 화면상에서 뿐만 아니라 인쇄될 때도 생각한다.
  - 혼동이 없어야 한다.
- 나중에 쉽게 고칠 수 있어야 한다.
  - 중복된 정보를 담지 않는다.
- 가능한 다수의 사람들이 쓰는 방식을 따른다.

## 공통
### self 혹은 this 를 붙이는 경우
자기 자신의 인스턴스에 대한 레퍼런스 변수인 _self_ 혹은 _this_ 가 생략 가능한 상황이여도, **해당 인스턴스 변수가 정의된 클래스 내부에서 사용 되어지는 경우**에는 명시적으로 표기한다.

다른 클래스 영역(예를들어, 상속을 통해 정의된 하위 클래스)에서는 붙이지 않는다.

### 들여쓰기
- 들여쓰기(Intent)는 탭으로만 한다. 스페이스 사용금지.
  - 만약 굳이 스페이스를 써야 한다면, 들여쓰기는 전부 스페이스로만 한다. 즉, 탭과 섞어서 사용하지 말것!
  - 탭 문자에 대한 처리가 에디터에 따라 다를 수 있기에 섞어쓰면 다른 환경에서 볼때 정렬이 흐트러짐.
- 제어블럭이 발생할 때, 즉 제어 범위가 한 단계 깊이 들어가는 경우에 들여쓴다.

```csharp
if (...) {
	// 조건 처리에 의해 제어 깊이가 발생.
}

void Func(...) {
	// 함수에 대한 제어 범위 발생.
}

struct XXX {
	// 구조체 선언에 의한 제어 범위 발생.
}
```
- 명령문이 한 라인에서 끝나지 않고 다음 라인까지 이어질 때, **그 다음의 라인**부터 들여쓴다.

```csharp
someFunc(arg1, 
	arg2, // 들여쓰기
	arg3, // 들여쓰기
	argN); // 들여쓰기
```
- 전처리 문은 기본적으로 들여쓰지 않는다.
  - C계열 언어의 경우 #define 이나 #if 등의 경우.

### 변수 선언, 이름 짓기
- 헝가리안 표기 금지.
  - int, float 같은 뻔한 타입(type)을 접두어로 붙이지 않는다.
    - 이런건 선언문만 봐도 안다. 그리고 컴파일 단계에서도 타입 체크가 이뤄지고, 에디터에서도 쉽게 확인이 된다.
    - 타입이 변경될 경우, 변수 이름까지 고쳐야 되는 수고는 하지 말자.
  - 에디터에서 서포트 되지 않은 타입 정보가 필요하거나, 객체들간에 공통된 특성 등을 강조하고자 할 때 적당한 접두어를 붙인다.
- 상수(const)는 전부 대문자로 한다.
- 함수의 인자와 지역 변수는 소문자로 시작한다.
- 타입 이름은 대문자로 시작한다.

### 연산자
- 기본적으로 이항 연산자의 양쪽에는 공백을 한칸 씩 둔다.
  - 단, 우선 순위를 강조하고자 할 때 붙여서 쓸 수도 있다. 다만 괄호를 확실하게 해주는게 더 나을지도.

```csharp
int a = (1 + 2*3) / 4;
```

## C 계열 언어
### 지역 변수
- 임시 변수(지역 변수)는 소문자와 밑줄(_)로만 명명한다. (optional)
- index, iterator로 활용되는 지역 변수는 i_ 로 시작한다.
- 변수 선언을 최대한 해당 변수가 사용되는 직전까지 미룬다. 즉, 변수 선언은 곧 이 변수가 사용될 거라는 걸 암시한다.

### 제어문 블럭
- 제어문 블럭은 제어문이 선언된 행에서 **{** 를 연다.
- **}** 는 제어문 키워드가 시작된 열에 맞춰서 닫는다.
- 블럭을 여는 **{** 문자가 있는 행의 첫 문자가 시작되는 열과, 블럭을 닫을 때 나오는 **}** 문자가 있는 열을 일치 시키도록 한다.
- 제어문의 **( )** 는 함수 호출과 구별하기 위해 제어문 키워드와 한칸 공백을 두고 쓴다.

#### if/else
- if와 else문장이 함께 나올 때는, if절 다음에는 긍정 조건을 else에는 부정 조건 때의 처리를 기술함을 원칙으로 한다.
  - 이는 if ~ else 형식의 문장을 봤을 때, 가능한 일관되게 해석이 되도록 하기 위함이다.

```csharp
if (condition) {
	// when condition == true.
}
else {
	// when condition == false.
}

// 다음과 같이 하지 않는다.
if (!condition) {
	// when condition == false.
}
else {
	// when condition == true.
}
```

- **if ~ else if ~ else** 표현:

```csharp
if (...) {
	// . . .
}
else if (...) {
	// . . .
}
else if (...) {
	// . . .
}
else {
	// . . .
}
```

- 다수의 **or** , **and** 로 연결된 긴 조건식이 있는 경우:

```csharp
if (CONDITION1 ||
	CONDITION2 ||
	CONDITION3 ||
	CONDITIONn)
{
	// . . .
}

// 임시로 특정 조건을 막아두거나 할 때는 다음과 같이 편하게 주석으로 처리할 수 있다.
if (CONDITION1 ||
	CONDITION2 ||
//	CONDITION3 ||
	CONDITIONn)
{
	// . . .
}
```

- C언어의 경우: if나 while등의 조건식에서, 대입 연산(=)과 비교 연산(==)의 오타로 인한 오류 방지를 위해 다음과 같이 한다.
  - 조건식에서 대입 연산자는 쓰지 않는다. ==의 오타로 오인할 수 있으므로.
  - 변수와 상수를 비교할 경우, 상수를 먼저 쓴다. 오타일 경우, 컴파일 단계에서 오류가 나도록.

```csharp
if (10 == a) { ... }
```

- C++은 조건식의 결과값이 bool 타입이 되어야 하므로, C에서와 같은 오타로 인한 오류는 방지가 되지만, 혼동을 방지하기 위해 조건식 안에서 대입 연산은 가능한 하지 말자.
  - 조건식의 판정을 bool 타입으로 하기 때문에 다음과 같이 true/false를 명시적으로 써줄 필요는 없다.

```csharp
// 다음은 의미상의 중복
if (a == true) { ... }

// 오타로 인한 버그도 우려된다
if (a = true) { ... }
```

#### switch

```csharp
switch (condition) {
	case CASE_1:
		break;

	case CASE_2:
	case CASE_3:
		break;

	case CASE_n:
		break;

	default:
		break;
}

/* case 안에서 변수 선언이 있는 경우 */
switch (condition) {
	case CASE_x: {
		int x = xxx;
		// . . .	
		break;
	}
}
```

#### wile, do ~ while

```c
while {
	// . . .
}

do {
	// . . .
} while (...);
```
do ~ while에서 while은 **}** 다음에 쓴다. 다음 행으로 내려서 쓰면 일반 while문과 혼동될 수 있기 때문에.

#### for

```csharp
for (int i = 0; i < MAX, i++) {
	// . . .
}
```

#### 3항 연산자
조건에 때라 변수 초기화 내용이 달라질 때,
```java
/* Java */
int some;
if (cond)
	some = 1;
else
	some = 0;
```

3항 연상자를 이용해서 간단히 표현하기.
```java
/* Java */
final int some = cond ? 1 : 0;

// 또는 
final int some = cond
	? 1
	: 0;
```
두번째 표현 방식은, 단순한 상수를 할당하는게 아니라 내용이 좀 더 복잡한 경우에 유용하다.

특히 중첩된 조건이 필요한 경우에,
```java
/* Java */
final int some = cond > 0
	? cond == 1
		? getCase1()
		: getCase2()
	: cond < 0
		? getCase3()
		: getCase4();
```

### 함수
#### 함수 선언

```java
void Func(int argName, ...) {
	// TODO ...
}

void Func(int arg_1,
	int arg_2,
	int arg_n)
{
	// TODO ...
}

// 결과값을 return문 아닌 인수에다 전달 할 경우, 결과를 전달 받는 인수에 접두어 'out_' 을 붙인다.
// out_이 붙은 인자는 기본적으로 함수내에서 초기화를 담당한다.
void FuncA(int* out_result) { ... }
void FuncB(int& out_result) { ... }
```

#### 함수 호출
- 제어문과 구분하기 위해, 함수명에 ( )를 공백없이 붙여서 쓴다.
- 함수 인자가 길어지는 경우:

```csharp
Func(arg_1,
	arg_2,
	arg_n);

result = Func(arg_1,
	arg_2,
	arg_n);
```
- 부등호가 포함된 연산(>, <, >=, <=)은 함수 인자를 넘기는 곳에서는 가급적 쓰지 않도록 한다. C++의 경우 템플릿 문법(< >)과 혼동이 올 수 있다.

### C#
- 매서드와 프로퍼티는 대문자로 시작하고 낙타법으로 명명한다.
- 맴버변수는 m_ 으로 시작하고 낙타법으로 명명한다.
  - 단 public 맴버변수는 프로퍼티 작명과 동일한 규칙을 적용한다.

```csharp
class Sample {
	public string Name;
	private int m_memberVar;

	public int ExcuteSome(int someParam) {
		int local_var = 1;
		return local_var * memberVar + someParam;
	}

	public int Count {
		get { return 0; } 
	}
}
```

### Java
- 맴버 변수는 소문자로 시작하고 낙타법으로 명명한다.
- 매서드는 소문자로 시작하고 낙타법으로 명명한다.

```java
class Sample {
	public string name;
	private int memberVar;

	public int excuteSome(int someParam) {
		int local_var = 1;
		return local_var * m_memberVar + someParam;
	}
}
```

## Kotlin
### 명명규칙
- Type은 대문자로 시작
  - enum 내부의 항목들은 대문자로 시작. (상수 취급)
- 컴파일 타임 상수(const)는 모두 대문자
- 이 외는 모두 소문자로 시작

## Android
### Resource ID 명명 규칙
기본적으로 소문자와 밑줄(_)만으로 명명하는 것을 원칙으로 한다.
#### View ID

| prefix | 説明 |
| --- | --- |
| action_ | Button 등을 통해 사용자의 액션을 반영하는 UI |
| edit_ | 사용자가 입력/편집 할 수 있는 UI |
| select_ | 열거된 항목 중에서 선택만 가능한 UI |
| text_ | 문자열을 표시하기 위한 UI |
| img_ | 이미지를 표시하기 위한 UI |
| view_ | 기타, 정보 표시를 목적으로 하는 UI |
| touch_ | 터치 이벤트를 받기 위한 UI. 실제 화면에 보이는 내용보다 더 넓은 터치 영역을 지정하고자 하는 경우 등. |
| list_ | ListView, RecyclerView 같이 Adapter를 이용해서 내부에 다수의 Item View를 표시하는 UI |
| group_ | 복수의 View를 그룹화해서 관리하기 위한 목적의 UI |

내용상 중복되는 UI가 있는 경우, 명명 규칙은 위에서 부터 우선적으로 적용한다. 예를 들면, 텍스트를 표시하는 UI이지만 버튼처럼 쓰이는 경우는 ''action_''을 붙인다.

#### strings

| prefix | 説明 |
| --- | --- |
| err_ | 에러 메세지 |
| fmt_ | Formatting을 위한 문자열 |
| msg_ | 메세지 표시를 위한 문자열 |

#### 리소스 파일 명명 규칙
##### Layout

| prefix | 説明 |
| - | - |
| activity_ | Activity Layout |
| fragment_  | Fragment Layout |
| dailog_ | Dialog Layout |
| adapter_ | Adapter에서 Item View를 구성하는 Layout |
| sub_ | 다른 Layout에서 include 태그 등으로 불려지는 Layout |

## Swift
### 인수 갯수가 많은 func을 정의한 때

```swift
public func createConfirmDialog(
	title: String,
	message: String,
	okText: String = "OK",
	cancelText: String = "Cancel",
	onOkClick: ((UIAlertAction) -> Void)? = nil,
	onCancelClick: ((UIAlertAction) -> Void)? = nil
) -> UIAlertController {
	SizAlertBuilder()
		.setTitle(title)
		.setMessage(message)
		.addAction(title: cancelText, handler: onCancelClick)
		.addAction(title: okText, handler: onOkClick)
		.create()
}

class XXX {
// ...
	public init(
		type: CellType = .text,
		height: (()->CGFloat)? = nil,
		label: String = "",
		bindData: (()->Any?)? = nil,
		hint: String = "",
		customViewID: String? = nil,
		onSelect: ((_ index: IndexPath)->Void)? = nil,
		onCreateCell: ((UITableViewCell)->Void)? = nil,
		onWillDisplay: ((UITableViewCell)->Void)? = nil,
		onChanged: ((_ value: Any?)->Void)? = nil
	) {
		self.type = type
		self.height = height
		self.label = label
		self.bindData = bindData
		self.hint = hint
		self.customViewID = customViewID
		self.onSelect = onSelect
		self.onCreateCell = onCreateCell
		self.onWillDisplay = onWillDisplay
		self.onChanged = onChanged
	}
// ...
}
```
