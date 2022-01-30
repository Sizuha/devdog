# Swift 문법

## 세미콜론
Swift는 기본적으로 한 줄에 한 문장만 들어간다. 따라서 C 언어처럼 문장 끝을 나타내기 위해 ; 등을 쓰지 않는다.

단, 한 줄에 여러 문장을 쓰는 경우에 ; 으로 구분할 수 있다.

BASIC 언어의 : 같은 역할.

## 상수/변수 선언

```swift
let 상수명 = 값
// ex)
let myConstant = 42

var 변수명 = 값
// ex)
var myVariable = 42
```
위 경우는 값으로부터 자동으로 Type을 유추해서 적용된다.

직접 Type을 지정해서 선언하는 경우:
```
let 상수명:Type = 값
// ex)
let myConstant: Double = 72.0

var 변수명:Type = 값
// ex)
var myVariable: String = "72"
```

변수를 한줄에 여러개 선언:
```swift
var red, green, blue: Double
var x = 0.0, y = 0.0, z = 0.0
```

### Lazy 선언
lazy var로 선언된 변수는, 처음 참조가 이루어질 때에만 초기화가 이루어 진다. 이후로는 초기화된 상태에서 참조만 이루어 진다.

https://qiita.com/negi0205/items/d367395d5780c915fb1d

```swift
// lazyなし
var defaultPrice = 100

class Item {
    var price: Int = defaultPrice
}

let item = Item()

defaultPrice = 200
print(item.price) // 100
```

```swift
// lazyあり
var defaultPrice = 100

class Item {
    lazy var price: Int = defaultPrice
}

let item = Item()

defaultPrice = 200
print(item.price) // 200

defaultPrice = 300
print(item.price) // 200
```

## Basic Types
### Integers
각각 8, 16, 32, 64 bit 크기를 가지는 정수형: Int8, Int16, Int32, Int64

그리고 부호없는 정수형: UInt8, UInt16, UInt32, Uint64

Int, Uint 형은 플랫폼에 따라서 32bit 혹은 64bit 가 된다.

```swift
let decimalInteger = 17
let binaryInteger = 0b10001       // 17 in binary notation
let octalInteger = 0o21           // 17 in octal notation
let hexadecimalInteger = 0x11     // 17 in hexadecimal notation
```

숫자 사이에 _ 문자를 넣을 수 있다. 천단위 자릿수 구분으로 활용할 수 있다.
```swift
let oneMillion = 1_000_000
```

### Floating-Point Numbers
- Double: 64bit
- Float: 32bit

```swift
let sample1: Double = 5.03
let sample2: Float = 5.03

let justOverOneMillion = 1_000_000.000_000_1
```

#### 지수 표현
10진수 방식은 e를 쓰고, 16진수 표현에서는 p를 쓴다. 단 p의 경우는 2^exp 가 된다.
```swift
let exponentDouble = 1.21875e1 // 1.21875 x 10^1
let hexadecimalDouble = 0xC.3p0 // 0C x 2^0
```

### Booleans
```swift
let orangesAreOrange: Bool = true
let turnipsAreDelicious = false
```

### Tuples
튜플은 여러개 값을 하나로 묶은 것. 리스트와 유사하지만 리스트처럼 안의 원소들에 대한 변경은 지원하지 않는다.
```swift
let http404Error = (404, "Not Found")
// http404Error is of type (Int, String), and equals (404, "Not Found")
```

튜플을 이용한 변수/상수 선언 및 할당:
```swift
// 앞에서 이어짐...
let (statusCode, statusMessage) = http404Error
print("The status code is \(statusCode)") // Prints "The status code is 404"
print("The status message is \(statusMessage)") // Prints "The status message is Not Found
```

익명(_):
```swift
let (justTheStatusCode, _) = http404Error
```

이름이 붙은 원소:
```swift
let http200Status = (statusCode: 200, description: "OK")

print("The status code is \(http200Status.statusCode)") // Prints "The status code is 200"
print("The status message is \(http200Status.description)") // Prints "The status message is OK
```

