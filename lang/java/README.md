# Java

## 설치 환경
OS X에서 JDK 설치경로(JAVA_HOME) 확인하기
```
$ /usr/libexec/java_home
```

## Data Types
### enum
단순한 형태로 사용하고자 한다면,
```java
public enum ErrorCode { SERVER_CONNECT_ERR, LOGIN_WRONG }
```

그러데, enum도 사실은 클래스이다. 따라서 다음과 같이 생성자는 물론이고, 매서드도 포함 할 수 있다.
```java
public enum ErrorCode {
        SERVER_CONNECT_ERR(400), LOGIN_WRONG(100);

        private int value;
        private ErrorCode(int value) { this.value = value; }

        public int toInt() { return value; }
}
```

### Generics
#### Generic Methods

```java
static <T> void fromArrayToCollection(T[] a, Collection<T> c) {
    for (T o : a) {
        c.add(o); // Correct
    }
}
```

#### 제너릭 클래스 정의

```java
public class Hoge<T> {
    private T t;
}
```

##### 제한 설정

```java
public class A {}
public class B extends A {}
public class C extends B {}

public class Foo1<T extends B> {} // BやCやその派生

// class&interface[&interface[...] ]
public class Bar<T extends B & java.io.Serializable> {}
```

#### 와일드카드
変数などの型の宣言時にはワイルドカードを使うことができる。
```java
Hoge<?> hoge = new Hoge<String>();
Hoge<? extends A> a = new Hoge<B>();
```
このワイルドカード?にはsuperとextendsで境界を設定することができる。&による複数のinterfaceの継承は表現できない。

## IO
### HTTP
#### GET

```java
    String request_get(String url, List<NameValuePair> params) {
        HttpClient httpclient = new DefaultHttpClient();
        String content;

        try {
            if (params != null && params.size() > 0) {
                boolean first = true;
                String param_str = "?";

                for (NameValuePair nv : params) {
                    param_str += (first ? "" : "&") + nv.getName() + "=" + nv.getValue();
                    first = false;
                }

                URLEncoder.encode(param_str, "UTF-8");
            }

            HttpGet httpget = new HttpGet(url);
            HttpResponse response = httpclient.execute(httpget);

            content = EntityUtils.toString(response.getEntity());
        }
        catch (IOException e) {
            content = null;
        }

        return content;
    }
```

#### POST

```java
    String request_post(String url, List<NameValuePair> params) {
        Response result = new Response();
        String content;

        HttpClient httpclient = new DefaultHttpClient();
        HttpPost httppost = new HttpPost(url);

        try {
            if (params != null && params.size() > 0) {
                httppost.setEntity(new UrlEncodedFormEntity(params, HTTP.UTF_8));
            }

            // Execute HTTP Post Request
            HttpResponse response = httpclient.execute(httppost);

            content = EntityUtils.toString(response.getEntity());
        }
        catch (ClientProtocolException e) {
            content = null;
        }
        catch (IOException e) {
            content = null;
        }

        return content;
    }
```

## Lamda (Java 1.8)

참고 링크
- http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html
- http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/Lambda-QuickStart/index.html

### Lamda 기본 문법

```java
(Argument List) -> body

(int x, int y) -> x + y
() -> 42
(String s) -> { System.out.println(s); } // return이 없는 경우.

//인자의 타입을 생략할 수도 있다.
(x, y) -> x + y
s -> { System.out.println(s); }
```

## Stream API (Java 1.8)
https://qiita.com/takumi-n/items/369dd3fcb9ccb8fcfa44

### 基本的な流れ
- コレクションからstreamを取得
- streamに対して満足するまで「中間操作」を実行。コレクションの中身を都合よく変換
- 「終端操作」で変換したコレクションの中身に対して処理を適用する

## Tips

### 도메인 이름에 해당하는 IP 주소 얻기
http://stackoverflow.com/questions/2462398/how-to-get-the-ip-address-from-the-domain-name-in-java

