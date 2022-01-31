# 정규표현식
正規表現（せいきひょうげん、英: regular expression）

## 언어별 정규표현식 사용법

### Java

```java
public static void main(String args[]) {
    //判定する文字列
    String str = "123A5";

    //判定するパターンを生成
    Pattern p = Pattern.compile("^[0-9]*$");
    Matcher m = p.matcher(str);

    //画面表示
    System.out.println(m.find());
}
```

### C Sharp

```csharp
class TestRegularExpressionValidation
{
    static void Main()
    {
        string[] numbers = 
        {
            "123-555-0190", 
            "444-234-22450", 
            "690-555-0178", 
            "146-893-232",
            "146-555-0122",
            "4007-555-0111", 
            "407-555-0111", 
            "407-2-5555", 
        };

        string sPattern = "^\\d{3}-\\d{3}-\\d{4}$";

        foreach (string s in numbers)
        {
            System.Console.Write("{0,14}", s);

            if (System.Text.RegularExpressions.Regex.IsMatch(s, sPattern))
            {
                System.Console.WriteLine(" - valid");
            }
            else
            {
                System.Console.WriteLine(" - invalid");
            }
        }

        // Keep the console window open in debug mode.
        System.Console.WriteLine("Press any key to exit.");
        System.Console.ReadKey();
    }
}
/* Output:
      123-555-0190 - valid
     444-234-22450 - invalid
      690-555-0178 - valid
       146-893-232 - invalid
      146-555-0122 - valid
     4007-555-0111 - invalid
      407-555-0111 - valid
        407-2-5555 - invalid
*/
```

## Samples

Java에서 사용하는 정규 표현식 문법 기준

### E-mail 형식 검사

```
^(\\w+\\.)*\\w+@(\\w+\\.)+[A-Za-z]+$
```

### Password
パスワードは以下をすべて使用して、8から48文字で入力する。

- 小文字の半角アルファベット
- 大文字の半角アルファベット
- 半角数字
- 記号（使用可能記号　```!"#$%&'()*+,-./:;<=>?@[]^_`{|}~```）

```
^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])(?=.*[!-/:-@[-`{-~])[!-~]{8,48}$
```

### 주민등록번호 검사

```java
// Java
public static boolean juminCheck(final String serial1, final String serial2)  
{
    // 정규식을 이용해서 체크섬 이외의 대략적인 검사를 할 수 있다.
    //
    // 1. 년월일 날짜 부분 검사:
    //   1 ~ 12월까지만 유효.
    //   1 ~ 31일까지만 유효. (단 2월 30일 등의 표현은 검사 못함)
    //
    // 2. 성별 코드는 1 ~ 4만 유효.
    //
    // 3. 기본적인 자릿수 검사
    //
    final String src = serial1 + '-' + serial2;
    final String regex = "\\d{2}(0[1-9]|1[0-2])(0[1-9]|[12][0-9]|3[01])-[1-4]\\d{6}";
    if (!Pattern.compile(regex).matcher(src).matches())
        return false;
 
    // 체크섬 검사
    int[] a = new int[6];
    for (int i = 0; i < 6; ++i) {
        a[i] = serial1.charAt(i) - '0';
    }
 
    int[] b = new int[7];
    for (int i = 0; i < 7; ++i) {
        b[i] = serial2.charAt(i) - '0';
    }
 
    int check_digit = 
        a[0]*2 + a[1]*3 + a[2]*4 + a[3]*5 + a[4]*6 + a[5]*7 +
        b[0]*8 + b[1]*9 + b[2]*2 + b[3]*3 + b[4]*4 + b[5]*5 ;
 
    check_digit %= 11;
    check_digit = (11 - check_digit) % 10;
 
    return check_digit == b[6];
}
```