### Optionals
C# 에서의 Nullable 형과 유사. 타입 이름 뒤에 ? 를 붙이면 Optional 타입이 된다.

오브젝트형이 아닌 기본형에서 널(nil) 혹은 값(Value)을 취급 할 수 있게 해준다.

```swift
var serverResponseCode: Int? = 404

// nil 할당도 가능
serverResponseCode = nil

var surveyAnswer: String?
// surveyAnswer is automatically set to nil
```

#### nil 검사
인스턴스가 nil이 아닌 경우에 메소드 실행
```swift
someOptional?.someMethod()
```

?? 연산자는 앞의 항이 nil 인 경우에 뒤 문장을 수행한다.
```swift
let test = some as? String ?? ""
```

##### 제어문과 let/var 조합
if/guard 등의 제어문과 let 혹은 var를 같이 사용하면 Optional 값에 대한 nil 여부 처리를 간단히 할 수 있다.
```swift
if let constName = someOptional {
    // constName은 이 블록 안에서만 유효
    statements
}
// 혹은
if var varName = someOptional {
    // varName은 이 블록 안에서만 유효
    statements
}
```

```swift
guard let constName = AnimeDataManger.shared.loadDetail(id: itemID) else {	
	return
}
// constName은 이후에도 유효
```

```swift
// for와 var를 같이 사용하는 경우
for var a in someArr {
	a = 0	
}
```

#### Implicitly Unwrapped Optionals
Optional을 일반 타입으로 전환할 때:
```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // requires an exclamation mark
```

```swift
let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // no need for an exclamation mark
```

### String / Characters
```swift
let someString: String = "Some string literal value"
let someCharacter: Character = "A"
```

문자열 연결
```swift
let someString = "black" + " and " + "white"
```

문자열로 부터 개별 문자열 탐색
```swift
for character in "Dog!🐶".characters {
    print(character)
}
```

문자열 안에 값 넣기: 문자열 리터럴 안에 \( ) 표현을 이용해 변수, 상수, 계산식 등을 추가할 수 있다.
```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message is "3 times 2.5 is 7.5”
```

#### Special Characters in String Literals
C에서 처럼 다음과 같은 특수문자 표현을 지원한다.
```swift
\0 \\ \t \n \r \" \'
```

유니코드 표현
```swift
let dollarSign = "\u{24}"        // $,  Unicode scalar U+0024
```

#### 복수행

https://medium.com/swift-column/multiline-string-literals-ba5fa8f4853e

```swift
let sampleString = """
あいうえお
かきくけこ
さしすせそ
"""

// "あいうえお\nかきくけこ\nさしすせそ"
```

```swift
let sampleString = """
あいうえお
かきくけこ\
さしすせそ
"""

// "あいうえお\nかきくけこさしすせそ"
```

```swift
let sampleString = """
    あいうえお
        かきくけこ
    さしすせそ
    """

// "あいうえお\n    かきくけこ\nさしすせそ"
```

#### Raw String

https://www.hackingwithswift.com/articles/162/how-to-use-raw-strings-in-swift

```swift
let zero = "This is a string"
let one = #"This is a string"#
let two = ##"This is a string"##
let three = ###"This is a string"###
let four = ####"This is a string"####
```

```swift
let example = "ABCD"
let message = #"""
This is rendered as text: \(example).
This uses string interpolation: \#(example).
"""#

// This is rendered as text: \(example).\nThis uses string interpolation: ABCD
```

