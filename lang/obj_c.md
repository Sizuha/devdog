# Objective C

## 지시어
Objective-C에서 컴파일러 지시어는 @문자로 시작된다.

### @"문자열"
NSString 문자열 상수 객체. 

```objc
NSString *str = @"Sample String";
```

### @class

클래스 선언. C++에서 class 키워드로 클래스임을 표시하는 것과 비슷함.<br />구현 내용을 import하지 않고 단지 class를 레퍼런스로 참조만 하고자 할 때 사용.

```objc
@class Point, Rectangle;
```

### @interface _className_: _parentClass_ <_protocol_, ...>
클래스의 인터페이스 부분 정의.

### @try, @catch, @finally, @throw
예외처리

## String

### NSString to char*

```objc
NSString* nsStr = @"hi I'm NSString";
const char* strTmp = [nsStr UTF8String];
```

### char* to NSString

```objc
char szText[] = "hi...";
NSString* nsStr = [NSString stringWithUTF8String: szText];
```

### NSData to NSString

```objc
NSData *recieveData = . . . ;
NSString *strData = [[NSString alloc]initWithData:recieveData encoding:NSUTF8StringEncoding];
NSLog(@"data:%@", strData);
```

## Object
### nil 체크

```objc
if (!obj) { ... }
```
