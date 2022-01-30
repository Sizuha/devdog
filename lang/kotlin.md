# Kotlin

Google에서 Android 개발언어로 공식 지원 결정.

Android Studio 3.0 부터 지원.

- 공식 사이트
https://kotlinlang.org/docs/reference/android-overview.html

## 변수/상수 선언

```java
// Java
String s = "abc";
final String t = "xyz";
```

```kotlin
// Kotlin
var s: String = "abc"
val t: String = "xyz"

var s = "abc"
val t = "xyz"
```

## Basic Types
### Numbers 

| Type | Bit width |
| ---- | --------- |
| Double | 64 |
| Float | 32 |
| Long | 64 |
| Int | 32 |
| Short | 16 |
| Byte | 8 |

#### Literal Constants

```
Decimals: 123
Long: 123L
Hexadecimals: 0x0F
Binaries: 0b00001011
Doubles: 123.5, 123.5e10
Floats: 123.5f
```

```kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

### Characters

```kotlin
Char
```

Character literals go in single quotes: '1'. 

Special characters can be escaped using a backslash. 

The following escape sequences are supported: \t, \b, \n, \r, \', \", \\ and \$. 

To encode any other character, use the Unicode escape sequence syntax: '\uFF00'.

### Booleans

```kotlin
var b: Boolean = true
b = false
```

### Arrays

```kotlin
// Creates an Array<String> with values ["0", "1", "4", "9", "16"]
val asc = Array(5, { i -> (i * i).toString() })

val x: IntArray = intArrayOf(1, 2, 3)
x[0] = x[1] + x[2]
```

### String

```kotlin
val s: String = "Hello, world!\n"
```

A raw string is delimited by a triple quote ("""), contains no escaping and can contain newlines and any other characters:
```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

#### trimMargin

```kotlin
fun main(args: Array<String>) {
    println("---")
    println("""
    |for (i = 0; i < 100; ++i)
      |console.log(i)
    """.trimMargin())
    println("---")
    println("""
    > a
    > b
    > c
    > d
    """.trimMargin("> "))
    println("---")
}
```

결과
```
---
for (i = 0; i < 100; ++i)
console.log(i)
---
a
b
c
d
---
```

#### trimIndent

```kotlin
fun main(args: Array<String>) {
    println("---")
    println("""
    for (i = 0; i < 100; ++i)
      console.log(i)
    """.trimIndent())
    println("---")
}
```
결과
```
---
for (i = 0; i < 100; ++i)
  console.log(i)
---
```

#### String Templates

```kotlin
val i = 10
val s = "i = $i" // evaluates to "i = 10"

val s = "abc"
val str = "$s.length is ${s.length}" // evaluates to "abc.length is 3"
```