#### String Indices
```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

### Type Aliases
```swift
typealias AudioSample = UInt16
```

## Collection Types
### Arrays

```
형: [Type]
리터럴: [원소1, 원소2, ...]
```

배열 선언
```swift
var someInts = [Int]()
var shoppingList: [String] = ["Eggs", "Milk"]
```

배열 머지
```swift
let arrA: [Int] = [1,2,3]
let arrB: [Int] = [4,5,6]
let arrC = arrA + arrB
```

배열에, 다른 배열의 내용을 추가
```swift
let source: [Int] = [1,2,3,4,5]
var base = [Int]()
base.append(contentsOf: source)
```

### Set
```
형: Set<Type>
```

Array이랑 비슷하지만, 원소 내용이 중복되지 않는다.

```swift
var letters = Set<Character>()
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]
```

### Dictionaries
```
형: [KeyType: ValueType]
리터럴: [key 1: value 1, key 2: value 2, key n: value n, ...]
```

```swift
var namesOfIntegers = [Int: String]()

namesOfIntegers[16] = "sixteen"
// namesOfIntegers now contains 1 key-value pair
namesOfIntegers = [:]
// namesOfIntegers is once again an empty dictionary of type [Int: String]

var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

## Basic Operators
C언어의 연산자들과 거의 비슷하다.

### Assignment Operators
**=** 기호로 대입을 표현한다. 단, C와는 다르게 Swift의 대입문은 값을 Return하지 않는다.

C 언어와 마찬가지로, 산술연사과 대입이 결합된 형태의 연산자도 지원한다.
```
+= -= *= %=
```

### Arithmetic Operators ===
```
+ - * / %
```

C언어랑 동일하다.

주의할 점은 나머지 연산(%)이 Remainder 연산이라는 점이다. 음수에 대한 나머지 연산일 때 주의가 필요하다.
```swift
/*
 * a = (b x some multiplier) + remainder 
 */

-9 % 4   // => -1
```

부호 전환
```swift
let three = 3
let minusThree = -three
```

### Comparison Operators
역시 C랑 비슷하다.
```
== != > < >= <=
```

문자열값 비교에는 == 혹은 != 를 사용한다.

### Ternary Conditional Operator
C 언어 처럼 ? : 연산자도 지원한다.
```
(condition) ? (ifTrue) : (ifFalse)
```

### Nil-Coalescing Operator
C# 언어처럼 ?? 연산자를 지원한다.

- 앞의 항은 Optional 타입이어야 한다.
- 앞 항이 nil이 아니면 unwrapped된 값을 반환
- nil이면 뒤 항을 실행한다.

```swift
a != nil ? a! : b

a ?? b
```

### Range Operators

a부터 b까지(b포함)에 해당되는 범위를 나타낸다.
```
a...b
```

a부터 b-1에 해당되는 범위를 나타낸다.
```
a..<b
```

```swift
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
```

### Logical Operators
```swift
&& || !
```

C랑 동일.


## 제어문
### Loop
#### For-In

```swift
for 변수 in 열거형 {
    // ...
}
```
변수는 익명( _ ) 으로 대체 가능.

```swift
let numberOfLegs = ["spider": 8, "ant": 6, "cat": 4]
for (animalName, legCount) in numberOfLegs {
    print("\(animalName)s have \(legCount) legs")
}

for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
```

#### While

```swift
while condition {
    statements
}

repeat {
    statements
} while condition
```

### Conditional Statements
#### if

```swift
if 조건 {
    // true이면
}
else {
    // false이면
}

if let XXX = 식 {
    // nil이 아니면
}
```

#### switch

```swift
switch some_value_to_consider {
case value 1:
    // respond to value 1
case value 2, value 3:
    // respond to value 2 or 3
default:
    // otherwise, do something else
}
```
각 case에는 기본적으로 break가 자동으로 붙는다.

case에 범위 지정(... 혹은 ..<)도 가능.
```swift
switch point  {
case 0...50:
    // ...
case 51..<100:
    // ...
}
```

case에 문자열도 가능.
```swift
switch command {
case "add":
    // ...
case "remove":
    //
}
```

case에 튜플도 가능.
```swift
let somePoint = (1, 1)
switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
```

