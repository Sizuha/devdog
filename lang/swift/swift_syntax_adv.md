# Swift 문법 심화

## 주석
### 문서화된 주석
/\*\* 혹은 /// 으로 시작되는 주석에서, Markdown 문법을 사용할 수 있다.

파라매터, 리턴값 등의 설명을 달기 위해 몇 가지 예약어가 존재한다.
```swift
/// 함수 설명
/// 
/// - Parameters:
///   - arg1: 첫번째 인수에 대한 설명 
///   - arg2: 두번째 인수에 대한 설명
/// - Returns: 리턴값에 대한 설명
func some(arg1: Int, arg2: Int) { .. }

/** 혹은 **/

/// 함수 설명
/// 
/// - Parameter arg1: 첫번째 인수에 대한 설명 
/// - Parameter arg2: 두번째 인수에 대한 설명 
/// - Returns: 리턴값에 대한 설명
func some(arg1: Int, arg2: Int) { .. }
```

### 표식
```swift
// MARK: *****
// TODO: *****
// FIXME: *****
```

区切り線表示
```
// MARK: - *****
// TODO: ***** -
// FIXME: ***** -
```

## let
Xcode에서 return값이 있는 함수를 호출 할 때, return값을 받는 처리가 없으면 경고 마크가 뜬다. 굳이 return값을 받을 필요가 없는 경우, 다음과 같이 처리.
```swift
func some() -> Bool { return true }
let _ = some()
```
let 키워드 조차 생략할 수 있다
```swift
_ = some()
```

컬렉션 타입을 let으로 선언하게 되면 Java의 final 처럼 참조만 상수성을 가지는게 아니라, 컬렉션의 변경 자체가 불가능하게 된다.
```swift
let strA: String = "TEXT" // 이후 strA에는 어떤 값도 할당이 안된다
var strB: String = "TEXT" // Mutable String 객체가 된다.

let arrA = [Int]() // 배열 내용을 변경하는게 불가능해 진다.
var arrB = [Int]() // Mutable Array가 된다.
```

**if let** 문법으로 대상이 nil이 아닌 경우를 처리할 수 있지만, nil이 아닌것을 전제조건으로 하고자 할 때 **guard let** 문법을 쓸 수 있다.
```swift
// xxx가 반드시 nil이 아니어야 하고, 이후 a로 정의된다.
guard let a = xxx else { return }
```

## enum
### Enum iterations
```swift
enum TextType: CaseIterable {
    case title
    case subtitle
    case sectionTitle
    case body
    case comment
}

var fonts = [TextType : UIFont]()

for type in TextType.allCases {
    switch type {
    case .title:
        fonts[type] = .preferredFont(forTextStyle: .headline)
    case .subtitle:
        fonts[type] = .preferredFont(forTextStyle: .subheadline)
    case .sectionTitle:
        fonts[type] = .preferredFont(forTextStyle: .title2)
    case .body:
        fonts[type] = .preferredFont(forTextStyle: .body)
    case .comment:
        fonts[type] = .preferredFont(forTextStyle: .footnote)
    }
}
```

## Collection
### Dictionary

```swift
// Dictionary 타입 정의
// [KeyType : ValueType ]

var dic: [String : Any?] = [:]
// 혹은
var dic = [String : Any?]()

// [:]는 비어있는 컬렉션을 뜻함.
// var로 선언되어 있는 컬렉션만 요소를 추가/삭제 가능.
```

**주의!! nil을 할당하면 key가 삭제된다**
```swift
dic["someKey"] = nil
```
이렇게 키에 nil을 할당하면, 「nil」이 값으로 들어가는 것이 아니라, 해당 키가 삭제된다.

## 문자열 다루기
### let과 var의 차이
var로 선언된 문자열 변수는 Java의 StringBuilder, StringBuffer 같은 역할을 한다.
```swift
var buffer = ""
buffer.reserveCapacity(100)
buffer.append("some text")
```

let으로 선언된 문자열 변수는, 문자열 내용이 변화할 때 새로운 문자열을 생성한다.
```swift
let text ="abc"
let text2 = text.appending("def") // append()를 사용할 수 없다. (+ 연산자와 같은 기능)
```

### 문자열 비교
xxx으로 시작하는지 확인
```swift
if url.hasPrefix("http://") { ... }
```

xxx으로 끝나는지 확인
```swift
if filename.hasSuffix(".txt") { ... }
```

xxx가 포함되어 읽는지 확인
```swift
if filename.contains(".txt") { ... }
```

### 문자열 치환
```swift
func replace(text: String, from: String, to: String) -> String {
    text.replacingOccurrences(of: from, with: to)
}
```

### 좌우 공백제거
```swift
"   string    ".trimmingCharacters(in: .whitespacesAndNewlines)
```

### 문자열 분리
```swift
let parts: [String] = "xxx-xxx-xxxx".components(separatedBy: "-")
```

## 형변환
### Data / UnsafeRawPointer
Data to UnsafeRawPointer
```swift
let result = dataObj.withUnsafeBytes { ptr in
    // ...
}
```