Templates are supported both inside raw strings and inside escaped strings. If you need to represent a literal $ character in a raw string (which doesn't support backslash escaping), you can use the following syntax:
```kotlin
val price = """
${'$'}9.99
"""
```

## Packages
### Imports

```kotlin
import foo.Bar // Bar is now accessible without qualification
import foo.* // everything in 'foo' becomes accessible
import bar.Bar as bBar // bBar stands for 'bar.Bar'
```

## Control Flow
### If

```kotlin
// Traditional usage 
var max = a 
if (a < b) max = b

// With else 
var max: Int
if (a > b) {
    max = a
} 
else {
    max = b
}
 
// As expression 
val max = if (a > b) a else b

val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```

### When
when replaces the switch operator of C-like languages. In the simplest form it looks like this
```kotlin
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> { // Note the block
        print("x is neither 1 nor 2")
    }
}

when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}

when (x) {
    parseInt(s) -> print("s encodes x")
    else -> print("s does not encode x")
}

when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}

// As expression (1)
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}

// As expression (2) 
val groupCode  = when(number) {
    0 -> 0
    in 1..10 -> 1
    in 11..100 -> 2
    else -> 3
}

when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}
```

### For Loops

```kotlin
for (item: Type in Collection) {
    // ...
}
```

```kotlin
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```

例） 1 ~ 100 까지 루프
```kotlin
// 기본적으로 range 표현을 이용한다.

// 1.
for (i in 1..100) { ... }

// 2.
(1..100).forEach { i -> }
```

### While Loops

```kotlin
while (x > 0) {
    x--
}

do {
    val y = retrieveData()
} while (y != null) // y is visible here!
```

### Returns and Jumps
**return**, **break**, **continue**

#### Break and Continue Labels

```kotlin
loop@ for (i in 1..100) {
    for (j in 1..100) {
        if (...) break@loop
    }
}
```

#### Return at Labels

```kotlin
fun foo() {
    ints.forEach lit@ {
        if (it == 0) return@lit
        print(it)
    }
}

fun foo() {
    ints.forEach {
        if (it == 0) return@forEach
        print(it)
    }
}
```

## Operator
### Unary operations
| Expression | Translated to |
| - | - |
| +a | a.unaryPlus() |
| -a | a.unaryMinus() |
| !a | a.not() |
| a++ | a.inc() |
| a-- | a.dec() |

### Binary operations
| Expression | Translated to |
| - | - |
| a + b | a.plus(b) |
| a - b | a.minus(b) |
| a * b | a.times(b) |
| a / b | a.div(b) |
| a % b | a.rem(b) |
| a..b | a.rangeTo(b) |
| a in b | b.contains(a) |
| a !in b | !b.contains(a) |

### Indexed access operator
| Expression | Translated to |
| - | - |
| a\[i] | a.get(i) |
| a\[i, j] | a.get(i, j) |
| a\[i] = b | a.set(i, b) |
| a\[i, j] = b | a.set(i, j, b) |

### Invoke operator
| Expression | Translated to |
| - | - |
| a() | a.invoke() |
| a(i) | a.invoke(i) |

### Augmented assignments
| Expression | Translated to |
| - | - |
| a += b | a.plusAssign(b) |
| a -= b | a.minusAssign(b) |
| a *= b | a.timesAssign(b) |
| a /= b | a.divAssign(b) |

### Equality and inequality operators
| Expression | Translated to |
| - | - |
| a == b | a?.equals(b) ?: (b === null) |
| a != b | !(a?.equals(b) ?: (b === null)) |

### Comparison operators
| Expression | Translated to |
| - | - |
|a > b	|a.compareTo(b) > 0 |
|a < b	|a.compareTo(b) < 0 |
|a >= b	|a.compareTo(b) >= 0 |
|a <= b	|a.compareTo(b) <= 0 |

## Range
https://kotlinlang.org/docs/reference/ranges.html

1에서 100까지 (100 포함)
```kotlin
for (i in 1..100) {
    print(i)
}
```

1에서 99까지(100 미포함)
```kotlin
for (i in 1 until 100) {
    print(i)
}
```

100에서 1로 (1 포함)
```
for (i in 100 downTo 1) {
    print(i)
}
```

0부터 100까지, 단 2씩 증가
```kotlin
for (i in 0..100 step 2) {
    print(i)
}
```

100에서 0까지, 단 2씩 감소
```kotlin
for (i in 100 downTo 0 step 2) {
    print(i)
}
```

## Class

```kotlin
class Some {

}
```

### Constructors

```kotlin
class Person constructor(firstName: String) {
}
//--- or ---//
class Person(firstName: String) {
}
```

```kotlin
class AClass(some: String) {	
	lateinit var field: String

	init {
		field = some
	}
}
//--- or ---//
class AClass(some: String) {	
	val field: String = some
}
//--- or ---//
class AClass(val field: String) {	
}
```

```kotlin
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}

class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

#### initializer blocks

```kotlin
class Constructors {
    init {
        println("Init block")
    }

    constructor(i: Int) {
        println("Constructor")
    }
}
```
init 블록은 constructor 블록 정의 보다도 먼저 수행됨.

#### Creating instances of classes 

```kotlin
class Customer(val customerName: String = "") {}

val customer = Customer("Joe Smith")
```

### Properties

```kotlin
class AClass {
    var name: String
        get() = this.toString()
        set(value) { name = value }
}
```

읽기전용으로 만들기
```kotlin
class AClass {
    var name: String
        get() = this.toString()
        private set
}
```

### Nested and Inner Classes

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```
Inner Class는 자신이 선언된 클래스 영역의 맴버에 바로 접근이 가능하다.

#### Anonymous inner classes

```kotlin
window.addMouseListener(object: MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {
        // ...
    }
                                                                                                            
    override fun mouseEntered(e: MouseEvent) {
        // ...
    }
})
```

### Inheritance

```kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```
Kotlin은 기본적으로 final class이다. open class로 선언해 줘야 상속 가능. 매서드도 마찬가지.

## interface
Java처럼 **interface** 키워드로 정의.
```kotlin
// 정의
interface ISome {
	var fieldA: String // 프로퍼티
	val fieldB: String // 프로퍼티

	fun testA()
	fun testB(): Boolean { return true } // 기본 동작을 지정할 수 있다
}

// 사용
class SomeClass : ISome {
	override var fieldA
		get() = "TEST"
		private set

	override val fieldB = "TEST2"
	override fun testA { /* ... */ }
}
```

## Generics
### 제네릭 함수

```kotlin
fun <T> someFunc() { ... }

// T에 대한 타입 조건을 추가
fun <T: SomeType> someFunc() { ... }

// T에 대한 여러개의 타입 조건을 추가
fun <T> someFunc() 
where T: SomeTypeA, T: SomeTypeB
{ ... }

// 물론 제네릭 타입을 여러개 지정할 수도 있다
fun <E, R> someFunc() { ... }
```

### 제네릭 클래스
class 혹은 interface 등에 제네릭을 지정할 수 있다.
```kotlin
class SomeClass<T> {
// . . .
}
```

마찬가지로 타입 제한을 걸 수도 있다.
```kotlin
class SomeClass<T: SomeType> {
// . . .
}

class SomeClass<T> 
where T: SomeTypeA, T: SomeTypeB {
// . . .
}
```

#### 宣言側変性(Declaration-site variant)
제네릭 타입을 **out** 키워드로 선언하면 리턴 타입으로만 사용이 제한된다.
```kotlin
interface ISome<out T> {
	fun someFunc(): T
}
```

반대로 **in** 키워드로 선언된 타입은 인수로서만 사용된다.
```kotlin
interface ISome<in T> {
	fun someFunc(a: T)
}
```

## Annotation 정의 

```kotlin
@Target(AnnotationTarget.FIELD, AnnotationTarget.PROPERTY)
annotation class Column(
        val name: String,
        val notNull: Boolean = false,
        val unique: Boolean = false,
        val exclude: Boolean = false
)
```
**annotation class** 키워드로 어노테이션 클래스를 작성한다.<br/>어노테이션에서 취급하는 인수의 타입은 기본 타입만 지정할 수 있다.

**@Target()** 어노테이션으로 해당 어노테이션이 어디에 붙을 수 있는지 제한을 둔다.

## Reflection
Android 프로젝트에서, build.gradle (Module) 설정
```
dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    implementation "org.jetbrains.kotlin:kotlin-reflect:$kotlin_version"
}
```

### 클래스 맴버 탐색
**타입명::class.memberProperties**는 맴버 변수, 프로퍼티를 탐색할 수 있다.
```kotlin
for (member in some::class.memberProperties) {
	// 어노테이션 탐색(1)
	member.annotations.forEach { a: Annotation -> 
		// ...
	}

	// 어노테이션 탐색(2)
	member.findAnnotation<AnnotationType>()?.let { /* ... */ }
}
```

### 타입 확인
클래스 맴버의 리턴 타입을 확인.
```kotlin
for (member in some::class.memberProperties) {
	when (member.returnType) {
		Int::class.createType() -> { /* ... */ }
		else -> { /* ... */ }
	}
}
```
**타입명::class.createType()**으로 비교하는 것에 주의.

### 맴버에 읽고 쓰기

```kotlin
// member: KMutableProperty<*>

// 읽기
val r = member.getter.call(...)

// 쓰기
member.setter.call(...)
```

#### private 맴버에 대한 접근

```kotlin
val accessible = member.isAccessible
member.isAccessible = true
member.setter.call(tableObj, value)
member.isAccessible = accessible
```

## apply, also, run, let
다음과 같이 class를 정의했다고 할 때,
```kotlin
class NameCard {
    var name = ""
    var tel = ""
    var email = ""
}
```

apply의 경우
```kotlin
val newCard: NameCard = NameCard().apply { // this = NameCard()
    name = "xxxxxx"
    tel = "000-0000-0000"
    email = "xxxx@gmali.com"
    // 자동으로 this를 넘겨줌
}
```

also의 경우
```kotlin
val newCard: NameCard = NameCard().also { // it = NameCard()
    it.name = "xxxxxx"
    it.tel = "000-0000-0000"
    it.email = "xxxx@gmali.com"
    // 자동으로 it을 넘겨줌
}
```

run의 경우
```kotlin
val newCard: NameCard()
newCard.run { // this = newCard
    name = "xxxxxx"
    tel = "000-0000-0000"
    email = "xxxx@gmali.com"
}
```

let의 경우
```kotlin
val newCard: NameCard()
newCard.let { // it = newCard
    it.name = "xxxxxx"
    it.tel = "000-0000-0000"
    it.email = "xxxx@gmali.com"
}
```