##### Value Bindings

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "on the x-axis with an x value of 2”
```

###### Where

```swift
let yetAnotherPoint = (1, -1)
switch yetAnotherPoint {
case let (x, y) where x == y:
    print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("(\(x), \(y)) is just some arbitrary point")
}
// Prints "(1, -1) is on the line x == -y”
```

### Control Transfer Statements
```swift
continue, break, return
```

C언어에서와 동일.

break는 switch 문에서도 사용할 수 있지만, 기본적으로 모든 case에서 자동으로 break가 수행된다. 단, 비어있는 case 문이 필요할 때 break를 명시할 수 있다.

#### fallthrough
switch문에서 각 case는 마지막에 자동으로 break를 수행한다.

단, 명시적으로 바로 아래의 case의 내용으로 진행하고자 할 때 fallthrough을 쓸 수 있다.
```swift
let integerToDescribe = 5
var description = "The number \(integerToDescribe) is"
switch integerToDescribe {
case 2, 3, 5, 7, 11, 13, 17, 19:
    description += " a prime number, and also"
    fallthrough
default:
    description += " an integer."
}
```

#### Labeled Statements
제어문 앞에 label을 붙여서 네이밍을 해 두면, continue나 break 때 label을 지정할 수 있다.

label이 지정된 continue/break 문은, 해당 문이 속해있는 제어블록을 벗어나 지정된 label을 제어블록을 대상으로 수행하게 된다.
```swift
gameLoop: while square != finalSquare {
    diceRoll += 1
    if diceRoll == 7 { diceRoll = 1 }
    switch square + diceRoll {
    case finalSquare:
        // diceRoll will move us to the final square, so the game is over
        break gameLoop
    case let newSquare where newSquare > finalSquare:
        // diceRoll will move us beyond the final square, so roll again
        continue gameLoop
    default:
        // this is a valid move, so find out its effect
        square += diceRoll
        square += board[square]
    }
}
print("Game over!")
```

### Guard
if와 비슷한데, 조건식이 true이어야 함을 보장할 때 사용된다. 그리고 반드시 else문과 짝을 이룬다.
```swift
guard 조건식 else {
    // false 일때 처리할 내용.
    // return, break, continue 등의 제어문으로 끝나야 한다.
}
```

```swift
func greet(person: [String: String]) {
    guard let name = person["name"] else {
        return
    }
    
    print("Hello \(name)!")
    
    guard let location = person["location"] else {
        print("I hope the weather is nice near you.")
        return
    }
    
    print("I hope the weather is nice in \(location).")
}
```

하나의 guard 문에 여러개의 조건식(콤마(,)로 구분)을 넣을 수 도 있다.
```swift
guard
    let condA = condA,
    let condB = condB
else { return }
```

### Checking API Availability
특정 플랫폼, OS 버전에서만 수행되어야 하는 코드일 경우:
```swift
if #available(platform name version, ..., *) {
    // statements to execute if the APIs are available
} else {
    // fallback statements to execute if the APIs are unavailable
}
```
\* 는 열거된 거 이외의 플랫폼을 의미한다.

```swift
if #available(iOS 10, macOS 10.12, *) {
    // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
} else {
    // Fall back to earlier iOS and macOS APIs
}
```

## Functions

```swift
func 함수명([호출용_키워드 ]인자명: [xxx ]인자형, ...) -> 반환형 {
    // ...
}

func 함수명(인자명: 인자형, ...) {
    // ...
}

func 함수명() -> 반환형 {
    // ...
}

func 함수명() {
    // ...
}
```

함수 호출은 다음과 같이 표현한다.
```swift
함수명(인자명: 값)
```

호출할 때, 인자명을 쓰지 않고 익명으로 호출하게 하고자 할 때:
```swift
func 함수명(_ 내부인자명: 인자형, ...) -> 반환형 {
    // ...
}

// ex)
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(1, secondParameterName: 2)
```

### Default Parameters Values

```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // If you omit the second argument when calling this function, then
    // the value of parameterWithDefault is 12 inside the function body.
}
```

### Variadic Parameters
가변 파라매터 처리.

함수 내부에서는 배열로 취급된다.
```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers
```

### In-Out Parameters

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}

var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3”
```

### Function Types
```
형: (인자형, ...) -> 반환형
```

