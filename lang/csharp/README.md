# C#

기본적으로 C++과 비슷하므로, 여기서는 C#에서의 차이점만 설명함.

## 기본 문법
[C# 문법](csharp_syntax.md) 참조.

## 삽질

### 부등호 괄호 주의

```csharp
public class DirFlags {
	public bool up;
	public bool down;
	public bool left;
	public bool right;
	
	public DirFlags(bool u=false, bool d=false, bool l=false, bool r=false) {
		up = u;
		down = d;
		left = l;
		right = r;
	}
}

// p2 ->  p1
public DirFlags GetMoveDir() {
	Vector3 p1;
	Vector3 p2;
	GetLastVectors(out p1, out p2);
	
	return new DirFlags(p1.y > p2.y, p1.y < p2.y, p1.x < p2.x, p1.x > p2.x );
}
```
이 코드는 컴파일 오류가 난다.

다음 부분을 제너릭 문법으로 인식하기 때문이다.
```csharp
p1.x < p2.x, p1.x > p2.x
```

이 오류를 피하려면, 파라매터를 각각 따로따로 변수에 담거나, 괄호를 묶어서 컴파일러에게 문법의 우선순위를 지정해 줄 수 밖에 없다.

### Out/Ref Parameter 제약
IEnumerator같은 iterator 속성을 가진 매소드에서는 out/ref 파라매터를 사용할 수 없다.

## Tips
### enum 타입의 값/이름 탐색
System.Enum 클래스의 static 매서드를 활용하면, enum으로 정의된 타입이 가지고 있는 값 혹은 이름들의 리스트를 가져올 수 있다. 물론 그 리스트를 통해서 foreach를 사용하는 것도 가능.
```csharp
public enum Suits
{
    Spades,
    Hearts,
    Clubs,
    Diamonds,
    NumSuits
}

public void PrintAllSuits()
{
    foreach (var suit in System.Enum.GetValues(typeof(Suits)))
    {
        System.Console.WriteLine(suit.ToString());
    }
}
```

## 날짜/시간

### Unix TimeStamp와 DateTime 사이의 형 변환

```csharp
private static readonly DateTime UnixEpoch =
    new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc);

public static long GetCurrentUnixTimestampMillis()
{
    return (long) (DateTime.UtcNow - UnixEpoch).TotalMilliseconds;
}

public static DateTime DateTimeFromUnixTimestampMillis(long millis)
{
    return UnixEpoch.AddMilliseconds(millis);
}

public static long GetCurrentUnixTimestampSeconds()
{
    return (long) (DateTime.UtcNow - UnixEpoch).TotalSeconds;
}

public static DateTime DateTimeFromUnixTimestampSeconds(long seconds)
{
    return UnixEpoch.AddSeconds(seconds);
}
```

### DateTime(UTC)를 지역 시간대로 변환

```csharp
DateTime local = (DateTime_Object).ToLocalTime();
```

## IO

### 파일/디렉토리 탐색

```csharp
DirectoryInfo root = new DirectoryInfo(path);
FileInfo[] files = root.GetFiles("*.xxx");

foreach (FileInfo f in files) {
	StreamReader input = f.OpenText();
	// . . .
}
```

### 파일/디렉토리 관리
#### 존재 여부 확인

```csharp
bool is_file_exist = System.IO.File.Exists(file_path);
bool is_dir_exist = System.IO.Directory.Exists(path);
```

#### 디렉토리 생성

```csharp
Directory.CreateDirectory("path");
```
Path상의 모든 디렉토리를 생성한다.

#### 디렉토리 삭제

```csharp
System.IO.Directory.Delete(path); 
System.IO.Directory.Delete(path, true); // 이 경우, 서브 디렉토리까지 지움.
```

### TextFile
텍스트 파일 전체를 읽어오거나, 문자열 전체를 텍스트 파일로 저장하고자 할 때는 다음과 같이 간단히 처리 가능.
```csharp
using System.IO;

// 이미 파일이 존재할 경우에는 기존 파일의 내용은 지워지고 새로운 내용으로 덮어쓰게 된다.
File.WriteAllText(path, createText);

File.ReadAllText(path);
```

#### 텍스트 파일 생성/쓰기
```csharp
using System.IO;

StreamWriter sw = File.CreateText(path);
sw.WriteLine("test");
sw.Close();
```
File.CreateText()는 append 모드가 false로 설정된 StreamWrite를 생성한다. 따라서, 이미 파일이 존재하는 경우에는 덮어 쓰기를 하게 된다. Encoding은 UTF-8로 설정된다.

#### 텍스트 파일 열기/읽기

```csharp
using System.IO;

StreamReader sr = File.OpenText(path);
string s = sr.ReadLine();
```

### Path

```csharp
using System.IO;

string fileName = @"C:\mydir\myfile.ext";
string path = @"C:\mydir\";
string result;

result = Path.GetFileNameWithoutExtension(fileName);
Console.WriteLine("GetFileNameWithoutExtension('{0}') returns '{1}'", fileName, result);

result = Path.GetFileName(path);
Console.WriteLine("GetFileName('{0}') returns '{1}'", path, result);

// This code produces output similar to the following: 
// 
// GetFileNameWithoutExtension('C:\mydir\myfile.ext') returns 'myfile' 
// GetFileName('C:\mydir\') returns ''
```

### XML
#### XmlTextReader

```csharp
using System.Xml;

XmlTextReader reader = new XmlTextReader("books.xml");

while (reader.Read()) {
	switch (reader.NodeType) {
	case XmlNodeType.Element: // The node is an element.
		Console.Write("<" + reader.Name);
		Console.WriteLine(">");
		break;
	case XmlNodeType.Text: //Display the text in each element.
		Console.WriteLine (reader.Value);
		break;
	case XmlNodeType. EndElement: //Display the end of the element.
		Console.Write("</" + reader.Name);
		Console.WriteLine(">");
		break;
	}

	// Read attributes.
	if (reader.HasAttributes) {
		Console.WriteLine("Attributes of <" + reader.Name + ">");
		while (reader.MoveToNextAttribute()) {
			Console.WriteLine(" {0}={1}", reader.Name, reader.Value);
		}
		// Move the reader back to the element node.
		reader.MoveToElement();
	}
}

reader.Close();
```

#### DOM Parser

```csharp
using System.Xml;

XmlTextReader reader = new XmlTextReader("patch_list.xml");

XmlDocument xml = new XmlDocument ();
xml.Load(reader);
XmlElement xmlRoot = xml.DocumentElement;

// Root
foreach (XmlNode node in xmlRoot.ChildNodes) {
	string nodeName = node.Name;
	XmlAttribute attr = node.Attributes["Attribute Name"].Value; // 속성값 가져오기.

	string value = node.value; // <node>value</node> 형태인 경우.
	string innerText = node.InnerText; // value 부분이 여러행으로 되어 있는 경우.
}

reader.Close();
```

#### Using LINQ-to-XML
Using LINQ-to-XML, you can do var doc = XDocument.Load("yourfilepath"). From there its just a matter of querying the data you want, say like this:
```csharp
var authors = doc.Root.Elements().Select( x => x.Element("Author") );
```

```csharp
using System;
using System.Xml.Linq;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main( string[] args )
        {
            XDocument doc = XDocument.Load( "XMLFile1.xml" );

            var authors = doc.Descendants( "Author" );

            foreach ( var author in authors )
            {
                Console.WriteLine( author.Value );
            }
            Console.ReadLine();
        }
    }
}
```

## Network

http://stackoverflow.com/questions/4015324/http-request-with-post

```csharp
using System.Net;
using System.Collections.Specialized;

// POST
using (var client = new WebClient())
{
    var values = new NameValueCollection();
    values["thing1"] = "hello";
    values["thing2"] = "world";

    var response = client.UploadValues("http://www.example.com/recepticle.aspx", values);

    var responseString = Encoding.Default.GetString(response);
}

// GET
using (var client = new WebClient())
{
    var responseString = client.DownloadString("http://www.example.com/recepticle.aspx");
}
```

## 보안

### AES

```csharp
using System.Security.Cryptography;
using System.Text;
using System;

public class CryptoLib {

	// 128bit(16byte)のIV（初期ベクタ）とKey（暗号キー）...
	private const string iv = @"1234567890ABCDEF";
	private const string key = @"1234567890ABCDEF";

	/// <summary>
	/// 文字列をAESで暗号化.
	/// </summary>
	public static string Encrypt(string text)
	{
		// AES暗号化サービスプロバイダ.
		AesCryptoServiceProvider aes = new AesCryptoServiceProvider();
		aes.BlockSize = 128;
		aes.KeySize = 128;


		aes.IV = Encoding.UTF8.GetBytes(iv);
		aes.Key = Encoding.UTF8.GetBytes(key);
		aes.Mode = CipherMode.CBC;
		aes.Padding = PaddingMode.PKCS7;
		
		// 文字列をバイト型配列に変換.
		byte[] src = Encoding.Unicode.GetBytes(text);
		
		// 暗号化する.
		using (ICryptoTransform encrypt = aes.CreateEncryptor())
		{
			byte[] dest = encrypt.TransformFinalBlock(src, 0, src.Length);
			
			// バイト型配列からBase64形式の文字列に変換.
			return Convert.ToBase64String(dest);
		}
	}
	
	/// <summary>
	/// 文字列をAESで復号化.
	/// </summary>
	public static string Decrypt(string text)
	{
		// AES暗号化サービスプロバイダ.
		AesCryptoServiceProvider aes = new AesCryptoServiceProvider();
		aes.BlockSize = 128;
		aes.KeySize = 128;
		aes.IV = Encoding.UTF8.GetBytes(iv);
		aes.Key = Encoding.UTF8.GetBytes(key);
		aes.Mode = CipherMode.CBC;
		aes.Padding = PaddingMode.PKCS7;
		
		// Base64形式の文字列からバイト型配列に変換.
		byte[] src = System.Convert.FromBase64String(text);
		
		// 複号化する.
		using (ICryptoTransform decrypt = aes.CreateDecryptor())
		{
			byte[] dest = decrypt.TransformFinalBlock(src, 0, src.Length);
			return Encoding.Unicode.GetString(dest);
		}
	}
}
```

## System / OS

### Shell 명령 실행

```csharp
System.Diagnostics.Process.Start("process-name", "parameters");

// 프로세스가 종료될 때까지 대기
System.Diagnostics.Process.Start("process-name", "parameters").WaitForExit();
```

## LINQ

참고
- http://yohshiy.blog.fc2.com/blog-entry-274.html
- http://ufcpp.net/study/csharp/sp3_linq.html

```IEnumerable<T>``` 인터페이스를 구현한 대상에 대해 적용 가능.

### 기본 사용법

#### Query 문법 이용

```csharp
using System.Linq;

// The Three Parts of a LINQ Query:
//  1. Data source.
int[] numbers = new int[7] { 0, 1, 2, 3, 4, 5, 6 };
		
// 2. Query creation.
// numQuery is an IEnumerable<int>
var numQuery = from num in numbers where (num % 2) == 0 select num;
		
// 3. Query execution.
foreach (int num in numQuery) Console.Write("{0,1} ", num);
```

#### Linq 함수 이용 

```csharp
using System.Linq;

int[] numbers = new int[7] { 0, 1, 2, 3, 4, 5, 6 };
// numQuery is an IEnumerable<int>
var numQuery = numbers.Where(num => num % 2 == 0);
```