UnsafeRawPointer to Data
```swift
let result = dataObj.withUnsafeBytes { ptr in
    Data(bytes: ptr, count: 3)
}
```

UnsafeRawPointer to \[UInt8]
```swift
let data: UnsafeRawPointer
// ...
let converted: [UInt8] = data.load(as: [UInt8].self)
```

### String
C형식 문자열을 Swift 문자열로. (단, UTF-8 문자열인 경우)
```swift
let swiftStr = String(cString: sqlite3_column_name(stmt, col))
```

Data to String
```swift
let text1 = String(data: dataObj, encoding: .utf8)
let text2 = String(bytes: dataObj, encoding: .utf8)
```

String to Data
```swift
if let data = string.data(using: .utf8) {
    // ...
}
```

to String
```swift
let text = "\(대상변수)"
```

String to Int
```swift
// 10진수로 변환
if let result = Int("100") {
    // ...
}

// 2진수로 변환
if let result = Int("100", radix: 2) {
    // ...
}
```

String to Double, Float ...
```swift
if let result = Double("123.0") { 
    // ...
}

if let result = Float("123.0") {
    // ...
}
```

## Assertion
### Swift Optimisation Levels
- Onone (default for debug builds) : 디버그 빌드
- O (default for release builds) : 릴리즈 빌드
- Ounchecked : 권장안함

### Assert
#### assert()

```swift
assert(조건식, "메세지")
```
디버그 빌드에서만 동작. 조건식이 실패하면 에러를 발생시키고 '메세지'를 표시한다.

#### assertionFailure()
디버그 빌드에서만 동작하고, assertion 실패 메세지를 표시한다.
```swift
func printAge(_ age: Int) {
    guard age >= 0 else {
        assertionFailure("Age can't be a negative value")
        return
    }
    print("Age is: ", age)
}
```

#### precondition(), preconditionFailure()
assert(), assertionFailure()와 같은 동작이지만, 디버그와 릴리즈 빌드에서 모두 동작한다. 단, Ounchecked 레벨에서는 동작하지 않는다.

### Fatal Error
fatalError()는 assertionFailure(), preconditionFailure()와 비슷하게 동작하지만, 모든 빌드에 적용된다.
```swift
func printAge(_ age: Int) {
    guard age >= 0 else {
        fatalError("Age can't be a negative value")
    }
    print("Age is: ", age)
}
```

## 연산자 오버로딩
### 2項演算子

```swift
/* 分数の足し算　*/
func + (left: Fraction, right: Fraction) -> Fraction {
    let numerator = left.numerator * right.denominator + right.numerator * left.denominator
    let denomitor = left.denominator * right.denominator
    return Fraction(numerator: numerator, denominator: denomitor)
}

let f1 = Fraction(numerator: 2, denominator: 3)
let f2 = Fraction(numerator: 4, denominator: 5)
let f3 = f1 + f2
print(f3.asString)    // -> 22 / 15
```

### 単項演算子
prefix（前に置く）, infix（間に置く）, postfix（後に置く）

```swift
/* 符号の反転 */
prefix func - (fraction: Fraction) -> Fraction {
    return Fraction(numerator: -fraction.numerator, denominator: fraction.denominator)
}

let f1 = Fraction(numerator: 4, denominator: 10)
print(f1.asString)    // -> 2 / 5
let f2 = -f1
print(f2.asString)    // -> - 2 / 5
let f3 = -f2
print(f3.asString)    // -> 2 / 5
```

### 複合代入演算子

```swift
/* 分数の加算　*/
func += (inout left: Fraction, right: Fraction) {
    left = left + right
}

var f1 = Fraction(numerator: 2, denominator: 5)
let f2 = Fraction(numerator: 3, denominator: 4)
f1 += f2
println(f1.asString)    // -> 23 / 20
```

### 等価演算子

```swift
/* 等価判定 */
func == (left: Fraction, right: Fraction) -> Bool {
    return left.asString == right.asString
}
func != (left: Fraction, right: Fraction) -> Bool {
    return !(left == right)
}

let f1 = Fraction(numerator: 2, denominator: 3)
let f2 = Fraction(numerator: 3, denominator: 5)
let f3 = Fraction(numerator: 4, denominator: 6)

println(f1 == f2)   // false
println(f1 == f3)   // true
println(f1 != f2)   // true
```

### 独自の演算子

예를들어 ?= 라는 연산자를 새로 정의하고 싶을 때, 사용할 연산자를 먼저 선언해 둔다.
```swift
prefix operator ?=
```
다음에 연산자의 동작을 정의해 준다.
```swift
public extension String {
    static prefix func ?= (pattern: String) -> NSRegularExpression? {
        return try? NSRegularExpression(pattern: pattern, options: [])
    }
    func isMatch(_ regex: NSRegularExpression?) -> Bool {
        return regex?.numberOfMatches(in: self, options: [], range: getNSRange()) ?? 0 > 0
    }
}

println("abcd1234".isMatch(?="^[0-9a-zA-Z]+$")) // true
```