```swift
// ex)
(Int, Int) -> Int
() -> Void

func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

var mathFunction: (Int, Int) -> Int = addTwoInts
```

#### Function Types as Parameter Types

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
```

#### Function Types as Return Types
함수내의 반환형에도 함수 타입을 쓸 수 있다.

### 함수 내 함수
함수 안에서 또 함수를 정의할 수 있다.

## Closures

```swift
{ (parameters) -> return type in
    statements
}
```

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)

// --- or ---

reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```

참고로 클로져는 레퍼런스 타입으로 취급된다.

### Inferring Type From Context

```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

### Implicit Returns from Single-Expression Closures

```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

### Shorthand Argument Names

```swift
reversedNames = names.sorted(by: { $0 > $1 } )
```

### Trailing Closures

```swift
// 클로져를 인자로 받는 함수 정의
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // function body goes here
}
 
// 사용법1. 
someFunctionThatTakesAClosure(closure: {
    // closure's body goes here
})
 
// 사용법2. 
someFunctionThatTakesAClosure() {
    // trailing closure's body goes here
}
```

## Enumerations
C 계열 언어의 enum과 비슷하지만, 좀 더 복잡하다.

Java에서 처럼 일종의 제한된 형태의 struct라고 봐도 되겠다.

```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}

enum Planet {
    case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

switch 문에서의 활용
```swift
directionToHead = .south
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
```

### Associated Values

```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

var productBarcode = Barcode.upc(8, 85909, 51226, 3)
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")

switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}

//--- or ---
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
```

### Raw Values

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}

enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}

// Initializing from a Raw Value
let possiblePlanet = Planet(rawValue: 7)
```

## Classes and Structures

```swift
class SomeClass {
    // class definition goes here
}

struct SomeStructure {
    // structure definition goes here
}
```

classは「参照型」、 structは「値型/データ型」

### 접근제한자
#### 접근
| 제한자 | 모듈밖 | 모듈내 어디든 | 파일내 어디든 |
| - | - | - | - |
| open | O | O | O |
| public | O | O | O |
| internal (default) | X | O | O |
| fileprivate | X | X | O |
| private | X | X | X |

#### 상속 및 오버라이드
| 제한자 | 모듈밖 상속/오버라이드 | 모듈내 상속 | 모듈내 오버라이드 |
| - | - | - | - |
| open | O | O | O |
| public | X | O | O |
| internal (default) | X | O | O |
| fileprivate | X | X | O (같은 파일에서) |
| private | X | X | X  |

### 참조 비교
다음 두 연산자를 사용
```
=== !==
```

### Properties

```swift
struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) { // 인수명을 생략하고 set { } 으로 쓰면, newValue가 인수명이 된다
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}

class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
```

#### Read-Only Computed Properties

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}

let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// Prints "the volume of fourByFiveByTwo is 40.0”
```

### Methods

```swift
class Counter {
    var count = 0

    func increment() {
        self.count += 1
    }

    func increment(by amount: Int) {
        count += amount
    }

    func reset() {
        count = 0
    }
}

let counter = Counter()
counter.increment()
counter.increment(by: 5)
counter.reset()
```

#### Modifying Value Types from Within Instance Methods
struct, enum 등은 Value Type이기 때문에, 한번 값이 할당되면 기본적으로 수정할 수 없다.

하지만, 예외적으로 인스턴스 메서드 안에서 값을 수정하고자 할 때, 메서드 앞에 mutating 키워드를 붙인다.
```swift
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}
//--- or ---
struct Point {
    var x = 0.0, y = 0.0
    mutating func moveBy(x deltaX: Double, y deltaY: Double) {
        self = Point(x: x + deltaX, y: y + deltaY)
    }
}

var somePoint = Point(x: 1.0, y: 1.0)
somePoint.moveBy(x: 2.0, y: 3.0)
print("The point is now at (\(somePoint.x), \(somePoint.y))")


enum TriStateSwitch {
    case off, low, high
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}

