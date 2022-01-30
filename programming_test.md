# 프로그래밍 기초 테스트
## Lv.0 기본 개념을 숙지하고 있는지 확인하기 위한 문제들
실제 코드를 작성하는게 아니라, 간단히 답을 할 수 있는 문제들

### Q1
以下のソースには一つ重大な問題がある。どう修正すれば良いか？

```java
import java.util.LinkedList;
import java.util.List;

public final class Sample {
    static class NameCard {
        String name;
        String email;
    }

    public static void main(String[] args) {
        final List<NameCard> nameCards = new LinkedList<>();
        final String[] names = {"Person A","Person B","Person C",};
        final String[] emails = {"a@sample.co.jp","b@sample.co.jp","c@sample.co.jp",};

        final NameCard newCard = new NameCard();
        for (int i=0; i<names.length; i += 1) {
            newCard.name = names[i];
            newCard.email = emails[i];
            nameCards.add(newCard);
        }

        for (NameCard card: nameCards) {
            System.out.println("\n-------------------");
            System.out.println(card.name);
            System.out.println(card.email);
            System.out.println("-------------------");
        }
    }
}
```

### Q2
IDiceインタフェースの内容を作成してください。

```java
interface IDice {
    // ここの内容を作成してください
}

class Dice6 implements IDice {
    public int getMin() { return 1; }
    public int getMax() { return 6; }

    protected int checkLimit(int value) {
        final int min = getMin();
        final int max = getMax();

        if (value < min) return min;
        else if (value > max) return max;
        else return value;
    }

    public int roll(int bonus) {
        final int result = new Random().nextInt(6) + 1 + bonus;
        return checkLimit(result);
    }

    public int[] rollTimes(int times) {
        final int[] result = new int[times];
        for (int i=0; i<times; i++) result[i] = roll(0);
        return result;
    }
}

class Dice12 implements IDice {
    public int getMin() { return 1; }
    public int getMax() { return 12; }

    protected int checkLimit(int value) {
        final int min = getMin();
        final int max = getMax();
        return (value < min) ? min : (value > max) ? max : value;
    }

    public int roll(int bonus) {
        final int result = new Random().nextInt(12) + 1 + bonus;
        return checkLimit(result);
    }

    public int[] rollTimes(int times) {
        final int[] result = new int[times];
        for (int i=0; i<times; i++) result[i] = roll(0);
        return result;
    }
}

class Dice3 implements IDice {
    public int getMin() { return 0; }
    public int getMax() { return 2; }

    protected int checkLimit(int value) {
        if (value < 0) return 0;
        if (value > 2) return 2;
        return value;
    }

    public int roll(int bonus) {
        final int result = new Random().nextInt(3) + bonus;
        return checkLimit(result);
    }

    public String[] rollTimes(int times) {
        final String[] result = new String[times];
        for (int i=0; i<times; i++) {
            final int dice = roll(0);
            switch (dice) {
                case 1: result[i] = "Good"; break;
                case 2: result[i] = "Best";  break;
                default: result[i] = "Bad";  break;
            }
        }
        return result;
    }
}
```

### Q3
下記のJavaコードを実行した場合、予想される結果とその理由は？

```java
public final class Sample2 {
    static int power(int a, int b) {
        int result = 1; // 4byte integer type
        for (int i=0; i<b; i++) result *= a;
        return result;
    }

    public static void main(String[] args) {
        final int result = power(2, 31);
        System.out.println(String.format(">> %d", result));
    }
}
```

## Lv.1 기본적인 문법 사용

### Q1. swap
a = 10; b = 20; 여기서 a와 b의 값을 서로 바꿔서 넣기. (swap)

```java
int a = 10;
int b = 20;

/* a는 b의 값을, b는 a의 값을 가지게 해보자 */
// TODO ...
```

### Q2
1. 1부터 100까지의 합을 계산
1. 1 ~ 100까지의 짝수의 합을 계산

### Q3
1. 2 ~ 99까지의 숫자에서 소수(1과 자기 자신으로만 나누어 떨어지는 수)만 구하기(소수만 출력하기)
1. 2 ~ 99까지의 숫자에서 소수를 찾는데, 소수를 10개까지만 구하기

## Lv.2 함수, 제어문 활용

### Q1
int형 인수 a와 b를 넘겨 받아서, a나 b 중에 큰 쪽을 반환(return)하는 함수를 작성. (만약 a == b인 경우는 둘중 아무거나 반환해도 된다.)

### Q2
int형 인수 a와 b를 받아서, a를 b회만큼 곱한 결과를 반환. 즉, a의 b제곱을 구하는 함수를 작성. (단 b는 0 보다 큰 값이 들어온다고 가정한다.)

### Q3. 1 ~ n까지의 합
1. 정수 n을 입력 받아서, 1 ~ n까지의 합을 구하는 함수를 작성.
1. 위 문제를, 제어문(for, if 등)을 사용하지 않고 풀어보기.

### Q4. 소비세 계산
입력받은 가격(엔)을 넘겨받아, 소비세 8% 포함된 가격을 계산해서 반환하는 매서드를 작성.
1엔 이하는 반올림으로 계산해서, 결과값은 반드시 정수로 반환.

