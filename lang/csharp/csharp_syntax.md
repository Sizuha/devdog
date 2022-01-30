# C# 문법

## 선언

### const

상수 선언은 C++과는 다르게 대입하는 값이 정말로 상수인 경우만 쓸 수 있다. 즉, 컴파일 단계에서부터 상수성을 가져야 함. 그리고 자동으로 static 속성을 가짐.
```csharp
class Sample {
    const int TEST = 10;
}
```

### readonly

```csharp
class Sample {
    readonly int TEST;

    public Sample(int v) {
        TEST = v;
    }
}
```
초기화 단계에서만 값을 할당할 수 있고, 그 이후부터는 상수성을 가진다. 이쪽이 C++의 const 개념과 더 가까울지도.

## 자료형

### string

#### Verbatim string literals

```csharp
const string msg = @"c:\Docs\Source\a.txt";  // rather than "c:\\Docs\\Source\\a.txt"
```
문자열 상수 앞에 @기호를 붙인다.

일반 문자열 상수랑 비슷하지만, escape문자 표현을 무시하고 그대로 표시한다. windows 형식의 파일 경로 표시 등에 사용하기 좋다.

이 형식의 문자열에서, 따옴표(") 기호를 표현하기 위해서는 따옴표 기호를 연속해서 2번 쓰면 된다.
```csharp
const string msg = @"""Ahoy!"" cried the captain."; // "Ahoy!" cried the captain.
```

### formatting
String.Format, Console.Write, Console.WriteLine 등의 매서드에서 사용.
```{ index[,alignment][ :formatString] }```

#### Number

| Format Specifier | Description | Examples | Output |
| - | - | - | - |
|C or c |Currency |Console.Write("{0:C}", 2.5);<br/>Console.Write("{0:C}", -2.5); |$2.50<br/>($2.50) |
|D or d |Decimal |Console.Write("{0:D5}", 25); <br/>Console.Write("{0:00000}", 25); |00025 |
|E or e |Scientific |Console.Write("{0:E}", 250000); |2.500000E+005 | 
|F or f |Fixed-point |Console.Write("{0:F2}", 25);<br/>Console.Write("{0:F0}", 25); |25.00<br/>25 |
|G or g |General |Console.Write("{0:G}", 2.5); |2.5 |
|N or n |Number |Console.Write("{0:N}", 2500000); |2,500,000.00 |
|X or x |Hexadecimal |Console.Write("{0:X}", 250);<br/>Console.Write("{0:X}", 0xffff); |FA<br/>FFFF |

## 제어문

### foreach

```csharp
foreach (Type element in collection) {
  // TODO ...
}
```
foreach 블록 안에서는 collection에 요소를 추가/삭제 등이 불가능.

### switch
case 문의 마지막은 break나 goto case, return 등으로 마무리.
```csharp
switch (state) {
case AA:
case BB:
case CC:
  // TODO ...
  break;

case XX:
  // TODO ...
  break;

case YY:
  // TODO ...
  goto case ZZ;

case ZZ:
  // TODO ...
  goto default;

default:
  // TODO ...
  break;
}
```

case문 안에서 블럭{ }으로 감싸지 않아도 변수 선언이 가능하다.
```csharp
switch (state) {
case A:
  int sample = 0;
  // TODO . . .
  break;
}
````

## Class/Object

### 생성자
#### 부모 생성자 호출

```csharp
class Child : Parent {
    public Child() : base() {
        // ...
    }
}
```

#### 다른 생성자 호출 

```csharp
class Sample {
    public Sample() {
        // ...
    }

    public Sample(int param) : this() {
        // ...
    }
}
```

### 추상 클래스 

```csharp
abstract class XXX {
    abstract public void fff();
}
```
추상 매서드를 선언하기 위해서는 Class도 추상 클래스로 선언되어야 한다.

class 앞에 **abstract** 키워드를 붙인다.

### 인터페이스

```csharp
interface XXX {

}
````
Java의 인터페이스 개념과 거의 같다.

### Static Class
클래스 내의 모든 필드가 static 필드로 구성되는 클래스.

일반적인 생성자는 쓸 수 없지만, static 필드를 초기화 시키기 위해 static 생성자를 활용.
```csharp
public static class TestClass {
   private static int ver;

   static TestClass() { 
      ver = 1;
   }
}
```

### Method
#### 가상 매서드

```csharp
// 선언
virtual Method() { ... }

// 오버라이드
override Method() { ... }
```

#### 추상 매서드

```csharp
// 선언
abstract Method();

// 오버라이드
override Method() { ... }
```

#### out 파라매터
C에서 포인터를 이용해 매서드의 인자를 통해 값을 내보내는 것과 비슷한 역할을 한다.

```csharp
// 선언.
void OutputMethod(out AnyObject o) { ... }

// 호출.
AnyObject x = new AnyObject();
OutputMethod(out x);
```
주의할건, 매서드를 호출할 때도 인자에 out 키워드를 써줘야 한다는 것.

#### 가변 파라매터
```params Type[]``` 형태로 인수를 선언할 경우, 매소드 내부에서는 배열처럼 처리하면 되지만, 매소드를 호출 할 때는 가변 파라매터 형태로 넘길 수 있다.

```csharp
public class MyClass
{
    public static void UseParams(params int[] list)
    {
        for (int i = 0; i < list.Length; i++)
        {
            Console.Write(list[i] + " ");
        }
        Console.WriteLine();
    }

    public static void UseParams2(params object[] list)
    {
        for (int i = 0; i < list.Length; i++)
        {
            Console.Write(list[i] + " ");
        }
        Console.WriteLine();
    }

    static void Main()
    {
        // You can send a comma-separated list of arguments of the  
        // specified type.
        UseParams(1, 2, 3, 4);
        UseParams2(1, 'a', "test");

        // A params parameter accepts zero or more arguments. 
        // The following calling statement displays only a blank line.
        UseParams2();

        // An array argument can be passed, as long as the array 
        // type matches the parameter type of the method being called. 
        int[] myIntArray = { 5, 6, 7, 8, 9 };
        UseParams(myIntArray);

        object[] myObjArray = { 2, 'b', "test", "again" };
        UseParams2(myObjArray);

        // The following call causes a compiler error because the object 
        // array cannot be converted into an integer array. 
        //UseParams(myObjArray); 

        // The following call does not cause an error, but the entire  
        // integer array becomes the first element of the params array.
        UseParams2(myIntArray);
    }
}
/*
Output:
    1 2 3 4
    1 a test

    5 6 7 8 9
    2 b test again
    System.Int32[]
*/
```

## 구조체(struct)
class와 거의 유사하지만, class는 참조 타입인 반면 struct는 값 타입으로 취급된다.
```csharp
struct OneStruct {
// . . .
}

// 선언 & 할당
OneStruct s = new OneStruct();

// 값 타입이기 때문에, 다음 처럼 null을 할당할 수 없다.
OneStruct s = null; // ERROR!!
```

## 자료구조

### List 

### Dictionary
선언
```csharp
using System.Collections.Generic;

Dictionary<key_type, value_type> dic;
```

foreach 사용
```csharp
foreach( KeyValuePair<string, string> kvp in myDictionary )
{
    Console.WriteLine("Key = {0}, Value = {1}", kvp.Key, kvp.Value);
}
```

## 동기화
### Synchronized Method
Method에 ```[MethodImpl(MethodImplOptions.Synchronized)]``` 옵션을 부여.

```csharp
using System.Runtime.CompilerServices;
using System.Threading;

public class AAA
{
    [MethodImpl(MethodImplOptions.Synchronized)]
    public void MMM()
    {
        // ...
    }
}
```

## Delegates
C의 함수 포인터 개념과 비슷.

객체의 매서드에 대한 레퍼런스를 관리하는 것이라고 보면 되겠다.

```csharp
// 델리게이트 타입 선언 (예)
public delegate int PerformCalculation(int x, int y);

// 델리게이트 변수 
PerformCalculation pc;

// 델리게이트 추가
pc += user_delegate;

// 델리게이트 제거
pc -= user_delegate;
```

## using 문법

Provides a convenient syntax that ensures the correct use of IDisposable objects.
- http://msdn.microsoft.com/ja-jp/library/yh598w02.aspx

```csharp
using (Font font1 = new Font("Arial", 10.0f)) 
{
    byte charset = font1.GdiCharSet;
}
```
File and Font are examples of managed types that access unmanaged resources (in this case file handles and device contexts). There are many other kinds of unmanaged resources and class library types that encapsulate them. All such types must implement the IDisposable interface.

As a rule, when you use an IDisposable object, you should declare and instantiate it in a using statement. The using statement calls the Dispose method on the object in the correct way, and (when you use it as shown earlier) it also causes the object itself to go out of scope as soon as Dispose is called. Within the using block, the object is read-only and cannot be modified or reassigned.

The using statement ensures that Dispose is called even if an exception occurs while you are calling methods on the object. You can achieve the same result by putting the object inside a try block and then calling Dispose in a finally block; in fact, this is how the using statement is translated by the compiler. The code example earlier expands to the following code at compile time (note the extra curly braces to create the limited scope for the object):

### Alias

```csharp
using System;
using MyString = Ufcpp.String;           // クラスのエイリアス(alias)
using MyCollections = Ufcpp.Collections; // 名前空間のエイリアスも作れる

class NameSpaceTest
{
  static void Main()
  {
    String str = new String("test");
    //↑ System.String が参照される
    MyString str = new MyString("test");
    //↑ Ufcpp.String が参照される
    MyCollections.List list = new MyCollections.List();
    //↑ Ufcpp.Collections.List が参照される
  }
}
```

#### エイリアス修飾子 (2.0)

```csharp
namespace Ufcpp.Test.Utilities
{
  class Image {}
}

namespace TestNamespace
{
  using Util = Ufcpp.Test.Utilities; // エイリアスをつける。

  class Program
  {
    static void Main(string[] args)
    {
      Util.Image img = new Util.Image();
    }
  }
}
```

## Debug

### Assertions
참고: https://msdn.microsoft.com/en-us/library/ttcc4x86(v=vs.110).aspx

```csharp
using System.Diagnostics;

//디버그 모드
Debug.Assert(true);

//릴리즈 모드
Trace.Assert(true);
```

## C# 2.0

### Nullable
int, float 등의 value형에 null값도 저장할 수 있는 특수한 형식. (주로 DB등에서 필요)
```csharp
int? i = 512;
i = null;
```
 
**int?** 는 ```Nullable<int>```와 같은 의미.

### ?? 연산자
```
a ?? b
```

a가 null 일 경우는 b를 반환하고, a가 null이 아니라면 a를 반환. 단, a와 b는 동일한 타입이어야 한다.
```csharp
object obj1 = null;
object obj2 = new object();
object obj3 = new object();
return obj1 ?? obj2 ?? obj3; // obj2 を返す
```

### 무명 메서드

http://msdn.microsoft.com/ko-kr/library/0yw3tz5k(v=vs.110).aspx

메서드를 선언하는 대신에, 메서드 이름 대신 delegate 키워드를 써서 람다 식 처럼 메서드를 정의.

```csharp
// Create a handler for a click event.
button1.Click += delegate(System.Object o, System.EventArgs e)
                   { System.Windows.Forms.MessageBox.Show("Click!"); };
```

### Generics (ジェネリック)

#### 제너릭 매소드

```csharp
アクセスレベル 戻り値の型 メソッド名<型引数>(引数リスト)
    where 型引数中の型が満たすべき条件
{
    メソッド定義
}

// Example.
public static Type Max<Type>(Type a, Type b) where Type : IComparable {
    return a.CompareTo(b) > 0 ? a : b;
}
```

#### 제너릭 클래스 

```csharp
class クラス名<型引数>
    where 型引数中の型が満たすべき条件
{
    クラス定義
}

// Example.
class Stack<Type>
{
    Type[] buf;
    int top;
    public Stack(int max) { this.buf = new Type[max]; this.top = 0;}
    public void Push(Type val) { this.buf[this.top++] = val; }
    public Type Pop(){ return this.buf[--this.top]; }
    public int Size{ get{return this.top; } }
    public int MaxSize{ get{ return this.buf.Length; } }
}
```

## C# 3.0

### var (암묵적 변수선언)

```csharp
var i = 10; // implicitly typed
int i = 10; //explicitly typed
```
var로 선언된 변수는 선언된 시점에서는 타입이 정해지지 않고, 최초에 대입이 일어났을 때 대입된 값에 의해 타입이 결정된다. (컴파일러가 적절한 타입을 판단해서 적용한다.)

변수선언에서 타입을 명시적으로 쓰는 것을 생략한 것일 뿐이고, 이거 자체가 만능 타입의 변수가 되는 건 아님.

var는 지역 변수를 선언할 때만 쓸 수 있다.
```csharp
// Example #1: var is optional because
// the select clause specifies a string
string[] words = { "apple", "strawberry", "grape", "peach", "banana" };
var wordQuery = from word in words
                where word[0] == 'g'
                select word;

// Because each element in the sequence is a string, 
// not an anonymous type, var is optional here also.
foreach (string s in wordQuery)
{
    Console.WriteLine(s);
}

// Example #2: var is required because
// the select clause specifies an anonymous type
var custQuery = from cust in customers
                where cust.City == "Phoenix"
                select new { cust.Name, cust.Phone };

// var must be used because each item 
// in the sequence is an anonymous type
foreach (var item in custQuery)
{
    Console.WriteLine("Name={0}, Phone={1}", item.Name, item.Phone);
}
```

### 자동생성 프로퍼티

```csharp
public int Value { get; set; }
```

위 선언은 다음과 같은 의미다.
```
private int __value;
public int Value
{
  get { return __value; }
  set { __value = value; }
}
```

### 람다(lamda)

http://msdn.microsoft.com/ko-kr/library/bb397687(v=vs.110).aspx

익명 함수와 비슷하게 동작하는데, 표현이 단순하고 매개 변수의 형을 유추할 수 있으며 보통 2,3개의 짧은 식으로 내용이 구성된다. 매개 변수에 람다를 지정할 수 있고, 리턴 값에 적용할 수도 있다.

#### 람다 식(Expression Lambdas)

```csharp
// =>을 기준으로 왼쪽 괄호()에 매개 변수 리스트를, 오른쪽에 식을 구현. 매개 변수의 형은 자동으로 유추된다.
(x, y) => x == y

// 형을 자동으로 유추할 수 없는 경우, 다음과 같이 직접 형을 지정할 수 있다.
(int x, string s) => s.Length > x

// 매개 변수가 없는 경우
() => SomeMethod()
```

#### 람다 문(Statement Lambdas)

```csharp
(input parameters) => {statement;}
```

#### 람다의 활용

```csharp
// 람다형 변수를 선언해서 활용
Func<int, bool> myFunc = x => x == 5;
bool result = myFunc(4); // returns false of course

// 인수를 Func<paramTypes, returnType> 형식으로 지정하면, 람다를 인수로 받을 수 있다.
int[] numbers = { 5, 4, 1, 3, 9, 8, 6, 7, 2, 0 };
int oddNumbers = numbers.Count(n => n % 2 == 1);
```

## C# 4.0

## C# 5.0
