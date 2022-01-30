알아두면 좋을 수도 있는 코딩의 잡다한 기술들

## if/switch문 제거
if(switch 포함)문으로 표현되는 분기는 코드 해석을 복잡하게 만드는 주범. if를 코드에서 제거 하는 것만으로도 해석을 쉽게 만들기도 한다. 

이때 디자인 패턴에서 자주 등장하는 방법으로, 객체 상속과 오버라이딩으로 if 표현을 대신하는 방법이 있다. 이 경우, 객체 자체가 if문에서 체크해야될 조건 상태를 대변한다. 즉, 객체를 생성한 단계에서 이미 특정 분기에 필요한 조건을 만족한 것이 된다. 

이후는 일관되게 인터페이스 매서드를 호출하기만 하면 된다. 보통 switch문으로 표현되는 형태를 다형성을 이용한 방식으로 바꿀 수 있다.

## 데이터 타입은 표시형식이 아닌 활용을 생각해서 결정하라
초심자의 경우, 표시되는 형식에 맞춰서 데이터 타입을 결정하는 경향이 있다.

예를들어 다음과 날짜를 표시하는 항목이 있다고 하자.

> 20XX-01-01

그러면 클래스를 이런식으로 정의하게 될 것이다.

```java
class Data {
    String date = "";
}
```
이런 경우, data 맴버에 표시되는 형식에 맞춰서 값을 집어넣어야 한다.

하지만, 표시 형식은 언제든 바뀔 수 있다. 당장은 아니더라도. 그리고 연/월/일을 따로 분리해서 값을 가져와야 한다거나, 날짜 계산등이 필요하게 될 지도 모른다.

따라서, 값을 저장할 때는 **가능한 많은 형태로 활용 가능한 형식** 으로 저장해야 한다.
```java
class Data {
    Date date = new Date();
}
```
다행히 대부분의 언어는 날짜/시간 처리를 위한 데이터 타입을 제공한다. 게다가 대부분이 날짜/시간을 표시하기 위한 formatting 매서드도 제공한다.


또, 성별을 저장할 때도
```java
class User {
    String sex = "男";
}
```
이런식으로 저장하면 안되고...

```java
class User {
    int sex = 0; // 0: 男, 1: 女
}

// 혹은 enum을 활용
enum Sex { Male, Female }
class User {
    Sex sex = Sex.Male; 
}
```