예) 93 => 100

※ 반올림 처리는 라이브러리에 있는 수학 함수(round 등)를 쓰지 말고, 직접 계산해 볼 것.

## Lv.3 배열, 컬렉션 자료구조 활용

### Q1
int형 배열 a에 1부터 10까지의 수를 채워넣고, 배열 a로부터 10, 9, 8, ... 1 순으로 값을 꺼내서 출력

### Q2
#### Q2-1
```java
int[] a = new int[] {53, 74, 57, 83, 25, 47, 42, 48, 57, 25, 20, 42, 96, 31};

// 배열 a에서 가장 큰 수를 찾아낼 것.
```

#### Q2-2
위 Q2-1문제에서, 배열 a의 내용을 오름차순(작은 숫자에서 큰 숫자 순서로)으로 정렬(sort)할 것.

#### Q2-3
위 Q2-1문제에서, 배열 a의 내용을 내림차순(큰 숫자에서  작은 숫자 순서로)으로 정렬(sort)할 것.

### Q3
다음 문장에서 문자 F(대소문자 포함)가 몇개 포함되어 있는지 세기
> Four score and seven years ago our fathers brought forth on this continent a new nation, conceived in liberty, and dedicated to the proposition that all men are created equal.

### Q4. 알파벳 갯수 세기
아래와 같이 영어 한 문장을 입력받아서, 입력 받은 문장 안에서 알파벳 a ~ z가 각각 몇번씩 사용되었는지 계산. (대소문자는 구분하지 않는다)

입력 예시:
> Java provides a data structure, the array, which stores a fixed-size sequential collection of elements of the same type. An array is used to store a collection of data, but it is often more useful to think of an array as a collection of variables of the same type.

출력 예시:
```
 a: 00
 b: 00
 . . .
 z: 00
```

### Q5. 테트리스
Q. 다음 6x4 배열의 내용을 반시계방향으로 90도 회전 시킨 4x6 배열을 만드는 코드를 작성.

```
char[][] board = { 
	{'＊','＊','　_','_　','_　','_　'}, 
	{'＊','＊','_　','_　','_　','_　'}, 
	{'＊','＊','＊','＊','＊','＊'}, 
	{'＊','＊','＊','＊','＊','＊'}
};
```

```
변경전:
＊＊____
＊＊____
＊＊＊＊＊＊
＊＊＊＊＊＊

변경후:
__＊＊
__＊＊
__＊＊
__＊＊
＊＊＊＊
＊＊＊＊
```

## Lv.4 응용편

### Q1.  히라가나 -> 카타카나 변환
입력받은 문자열 source에서, 히라가나를 카타가나로 변환해서 반환하는 매서드를 작성.

입력 예:
```
きたこれ
```

출력 예:
```
キタコレ
```

### Q2. 피보나치 수열
정수 n을 입력받아,  1 1 부터 시작되는 피보나치 수열 n번째 까지 출력하는 코드를 작성.

입력 예:
```
10
```

출력 예:
```
1 1 2 3 5 8 13 21 34 55
```

### Q3. 빙고 판독기
다음과 같이, 5x5 크기 빙고판의 상태를 입력받는다.

입력 예:
```
 1번째 행: OXOXO
 2번째 행: XOXXO
 3번째 행: OXOXO
 4번째 행: XOXOO
 5번째 행: OOXXO
```
그러면, O 마크가 표시된 것만을 대상으로, 가로 세로 대각선으로 5개의 O마크가 연속된게 있으면 1점씩 처리. 그래서 총 몇점인지 계산해서출력한다.

출력 예:
```
2
```

### Q4. 중복되지 않는 숫자조합 발생기
0 ~ 9까지의 숫자를 중복되지 않게 조합해서 만들 수 있는 모든 3자리 정수를 출력할 것.

단, 이때 정수의 맨 앞자리가 0이 되어서는 안된다. (예: 012 같은 경우)

출력 예:
```
102, 103, 104, 105, 106, 107, 108, 109, 120, 123, 124, 125, 126, 127, 128, 129, 130, 131, ..., 987
```

#### Q4-1
위 문제에서, 3자리 정수만이 아니라, 정수 n(1 ~ 10 범위내)을 입력 받아서 n자리수 조합을 모두 출력할 것.

### Q5. XML Parsing
다음과 같이 한개의 XML 태그를 포함하고 있는 문자열을 입력 받아서, Key/Value를 구분해서 저장할 것.

입력예:
```
<title>Test Sample</title>
```

출력예:
```
 key: title
 value: Test Sample
```

# 알고리즘 문제

## 계산기
"1+2*10/2" 같은 사칙연산이 포함된 수식을 문자열로 받아서, 그 계산 결과를 반환(float형)하는 함수를 작성.

```java
float calc(String exp) {
  return /* exp문자열을 해석해서 연산 결과를 반환. */
}
```

