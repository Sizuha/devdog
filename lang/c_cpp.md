# C/C++

## static 함수
일반 함수에 static이 붙는 경우, 이는 해당 함수가 해당 파일 안에서만 사용될 수 있음을 뜻한다.

```c
/* a.c */
// 이 함수는 a.c 내에서만 사용된다.
static void testFunc() {
	// ...
}

/* b.c */
// 이 함수는 b.c 내에서만 사용된다.
static void testFunc() {
	// ...
}

// 이런 식으로 같은 함수명을 선언하고 있어도 파일간에 충돌이 일어나지 않는다.
```

## 함수 포인터
### 함수를 가리키는 포인터 형 선언
```c
// F가 함수형 포인터 변수가 된다.
return_type (*F)(arg_type1, arg_type2, ...);

// F를 함수형 타입으로 쓸 수 있다.
typedef return_type (*F)(arg_type1, arg_type2, ...);
```

### 맴버함수 포인터 (C++)
```cpp
return_type (ClassName::*F)(arg_type1, arg_type2, ...);
```

## mutable (C++)
본래 const로 상수지정된 객체는 바이너리 단위의 상수성을 가진다. 말 그대로 상수객체일 때는 단 1비트도 변경이 불가능하다.

그런데 어쩌면 바이너리 수준이 아니라 개념적 수준에서만 상수성이 필요할 수도 있다. 즉, 객체 바이너리 전체를 다 상수화 할 필요는 없고, 사용자 쪽에서 보기에 상수성이 유지되고 있다고 보여지면 그만일 경우. 달리 말하자면 상수화된 객체에서도 필요에 따라서는 내용이 변경될 수 있도록 할 수 있다.

다음과 같이 상수성을 해제할 맴버(필드)를 mutable로 선언한다.

```cpp
class X {
private:
	bool m_flag;
	mutable int m_accessCount;
public:
	bool GetFlag() const {
		// 상수화된 인스턴스에서도 mutable로 선언된 맴버는 변경 가능.
		m_accessCount++;
		return m_flag;
	}
};
```

## 문제 및 해결
### char -> wchar_t 변환

char* 문자열을 unicode 문자열(wchar_t\*) 타입으로 변환할 때는 char형이 signed 타입이라는 것에 주의해야 할 필요가 있습니다. 이것과 관련해서 제가 경험했던 삽질의 간단한 예를 하나 들어보겠습니다.

```cpp
void charToWChar(char src, wchar_t& out_dest) {
    out_dest =(wchar_t)src;// X : 이렇게 하면 안됨!!
}
```

얼핏보면 별 문제없이 잘 동작할 것 같은 함수인데, 사실은 중대한 버그가 서식하고 있습니다. 일단 char는 1 Byte에 signed(부호있는) 정수로 취급된다는 거고, wchar_t는 unsigned short(부호없는 2 Byte 정수)로 생각할 수 있는데, 여기서 src 인자가 음수(-) 값이 들어올 경우를 생각해 봅니다.

예를들어 src = -1 이라고 할때, -1은 바이트로 0xFF가 됩니다. 원래 의도는 0xFF(즉 255)가 그대로 2 Byte로 확장되서 0x00FF가 되어서 out_dest에 전달이 되어야 하죠.

하지만 실제 동작은 전혀 다르게 일어납니다. 우선 -1을 2 Byte로 확장 시킵니다. -1에 대한 2 Byte 표현은 0xFFFF가 됩니다. 그리고 이것을 unsigned로 인식하고 out_dest에 전달합니다. 그래서 out_dest에 대한 출력값은 65535(0xFFFF)가 됩니다.

따라서 원래 의도대로 동작시키 위해서는 unsigned char 형으로(2 Byte 정수로 확장되지 않게) 타입 캐스팅을 해줘야 합니다.

```cpp
void charToWChar(char src, wchar_t& out_dest) {
    out_dest =(unsigned char)src;
}
```