var ovenLight = TriStateSwitch.low
ovenLight.next()
// ovenLight is now equal to .high
ovenLight.next()
// ovenLight is now equal to .off
```

### Type Property / Type Methods
C++ 에서 처럼 static 으로 Class에 종속된 맴버 변수, 메서드를 선언할 수 있다.

Class의 경우는 class 키워드로 선언하면 하위 클래스에서 재정의(override) 가능하게 만들 수 있다.

```swift
class SomeClass {
    class func someTypeMethod() {
        // type method implementation goes here
    }
}
```

### Subscripts
Classes, structures, and enumerations can define subscripts, which are shortcuts for accessing the member elements of a collection, list, or sequence. You use subscripts to set and retrieve values by index without needing separate methods for setting and retrieval.

```swift
subscript(index: Int) -> Int {
    get {
        // return an appropriate subscript value here
    }
    set(newValue) {
        // perform a suitable setting action here
    }
}

// for Read-only
subscript(index: Int) -> Int {
    // return an appropriate subscript value here
}
```

```swift
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}

let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// Prints "six times three is 18”
```

### Inheritance

```swift
class SomeSubclass: SomeSuperclass {
    // subclass definition goes here
}
```

#### Overriding

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}

class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
```

#### Abstract Class
Swift에는 추상 클래스 개념이 없다. **fatalError()** 등을 이용해서 추상 클래스 비슷하게 사용되어지게 할 수는 있다.

### Initialization

```swift
init() {
    // perform some initialization here
}
```

```swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}
var f = Fahrenheit()
print("The default temperature is \(f.temperature)° Fahrenheit")
// Prints "The default temperature is 32.0° Fahrenheit”


struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
        self.red   = red
        self.green = green
        self.blue  = blue
    }
    init(white: Double) {
        red   = white
        green = white
        blue  = white
    }
}

let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)
```

#### Memberwise Initializers for Structure Types

```swift
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

#### Class Inheritance and Initialization

```swift
class Bicycle: Vehicle {
    override init() {
        super.init()
        numberOfWheels = 2
    }
}
```

#### Failable Initializer
初期化に失敗することができるイニシャライザ
```swift
class Sample {
    // Optional<Sample>を返す
    init?() {
        // ...
    }

    // ImplicitlyUnwrappedOptional<Sample>を返す
    init!() {
        // ...
    }
}
```

#### Designated Initializer 
일반적인 생성자.

#### Memberwise Initializer
struct 타입에 적용.
```swift
struct Sample {
    let name: String = "Guest"
    let number: Int
}

let sample = Sample(number: 1)
```

#### Convenience Initializer
同じクラス(親クラスのものでもない)のDesignated Initializerを呼び出す(移譲する)必要があり、Designated Initializerに対して補助的な役割を担います。
```swift
convenience init() {
    /// ...
}
```

```swift
class Sample {
    let name: String

    init(name: String) {
        self.name = name
    }

    // nameを指定しなくても初期化できる補助的なイニシャライザ
    convenience init() {
        self.init(name: "Guest")
    }
}
```

#### Required Initializer
サブクラスに必ず実装させたいイニシャライザです。

required修飾子をつけたイニシャライザをオーバーライドする際はoverride修飾子は不要です。
```swift
class Sample {
    let name: String

    required init(name: String) {
        self.name = name
    }
}

class Example: Sample {
    required init(name :String) {
        super.init(name: name)
    }
}
```

### Deinitialization

```swift
deinit {
    // perform the deinitialization
}
```

## Extensions
Extensions in Swift can:
- Add computed instance properties and computed type properties
- Define instance methods and type methods
- Provide new initializers
- Define subscripts
- Define and use new nested types
- Make an existing type conform to a protocol

상속 없이 기존의 클래스를 그대로 사용하면서. 기능을 확장하고자 할 때 활용가능. 클래스의 상속은 강한 결합이기 때문에, 단순히 기능확장을 위해 클래스를 상속하는 것은 비효율. 

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
    // implementation of protocol requirements goes here
}
```