제한 사항
- 수식에 포함되는 숫자는 모두 0이상의 정수이고, 음수는 포함되지 않는다고 가정한다.
- 수식에 숫자와 연산자 이외에, 공백 등의 다른 문자들은 포함되지 않는다고 가정한다.
- 사칙연산(+ - * /)만 수행한다.
- 또한 수학 연산의 우선순위는 적용하지 않는다. 무조건 왼쪽부터 오른쪽 순으로 계산한다.

## 암호산술
### Ver. 1
다음과 같이 A ~ Z까지 알파벳으로 구성된 수식에서, 각각의 알파벳에 어떤 숫자가 들어가야 수식이 성립되는지 구하는 코드를 작성.

입력 예:
```
수식 입력: SEND+MORE
결과 입력: MONEY
```

위 경우, SEND+MORE = MONEY 이 수식을 만족하는 모든 숫자 조합을 출력한다.

출력 예:
```
SEND+MORE = MONEY
S=9 E=5 N=6 D=7 M=1 O=0 R=8 Y=2
```

물론 수식을 만족하는 조합은 여러 개가 있을 수 있으므로, 성립되는 모든 숫자 조합을 출력할 것.
<br/>(※답이 존재하지 않을 수도 있다. 이 경우는 답 없음으로 처리.)

제한 사항
- 숫자 종류가 0 ~ 9 이므로, 수식에 등장하는 알파벳 갯수도 10개를 넘지 않는다.
- 수식은 + - 연산만 존재한다.
- 숫자의 맨 앞자리는 0이 될수 없다.

Test Case
```
SEND+MORE=MONEY
FORTY+TEN+TEN=SIXTY
ADAM+AND+EVE=MOVED
FIVE+FIVE+NINE+ELEVEN=THIRTY
CROSS+ROADS=DANGER
USE+LESS=KIDDY
GREEN+ORANGE=COLORS
COFFEE+COFFEE+COFFEE=THEOREM
```

### Ver. 2
다음과 같이 알파벳으로 구성된 수식을 입력받아서, 
```
SEND+MORE=MONEY
EMAIL=SPAM+SPAM+SPAM+SPAM+SPAM+SPAM
FORTY+TEN+TEN= SIXTY
NUMBER= SQUARE+SQUARE+SQUARE+SQUARE
SEVENTY= FIVE+SEVEN+ELEVEN+TWELVE+FIFTEEN+TWENTY
MANET+MATISSE+MIRO+MONET+RENOIR=ARTISTS
SIX+SIX+SIX = NINE + NINE
ABCDE*F=GGGGGG
ADAM+AND+EVE=MOVED

FIVE+FIVE+NINE+ELEVEN=THIRTY
CROSS+ROADS=DANGER
USE+LESS=KIDDY

BILL+WILLIAM+MONICA=CLINTON
GREEN+ORANGE=COLORS
CEZANNE+MANET+MATISSE=ARTISTS
OLD+SALT+TOLD+TALL=TALES
COFFEE+COFFEE+COFFEE=THEOREM
SEND*ME=EMAIL
POWER=YOUR+SHOW
```
이 식을 만족 시키도록, 각 알파벳에 대응하는 숫자를 찾아라. (식을 만족하는 모든 조합을 찾아낼 것)

위의 경우, 231+231=462 등의 답이 나온다.

**제한 사항**
- 식에서 사용할 수 있는 문자는 사칙연산(+ = * /)과 = 기호, 그리고 알파벳 문자 A ~ Z까지(대문자)만 허용된다.
- 연산자 우선순위 규칙은 적용하지 않고 무조건 왼쪽부터 계산하는 것으로 한다.
- 식에는 공백은 포함되지 않는다.
- 한 식에서 사용될 수 있는 알파벳 문자 종류는 최대 10개까지(0 ~ 9까지를 표현해야 하므로) 이다.
- SEND 같은 경우, 앞자리 수인 S가 0이 될 수는 없다.
- 위 조건들을 만족하지 않는 식은 오류로 처리한다.
- 입력 받은 식을 만족하는 답을 못 찾은 경우는 답이 없음으로 처리한다.

## 자판기
자판기를 제어하는 프로그램 작성.

### 요구 사항
상품 정보 등록(미리 해둠):
1. 상품 버튼은 1 ~ 9번까지 있다.
   1. 각 버튼별로 상품은 미리 등록해 둔다.
   1. 상품 정보: 상품명, 가격, 갯수
   1. 버튼에 상품이 등록되지 않을 수도 있다.

구매 프로세스:
1. 자판기는 최초에 대기 상태
1. 동전 혹은 지폐를 투입한다.
   1. 투입 가능한 동전 및 지폐 종류 결정 필요
1. 돈이 투입 될 때마다:
   1. 투입한 총 합계금액 표시
   1. 합계금액으로 구매 가능한 상품이 있는 경우, 버튼 번호와 상품명, 가격을 표시
1. 반환 버튼을 누르면
   1. 투입한 금액을 전부 반환(메세지 출력으로 표현)하고 대기 상태로 돌아감
1. 상품 버튼을 누르면
   1. 구매 가능한 경우에 해당 상품을 출력하고 구매후의 거스름 돈을 출력하고 대기 상태로 돌아감
   1. 구매 불가능한 경우(금액 부족 혹은 품절)는 에러 메세지 출력