```java
InetAddress.getByName("xxxx.yyy.com").getHostAddress();
```

### 동적으로 클래스를 지정해서 인스턴스를 생성

```java
    // for Android
    public void selectFragment (MainMenu mainMenu){
        int position = mainMenus.indexOf(mainMenu);
        onNavigationDrawerItemSelected(position);
        setTitle(mainMenus.get(position).getName());

        Fragment fragment = null;
        try {
            Class<?> fragmentClass = Class.forName(mainMenus.get(position).getClassName());
            fragment = (Fragment) fragmentClass.newInstance();
            replaceFragment(fragment);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (java.lang.InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
```

### Bytes To Hex String

```java
    /**
     * bytesToHex method
     * Found on the internet
     * http://stackoverflow.com/a/9855338
     */
    static final char[] hexArray = "0123456789ABCDEF".toCharArray();
    public static String bytesToHex(byte[] bytes) {
        char[] hexChars = new char[bytes.length * 2];
        for ( int j = 0; j < bytes.length; j++ ) {
            int v = bytes[j] & 0xFF;
            hexChars[j * 2] = hexArray[v >>> 4];
            hexChars[j * 2 + 1] = hexArray[v & 0x0F];
        }
        return new String(hexChars);
    }
```

### 파일이름 검사

```java
    public final static boolean isValidFileName(String filename_without_path) {
        final String[] reservedChars = {"|", "\\", "?", "*", "<", "\"", ":", ">"};

        if (filename_without_path == null || filename_without_path.trim().isEmpty()) return false;

        for (String c: reservedChars){
            if (filename_without_path.indexOf(c) >= 0) return false;
        }

        return true;
    }
```

## String Formatting
https://docs.oracle.com/javase/jp/6/api/java/util/Formatter.html

## 문제 및 해결

### if문 최적화에 의해 의도치 않은 결과가

```java
@Override
public void onClick(View v) {
    if (v == mBuyButton) {
        if (mManagedType != Managed.SUBSCRIPTION &&
                !service.requestPurchase(mItem, Consts.ITEM_TYPE_INAPP, null)) {
            showDialog(DIALOG_BILLING_NOT_SUPPORTED_ID);
        }
        else if (!service.requestPurchase(mItem, Consts.ITEM_TYPE_SUBSCRIPTION, null)) {
            showDialog(DIALOG_SUBSCRIPTIONS_NOT_SUPPORTED_ID);
        }
    }
}
```

#### 증상
당시에 mManagedType != Managed.SUBSCRIPTION 조건이 true인 상태로 왔기 때문에 else if쪽의 requestPurchase()가 호출되면 안된 상황이었으나, 실제로는 if와 else if쪽의 requestPurchase()가 모두 호출된 현상.

#### 원인
아마도 최적화하는 과정에서 if ~ else if ~ 형식의 문장이 if ~ else ~ 형식으로 고쳐지고, requestPurchase()가 조건 비교 과정에서 두번 호출이 되는 듯.
```java
if (V != C1 && !F(x, 1)) doThen();
else if (!F(x, 2)) doElse();

// 위 코드가...
// 이렇게 해석되면...
if ( V != C1 && !F(x, 1) && F(x, 2) )
	doThen();
else
	doElse();
```

또 다른 원인으로, 첫 if문의  !requestPurchase()한 결과가 false가 될 경우, else if쪽의 !requestPurchase()를 평가해야 하므로 둘 다 실행될 수 있다. (물론 당시에는 !requestPurchase() 결과가 제대로 true로 떨어졌다.)

#### 해결
- 반환값이 boolean 형이더라도 사이드 이펙트가 있는 메서드를 if문 조건절에서 호출하지 말것.
- else if를 쓰지 말고 중첩된 if를 사용.
```java
if (V == C1) {
	if (!F(x, 2)) doThen();
}
else {
	if (!F(x, 1)) doElse();
}
```