```swift
extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// Prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"


extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}


extension Int {
    func times(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}
3.times {
    print("Hello!")
}
// Hello!
// Hello!
// Hello!


extension Int {
    mutating func square() {
        self = self * self
    }
}
var someInt = 3
someInt.square()
// someInt is now 9


extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

## Error Handling
### Throwing

```swift
func vend(itemNamed name: String) throws {
        guard let item = inventory[name] else {
            throw VendingMachineError.invalidSelection
        }
        // ...
}
```

### do ~ catch

```swift
do {
    try /* expression */
    // statements
} 
catch pattern 1 {
    // statements
} 
catch pattern 2 where condition {
    // statements
}
```

```swift
func someThrowingFunction() throws -> Int {
    // ...
}
 
let y: Int?
do {
    y = try someThrowingFunction()
} 
catch {
    y = nil
}
```

### try?
try? 키워드는 뒤 문장을 수행한 후에 결과값을 Optional으로 반환하는데, 예외가 발생하면 nil을 반환한다.

```swift
func fetchData() -> Data? {
    if let data = try? fetchDataFromDisk() { return data }
    if let data = try? fetchDataFromServer() { return data }
    return nil
}
```

### Disabling Error Propagation

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```

### Specifying Cleanup Actions
You use a defer statement to execute a set of statements just before code execution leaves the current block of code.
```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

## Type Casting
### Checking Type

```swift
for item in library {
    if item is Movie {
        movieCount += 1
    } else if item is Song {
        songCount += 1
    }
}
```

### Downcasting
```swift
as?  as!
```
as?는 Optional을 반환하고, 변환에 실패하면 nil이 반환된다.

as!는 강제 변환. 실패하면 런타임 에러.
```swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: \(movie.name), dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: \(song.name), by \(song.artist)")
    }
}
```

### as
as 연산자는 upcasting, bridging의 경우에 사용되는 것으로, 컴파일 단계에서 성공이 보장되어 있는 경우에만 사용할 수 있다.

### Type Casting for Any and AnyObject
- **Any** can represent an instance of any type at all, including function types.
- **AnyObject** can represent an instance of any class type.

## Protocols
Java의 interface와 비슷한 개념.

property와 static 매서드도 포함시킬 수 있다.
```swift
protocol SomeProtocol {
    // protocol definition goes here
}

struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}

class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```

### Property Requirements

```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```

### Method Requirements

```swift
protocol SomeProtocol {
    [static | class] func someTypeMethod()
}
```

```swift
protocol RandomNumberGenerator {
    func random() -> Double
}

class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c).truncatingRemainder(dividingBy:m))
        return lastRandom / m
    }
}
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// Prints "Here's a random number: 0.37464991998171"
print("And another one: \(generator.random())")
// Prints "And another one: 0.729023776863283"


protocol Togglable {
    mutating func toggle()
}

enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
// lightSwitch is now equal to .on
```

### Initializer Requirements

```swift
protocol SomeProtocol {
    init()
}
 
class SomeSuperClass {
    init() {
        // initializer implementation goes here
    }
}
 
class SomeSubClass: SomeSuperClass, SomeProtocol {
    // "required" from SomeProtocol conformance; "override" from SomeSuperClass
    required override init() {
        // initializer implementation goes here
    }
}
```

## Generics

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}

struct Stack<Element> {
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}
```

### Type Constraints

```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

#### Generic Where Clauses

```swift
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {
        
        // Check that both containers contain the same number of items.
        if someContainer.count != anotherContainer.count {
            return false
        }
        
        // Check each pair of items to see if they are equivalent.
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }
        
        // All items match, so return true.
        return true
}
```

### Associated Types
When defining a protocol, it is sometimes useful to declare one or more associated types as part of the protocol’s definition. An associated type gives a placeholder name to a type that is used as part of the protocol. The actual type to use for that associated type isn’t specified until the protocol is adopted. Associated types are specified with the **associatedtype** keyword.

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```
