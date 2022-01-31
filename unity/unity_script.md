# Unity Script

## Editor Script
[Unity Editor Script](unity_editor_script.md) 참조.

## Debug

### Assertions
C#의 Assert() 매소드가 유니티에서는 제대로 동작을 안하는 듯. 그래서 직접 Assert() 라이브러리를 구현.
```csharp
using UnityEngine;
using System;

namespace DebugLib {

  public class Assert {

    public delegate void TestDelegate();

    public static void True(bool cond) {
#if DEBUG || UNITY_EDITOR
      if (!cond) throw new System.Exception("Assert Failed.");
#endif
    }

    public static void False(bool cond) {
#if DEBUG || UNITY_EDITOR
      True(!cond);
#endif
    }

    public static void Catch<E>(TestDelegate code) where E : Exception { 
#if DEBUG || UNITY_EDITOR
      try { code(); } catch (E) { /* ok */  }
#endif
    }

  }

}
```

## MonoBehaviour
### Messages

| message | description |
| ------- | ----------- |
|Awake | Awake is called when the script instance is being loaded.|
|FixedUpdate | This function is called every fixed framerate frame, if the MonoBehaviour is enabled.|
|LateUpdate | LateUpdate is called every frame, if the Behaviour is enabled.|
|OnAnimatorIK | Callback for setting up animation IK (inverse kinematics).|
|OnAnimatorMove | Callback for processing animation movements for modifying root motion.|
|OnApplicationFocus | Sent to all game objects when the player gets or loses focus.|
|OnApplicationPause(bool pauseStatus) | Sent to all game objects when the player pauses.|
|OnApplicationQuit | Sent to all game objects before the application is quit.|
|OnAudioFilterRead | If OnAudioFilterRead is implemented, Unity will insert a custom filter into the audio DSP chain.|
|OnBecameInvisible | OnBecameInvisible is called when the renderer is no longer visible by any camera.|
|OnBecameVisible | OnBecameVisible is called when the renderer became visible by any camera.|
|OnCollisionEnter | OnCollisionEnter is called when this collider/rigidbody has begun touching another rigidbody/collider.|
|OnCollisionEnter2D(Collision2D) | Sent when an incoming collider makes contact with this object's collider (2D physics only).|
|OnCollisionExit | OnCollisionExit is called when this collider/rigidbody has stopped touching another rigidbody/collider.|
|OnCollisionExit2D | Sent when a collider on another object stops touching this object's collider (2D physics only).|
|OnCollisionStay | OnCollisionStay is called once per frame for every collider/rigidbody that is touching rigidbody/collider.|
|OnCollisionStay2D | Sent each frame where a collider on another object is touching this object's collider (2D physics only).|
|OnConnectedToServer | Called on the client when you have successfully connected to a server.|
|OnControllerColliderHit | OnControllerColliderHit is called when the controller hits a collider while performing a Move.|
|OnDestroy | This function is called when the MonoBehaviour will be destroyed.|
|OnDisable | This function is called when the behaviour becomes disabled () or inactive.|
|OnDisconnectedFromServer | Called on the client when the connection was lost or you disconnected from the server.|
|OnDrawGizmos | Implement OnDrawGizmos if you want to draw gizmos that are also pickable and always drawn.|
|OnDrawGizmosSelected | Implement this OnDrawGizmosSelected if you want to draw gizmos only if the object is selected.|
|OnEnable | This function is called when the object becomes enabled and active.|
|OnFailedToConnect | Called on the client when a connection attempt fails for some reason.|
|OnFailedToConnectToMasterServer | Called on clients or servers when there is a problem connecting to the MasterServer.|
|OnGUI | OnGUI is called for rendering and handling GUI events.|
|OnJointBreak | Called when a joint attached to the same game object broke.|
|OnLevelWasLoaded | This function is called after a new level was loaded.|
|OnMasterServerEvent | Called on clients or servers when reporting events from the MasterServer.|
|OnMouseDown | OnMouseDown is called when the user has pressed the mouse button while over the GUIElement or Collider.|
|OnMouseDrag | OnMouseDrag is called when the user has clicked on a GUIElement or Collider and is still holding down the mouse.|
|OnMouseEnter | Called when the mouse enters the GUIElement or Collider.|
|OnMouseExit | Called when the mouse is not any longer over the GUIElement or Collider.|
|OnMouseOver | Called every frame while the mouse is over the GUIElement or Collider.|
|OnMouseUp | OnMouseUp is called when the user has released the mouse button.|
|OnMouseUpAsButton | OnMouseUpAsButton is only called when the mouse is released over the same GUIElement or Collider as it was pressed.|
|OnNetworkInstantiate | Called on objects which have been network instantiated with Network.Instantiate.|
|OnParticleCollision | OnParticleCollision is called when a particle hits a collider.|
|OnPlayerConnected | Called on the server whenever a new player has successfully connected.|
|OnPlayerDisconnected | Called on the server whenever a player disconnected from the server.|
|OnPostRender | OnPostRender is called after a camera finished rendering the scene.|
|OnPreCull | OnPreCull is called before a camera culls the scene.|
|OnPreRender | OnPreRender is called before a camera starts rendering the scene.|
|OnRenderImage | OnRenderImage is called after all rendering is complete to render image.|
|OnRenderObject | OnRenderObject is called after camera has rendered the scene.|
|OnSerializeNetworkView | Used to customize synchronization of variables in a script watched by a network view.|
|OnServerInitialized | Called on the server whenever a Network.InitializeServer was invoked and has completed.|
|OnTransformChildrenChanged | This function is called when the list of children of the transform of the GameObject has changed.|
|OnTransformParentChanged | This function is called when the parent property of the transform of the GameObject has changed.|
|OnTriggerEnter | OnTriggerEnter is called when the Collider other enters the trigger.|
|OnTriggerEnter2D(Collider2D) | Sent when another object enters a trigger collider attached to this object (2D physics only).|
|OnTriggerExit | OnTriggerExit is called when the Collider other has stopped touching the trigger.|
|OnTriggerExit2D(Collider2D) | Sent when another object leaves a trigger collider attached to this object (2D physics only).|
|OnTriggerStay | OnTriggerStay is called once per frame for every Collider other that is touching the trigger.|
|OnTriggerStay2D | Sent each frame where another object is within a trigger collider attached to this object (2D physics only).|
|OnValidate | This function is called when the script is loaded or a value is changed in the inspector (Called in the editor only).|
|OnWillRenderObject | OnWillRenderObject is called once for each camera if the object is visible.|
|Reset | Reset to default values.|
|Start | Start is called on the frame when a script is enabled just before any of the Update methods is called the first time.|
|Update | Update is called every frame, if the MonoBehaviour is enabled.|

### Lifecycle
![Mono Behaviour 플로우 차트](Monobehaviour_flowchart.svg)


### Require Component 지정

```csharp
using UnityEngine;

// The GameObject requires a Rigidbody component
[RequireComponent (typeof (Rigidbody))]
public class PlayerScript : MonoBehaviour {
  Rigidbody rb;
  
  void Start() {
    rb = GetComponent<Rigidbody>();
  }
  void FixedUpdate()  {
    rb.AddForce(Vector3.up);
  }
}
```

## Application

### Application Event

```csharp
void OnApplicationPause() {
// 다른 어플로 이동하거나, 모바일에서 Home 화면으로 이동되었을 때
}

void OnApplicationFocus() {
// Pause상태에서 돌아올 때(Resume)
}

void  OnApplicationQuit() {
// 종료될 때
}
```

## Scene
씬 전환
```csharp
Application.LoadLevel("SceneName");
```

씬 전환 이벤트
```csharp
//씬 로딩이 끝난 후에 볼려짐. level 번호로 어느 씬이 불려졌는지 확인.
void OnLevelWasLoaded(int level) {
  // TODO ...
}
```

### Loading 진행상태 확인 
비동기 방식으로 씬을 로딩할 때만 사용가능.
```csharp
// 씬을 로딩할 때 AsyncOperation을 받아둔다.
AsyncOperation ao = Application.LoadLevelAsync("NEXT SCENE");

void Update() {
  if (!ao.isDone) {
    SetLoadingProgress(ao.progress);
  }
}

void SetLoadingProgress(float progress) {
  // 여기서 로딩 진행상태를 업데이트.
  // progress는 0 ~ 1 사이의 값.
}
```

## Object 관리

### 생성
복제
```csharp
UnityEngine.Object obj = Instantiate(original_object);
```

컴포넌트 추가
```csharp
GameObject stagePart = new GameObject("StagePart");
AutoScroll autoScroll = stagePart.AddComponent<AutoScroll>();
```

#### 싱글턴

```csharp
public class UnityComp : MonoBehaviour
{
  private static UnityComp _instance;
  public static UnityComp Instance
  {
    get
    {
      if (!_instance)
      {
        _instance = GameObject.FindObjectOfType<UnityComp>();
        if (!_instance)
        {
          GameObject container = new GameObject();
          container.name = "UnityComp";
          _instance = container.AddComponent<UnityComp>();
          DontDestroyOnLoad(_instance); // 다른 씬이 로드될 때도 지워지지 않고 살아있음.
        }
      }
      return _instance;
    }
  }
}
```

static 맴버는 앱이 재기동될 때 초기화 되지 않고, 기존의 값을 그대로 유지하는 경우가 있음.  때문에 싱글턴을 단순히 static 변수를 이용해서 구현하는 것은 지양.

#### Prefab에서 복제
다음 소스는 Resource 디렉토리에 있는 Prefab을 바탕으로 객체를 생성한다.
```csharp
// プレハブを取得
GameObject prefab = Resources.Load<GameObject>("Prefabs/XXXObj");
// プレハブからインスタンスを生成
GameObject newObj = Instantiate(prefab) as GameObject;
```

### Object Pool

```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class GameObjectPool {

  List<GameObject> pool = new List<GameObject>();

  Transform pool_parent;

  public void SetPoolParent(Transform parent) {
    this.pool_parent = parent;
  }

  public GameObject GetFreeObject() {
    foreach (GameObject o in this.pool) {
      if (!o.activeSelf) {
        o.SetActive(true); // Set Used
        return o;
      }
    }

    return null;
  }

  public void Add(GameObject o) {
    o.SetActive(false);
    o.transform.parent = this.pool_parent;
    this.pool.Add(o);
  }

  public bool Release(GameObject o) {
    if (this.pool.Contains(o)) {
      o.SetActive(false);
      o.transform.parent = this.pool_parent;
      return true;
    }
      
    return false;
  }

  public bool SetUsed(GameObject o) {
    if (this.pool.Contains(o)) {
      o.SetActive(true);
      return true;
    }

    return false;
  }

}
```

사용법:
```csharp
GameObjectPool  pool = new GameObjectPool();

// SetPoolParent()로 Object Pool의 컨테이너 역할을 할 GameObject를 지정.
pool.SetPoolParent(gameObject.Transform);

// Add() 매소드로 pool에 미리 만들어진 GameObject를 추가한다. 이때 추가된 객체는 pool_parent 그룹 아래에 들어간다.
pool.Add(a_game_object);

// pool에서 객체를 받아옴.
GameObject o = pool.GetFreeObject();

// 사용이 끝났으면 반환한다.
pool.Release(o);
```

### Asset Bundle (Pro)

[Unity Asset Bundle](unity_asset_bundle.md) 참조.

### 제거

```csharp
Destroy(object);
```

### 찾기
#### GameObject 찾기

```csharp
GameObject obj = GameObject.Find("name");
```
Update 이벤트때 이걸 호출하면 부하가 크다고 함. 주의.

#### Component 찾기

```csharp
GameObject obj = GameObject.Find("name");
Type component = obj.GetComponent<Type>();
```


#### 자식 객체들 중에서 찾기 

```csharp
Transform child = transform.FindChild("object_name");
```

### Child 지정 

```csharp
childGameobject.transform.parent = parentGameObject.transform;
```

### 자식 개수 세기 

```csharp
int GetCountOfChildren(Transform parent) {
  int count = 0;
  foreach (Transform child in parent) {
    ++count;
  }
    
  return count;
}
```

### 이동, 변형

```csharp
Vector3 cur_pos = transform.position; // 개체의 위치(좌표) 가져오기.
transform.Translate(add_x, add_y, add_z); // 개체의 위치 이동. (변위값만 인자로 넘긴다)

// 개체의 좌표를 새로 지정.
Vector3 new_pos = new Vector(x, y, z);
transform.position = new_pos;
```

## 좌표 변환

### Local to World 

```csharp
// Transforms the position x, y, z from local space to world space.
TransformPoint(x: float, y: float, z: float): Vector3;
```

ex)
```csharp
// You need to assign an object to this variable in the inspector
var someObject : GameObject;
// Instantiate an object to the right of the current object
thePosition = transform.TransformPoint(2, 0, 0);
Instantiate(someObject, thePosition, someObject.transform.rotation);
```

### World to Local
지역 객체에서 transform 맴버의 InverseTransformPoint 메서드 활용.
```csharp
// Mouse 좌표를 로컬 좌표로 변환.
Vector3 mousePt = Input.mousePosition;
Vector3 worldPt = Camera.main.ScreenToWorldPoint(screenPoint);
Vector3 localPt = transform.InverseTransformPoint(worldPt);
```

### Screen to World
Camera 객체의 ScreenToWorldPoint 메서드 활용.
```csharp
var screenPoint = Input.mousePosition;
Camera.main.ScreenToWorldPoint(screenPoint);
```

### World to Screen

```csharp
screenPoint = Camera.main.WorldToScreenPoint(gameObject.transform.position);
var offset = gameObject.transform.position - Camera.main.ScreenToWorldPoint(new Vector3(Input.mousePosition.x, Input.mousePosition.y, screenPoint.z));
```

### Bounding 
중심 좌표(center)와 크기(size), 두 가지 정보로 물체의 크기 혹은 범위를 표현.

충돌영역의 Left, Right, Top, Bottom 등에 해당되는 좌표 확인.
```csharp
// Collider의 bounds.min, bounds.max는 월드 좌표임.
float left = collider.bounds.min.x;
float right = collider.bounds.max.x;
float top = collider.bounds.max.y;
float bottom = collider.bounds.min.y;
```

**주의! bounds 계산시, collider는 enabled 상태로!**<br/>
collider가 enabled 상태가 아닐 경우, bounds의 사이즈가 0이 되어버림.

## 난수

```csharp
// a 이상 b 미만의 정수형 난수를 발생시킨다.
int v = Random.Range(a, b);
```

## 코루틴(Coroutine) 

진입 지점이 여러개인 함수. yield return 구문으로 반환되고 다시 진입가능.

유니티에서 코루틴 함수는 IEnumerator을 반환한다.

**코루틴 정의**
```csharp
IEnumerator Function() {
  yield return ...;
}
```

**주의!**<br/>
Update(), OnEnable() 함수는 코루틴으로 구현될 수 없다. <br />
코루틴 안에서는 try ~ catch 구문을 사용해선 안된다.


***코루틴 함수에서 반환 가능한 타입들**
- WaitForSeconds 상속 클래스
- WaitForEndOfFrame 상속 클래스
- WaitForFixedUpdate 상속 클래스
- 코루틴 클래스
- WWW
- null : 다음 프레임까지 대기

**코루틴 호출**
```csharp
StartCoroutine("함수이름");
StartCoroutine( IEnumerator );
```

**특징**
- yield return StartCoroutine()을 통해 함수 중간에 대기 가능
- yield return null을 통해 렌더링 프레임 별로 호출 가능
- 게임 오브젝트가 비활성화되면 자동 소멸

### n초 후에 실행

```csharp
IEnumerator SomeFunc() {
  yield return new WaitForSeconds(2); // 2 sec
  // TO DO ...
}

// 함수 호출할 때,
StartCoroutine( SomeFunc() );
```

### 코루틴을 활용한 FSM 구현

```csharp
public enum PlayerState {
  Idle,
  Run,
  Attack
}

public PlayerState ps;

void OnEnable() {
  StartCoroutine(FSMMain());
}

IEnumerator FSMMain() {
  while (true) {
    yield return StartCoroutine(ps.ToString());
  }
}

IEnumerator Idle() {
  // 상태 진입 시점.

  do {
    yield return null; // 처음 시작할 때 한 프레임은 쉰다.

    // 상태 동작.
  }
  while (ps == PlayerState.Idle); // 상태가 바뀌면 종료

  // 상태 종료 시점.
}
```

## 환경설정

### 해상도

화면 크기 얻기
```csharp
Screen.width
Screen.height
```

화면 비율 맞추기
```csharp
// public static void SetResolution(int width, int height, bool fullscreen, int preferredRefreshRate = 0);
//
// ex 2:3
Screen.SetResolution(Screen.width, (int)(Screen.width/2.0*3.0), true);
```

### FPS

```javascript
function Awake () {
  Application.targetFrameRate = 60;
}
```

### 시스템 정보
#### 디바이스 모델 이름

```csharp
SystemInfo.deviceModel
```

## Unity3D 게임개발

### 3D 게임 관련
[Unity 게임개발](unity_game_dev.md) 참조.

### 2D 게임 관련
[Unity 2D게임](unity_2dgame_dev.md) 참조. (v4.3 이상)

## UI 개발
### 새 UI System (v4.6)

[Unity uGUI](unity_ugui.md) 참조.

###  Button
스킨, 폰트 지정
```csharp
void OnGUI() {
    // Create style for a button
    GUIStyle myButtonStyle = new GUIStyle(GUI.skin.button);
    myButtonStyle.fontSize = 50;
 
    // Load and set Font
    Font myFont = (Font)Resources.Load("Fonts/comic", typeof(Font));
    myButtonStyle.font = myFont;
 
    // Set color for selected and unselected buttons
    myButtonStyle.normal.textColor = Color.red;
    myButtonStyle.hover.textColor = Color.red;
 
    // ...
 
    // use style in button
    bool testButtonTwo = GUI.Button(new Rect(10,10,50,50), "test", myButtonStyle);
 
    // ...
}
```

## IO 
### PlayerPreferance 

```csharp
using UnityEngine;
using System.Collections;

public class TestPlayerPrefs : MonoBehaviour {
    void Start () {
        // 値を取得。第二引数はキーがない場合のデフォルト値
        int version = PlayerPrefs.GetInt ("VERSION", 1);
        Debug.Log(version); // 初めはキーがないのでデフォルト値の1

        // 値をセット。第一引数にキー名、第二引数にセットする値
        // 型に注意。この場合ほSetIntなので、int型しかセットできない
        PlayerPrefs.SetInt ("VERSION", 2);

        // ↑で2をセットしたので、2が取れる
        version = PlayerPrefs.GetInt ("VERSION", 1);
        Debug.Log(version); // 2

        // キーを全部消す
        PlayerPrefs.DeleteAll();
    }
}
```


#### 설정 파일이 저장되는 경로
##### Editor/Standalone 
スタンドアロンの Mac OS X で PlayerPrefs は、\~/Library/Preferences フォルダーに unity.\[company name].\[product name].plist という形で保存されます。company name および product name は Project Settings でセットアップします。 .plist ファイルは、Unity のエディター上および スタンドアロンプレイヤーでも読み込むことができます。

Windows の PlayerPrefs は、 ```HKCU\Software\[company name]\[product name]``` key, where company and product names are Project Settings で設定します。

Linux 上で PlayerPrefs は、```~/.config unity3d[CompanyName]/[ProductName]``` に保存されています。company name および product name は Project Settings で設定します。

On Windows Store Apps, Player Prefs can be found in ```%userprofile%\AppData\Local\Packages\[ProductPackageId]>\LocalState\playerprefs.dat```

##### Android
Android では、データはデバイスに保存 (持続) されます。データは ShaderPreferences に保存されます。C#/JavaScript、Android Java、ネイティブコードはすべて、PlayerPrefs データにアクセスできます。PlayerPrefs データは、/data/data/pkg-name/shared_prefs/pkg-name.xml に物理的に保管されます。

##### WebPlayer 
Web Player の PlayerPrefs はバイナリ形式のファイルで次の場所に保存されます。

Mac OS X: \~/Library/Preferences/Unity/WebPlayerPrefs

Windows: %APPDATA%\Unity\WebPlayerPrefs

WebGL: WebGL では、 PlayerPrefs はブラウザーの IndexedDB API を使って保存されます。

### File I/O
#### Data path 
데이터 파일을 읽고 쓸 경로는 플랫폼에 관계없이 다음 코드로 얻을 수 있다.
```csharp
string dataPath = Application.persistentDataPath;
```
* 참고: http://docs.unity3d.com/ScriptReference/Application-persistentDataPath.html


Application.dataPath를 각 플랫폼에 맞게 가져오는 방법은 다음을 참조.
* 참고: http://forum.unity3d.com/threads/data-storage-how-to-read-write-files.61198/

```csharp
        if(Application.platform == RuntimePlatform.IPhonePlayer) {
            //for iPhone
            //path should be <Application_Home>/Library/Caches
            //app data Path is /<Application_Home>/AppName.app/Data    
            string dataPath = Application.dataPath;
            cachePath = dataPath.Replace ("/Data", "");
            //find index of "/" in /AppName.app
            int index = cachePath.LastIndexOf ("/");
            cachePath = cachePath.Substring (0, cachePath.Length - (cachePath.Length - index));
            cachePath = cachePath + "/Library/Caches/";
        }
        else if(Application.platform == RuntimePlatform.Android) {
                //  Application.dataPath returns: /data/app/net.xorgames.mapp.apk/
       
                 // maybe the cache path should be this:
                // -> /data/<package_name>/files/     OR
                // /Android/data/<package_name>/cache/
           
            string dataPath = Application.dataPath;
           
            cachePath = dataPath.Replace(".apk", "/files/");
            cachePath = cachePath.Replace("app/", "");
           
        }
```

#### Text File 

```csharp
string fileName = Application.persistentDataPath + "/" + FILE_NAME;
StreamWriter fileWriter = File.CreateText(fileName);
fileWriter.WriteLine("Hello world");
fileWriter.Close();
```

#### Binary File 

```csharp
string fileName = Application.persistentDataPath + "/" + FILE_NAME;
FileStream fileWriter = File.Create(fileName);
fileWriter.WriteByte(0);
fileWriter.Close();
```

### Streaming Assets 

* 참고: http://docs-jp.unity3d.com/Documentation/Manual/StreamingAssets.html

Assets/StreamingAssets/ 경로에 위치한 것들은 유니티의 빌드시에 어셋 안에 포함되지 않고, 파일 시스템 상의 경로로 참조해서 쓰이게 된다.

각 플랫폼 별로 Streaming Assets 경로 얻기
```csharp
Application.streamingAssetsPath
```

### Text 
#### CSV Parser 
단순한 형태의 CSV 파서. 셀 하나에 여러줄의 텍스트가 들어있는 형식은 처리하지 못한다.
```csharp
using UnityEngine;
using System.Collections;
using System.IO;


public class CSVParser {
  public delegate void Event_OnReadLine(int row_idx, string line);
  public delegate void Event_OnLineEnd(int row_idx);
  public delegate void Event_OnReadColumn(int row_idx, int col_idx, string col);
  
  public int skip_lines = 1;
  public int max_cols = 0;
  
  private Event_OnReadLine onLine = null;
  private Event_OnLineEnd onLineEnd = null;
  private Event_OnReadColumn onColumn = null;

  public CSVParser(Event_OnReadLine onReadLine, Event_OnLineEnd onLineEnd, Event_OnReadColumn onReadCol) {
    this.onLine = onReadLine;
    this.onLineEnd = onLineEnd;
    this.onColumn = onReadCol;
  }
  
  public void ParseFromTextAsset(string file_path, bool unload_res = true) {
    TextAsset src = (TextAsset)Resources.Load(file_path,  typeof(TextAsset));
    ParseFromTextAsset(src, unload_res);
  }
  
  public void ParseFromTextAsset(TextAsset csv_file, bool unload_res = true) {
    Parse(csv_file.text);
    
    if (unload_res) {
      Resources.UnloadAsset(csv_file);
    }
  }
  
  public void Parse(string csv_text) {
    StringReader r = new StringReader(csv_text);
    Parse(r);
  }
  
  public void Parse(TextReader r, bool auto_close = true) {
    string line = null;
    int row_idx = 0;
    
    do {
      line = r.ReadLine();
      if (line == null) break;
      
      if (row_idx < this.skip_lines) {
        row_idx++;
        continue;
      }
      
      if (this.onLine != null) {
        this.onLine(row_idx, line);
      }
      
      string[] cols = line.Split(',');
      int col_idx = 0;

      bool flag_quote_begin = false;
      string col_str = "";
      
      foreach (string col in cols) {
        if (this.max_cols > 0 && col_idx > this.max_cols) break;

        if (flag_quote_begin) {
          if (col.EndsWith("\"")) {
            flag_quote_begin = false;
            col_str += "," + col.Substring(0, col.Length-1);
          }
          else {
            col_str += "," + col;
            continue;
          }
        }
        else if (col.StartsWith("\"")) {
          if (col.EndsWith("\"")) {
            col_str = col.Substring(1, col.Length-2);
          }
          else {
            flag_quote_begin = true;
            col_str = col.Substring(1, col.Length-1);
            continue;
          }
        }
        else {
          col_str = col;
        }

        if (this.onColumn != null) {
          this.onColumn(row_idx, col_idx, col_str);
        }
        col_idx++;
      }

      if (this.onLineEnd != null) {
        this.onLineEnd(row_idx);
      }
      row_idx++;
    } while (line != null);

    if (auto_close) {
      r.Close();
    }
  }
}
```

### Input Event 
#### Key Event 

```csharp
void Update() {
  if (Application.platform == RuntimePlatform.Android) {
    if (Input.GetKey(KeyCode.Home) || Input.GetKey(KeyCode.Escape) || Input.GetKey(KeyCode.Menu)) {
      Application.Quit();
      return;
    }
  }
}
```

#### 멀티터치 비활성

```csharp
void Awake() {
  Input.multiTouchEnabled = false;
}
```

## Network 


### Web
#### Open URL 

```csharp
Application.OpenURL("http://sizuha.iptime.org/");
```
웹 브라우져 앱으로 이동해서 URL을 연다.

url 부분에 GET 파라매터가 들어가는 경우, 파라매터 부분을 반드시 WWW.EscapeURL() 해주어야 한다. 
```csharp
var url = Defines.RANKING_URL + "?" +
  WWW.EscapeURL( string.Format("course={0}&name={1}&rank={2}&total={3}", a, b, c, d) );
Application.OpenURL(url);
```

##### macOS 

```csharp
#if UNITY_STANDALONE_OSX
System.Diagnostics.Process.Start("http://google.com/"); 
#endif
```

### WWW 

이제 가능한 WWW 보다, UnityWebRequest를 이용할 것을 권장함.

**도메인 이름에 주의**<br/>
도메인 문자열에 밑줄(\_) 문자가 포함되어 있으면, 안드로이드 환경에서는 동작하지 않는다.<br />
이제 iOS, Mac OS X에서도 도메인에 밑줄(\_) 문자를 허용하지 않는 걸로 바뀌는 듯함.<br/>
즉, 그냥 도메인에는 밑줄을 쓰지 않는게 정신건강에 좋다.

#### POST 전송 

```csharp
WWWForm wwwForm = new WWWForm();
//指定urlにデータを送信
wwwForm.AddField( "score", "10" );

WWW gettext = new WWW(url1,wwwForm);
```

#### 다운로드 진행상황 

```csharp
WWW download = WWW.LoadFromCacheOrDownload( "http://www.server.com/whatever.unity3d", 1 );
while( !download.isDone ) {
   m_CurrentValue = download.progress * 100;
   yield return null;
}

if (!string.IsNullOrEmpty(download.error)) {
   // error!
} 
else {
   // success!
}
```

**WWW 객체의 progress 값이 계속 0인 경우**<br/>
HTTP Response Header에서 Content-Length가 정의되지 않은 경우임.<br/>
WWW는 이 해더 정보를 바탕으로 progress 값을 산출한다.<br/>
서버쪽에서 Content-Length 정보가 해더에 포함되도록 고쳐야 한다.

#### Audio Clip 
WWW 객체를 통해 AudioClip 객체를 로딩할 수 있다. 로컬 파일의 경우 "file:///..." 형식의 URL로 지정.

```csharp
using (var www = new WWW("file://" + Defines.Path.LEVEL_PATH + snd_name)) {
  yield return www;

  var audio_clip = www.audioClip;
  // 혹은 다음과 같이 GetAudioClip() 매서드를 이용.
  var audio_clip = www.GetAudioClip(false, true);
}
```

**Streaming of 'mp3' on this platform is not supported**<br/>
Windows나 OS X 등, 모바일 플랫폼이 아닌 곳에서는 MP3 포맷을 지원하지 않는다. (MP3의 라이선스 문제) ogg로 바꾸거나 별도의 라이브러리를 사용.

#### 비동기 Http POST/GET 구현

```csharp
  public delegate void OnResponse(WWW www);

  public void HttpPost(string url, WWWForm param, OnResponse response = null) 
  {
    if (url != null) {
      StartCoroutine(postConnect(BASE_URL + url, param, response));
    }
  }

  public void HttpGet(string url, OnResponse response = null)
  {
    if (url != null) {
      StartCoroutine(connect(BASE_URL + url, response));
    }
  }

  IEnumerator postConnect(string url, WWWForm param, OnResponse response = null)
  {
    WWW www = new WWW(url, param);
    yield return www;

    if (www.error != null) {
#if UNITY_EDITOR
      Debug.Log("URL: " + url);
#endif
      Debug.Log("WWW Error: "+ www.error);
    } 
    else {
#if UNITY_EDITOR
      Debug.Log("WWW Ok!: " + www.text);
#endif
    }
    
    if (response != null) response(www);
    www.Dispose();
  }

  IEnumerator connect(string url, OnResponse response = null)
  {
    WWW www = new WWW(url);
    yield return www;
    
    if (www.error != null) {
      Debug.Log("WWW Error: "+ www.error);
    } 
    else {
      //Debug.Log("WWW Ok!: " + www.text);
    }
    
    if (response != null) response(www);
    www.Dispose();
  }
```

### Escape String

http://stackoverflow.com/questions/575440/url-encoding-using-c-sharp

```csharp
WWW.EscapeURL(" "); // result: "+"
System.Uri.EscapeUriString'(" "); // result: "%20"
```

## AudioClip
### wav로 저장 

```csharp
//  Copyright (c) 2012 Calvin Rien
//        http://the.darktable.com
//
//  This software is provided 'as-is', without any express or implied warranty. In
//  no event will the authors be held liable for any damages arising from the use
//  of this software.
//
//  Permission is granted to anyone to use this software for any purpose,
//  including commercial applications, and to alter it and redistribute it freely,
//  subject to the following restrictions:
//
//  1. The origin of this software must not be misrepresented; you must not claim
//  that you wrote the original software. If you use this software in a product,
//  an acknowledgment in the product documentation would be appreciated but is not
//  required.
//
//  2. Altered source versions must be plainly marked as such, and must not be
//  misrepresented as being the original software.
//
//  3. This notice may not be removed or altered from any source distribution.
//
//  =============================================================================
//
//  derived from Gregorio Zanon's script
//  http://forum.unity3d.com/threads/119295-Writing-AudioListener.GetOutputData-to-wav-problem?p=806734&viewfull=1#post806734

using System;
using System.IO;
using UnityEngine;
using System.Collections.Generic;

public static class SavWav {

  const int HEADER_SIZE = 44;

  public static bool Save(string filename, AudioClip clip) {
    if (!filename.ToLower().EndsWith(".wav")) {
      filename += ".wav";
    }

    //var filepath = Path.Combine(Application.persistentDataPath, filename);
    Debug.Log(filename);

    // Make sure directory exists if user is saving to sub dir.
    Directory.CreateDirectory(Path.GetDirectoryName(filename));

    using (var fileStream = CreateEmpty(filename)) {
      ConvertAndWrite(fileStream, clip);
      WriteHeader(fileStream, clip);
    }

    return true; // TODO: return false if there's a failure saving the file
  }

  public static AudioClip TrimSilence(AudioClip clip, float min) {
    var samples = new float[clip.samples];

    clip.GetData(samples, 0);

    return TrimSilence(new List<float>(samples), min, clip.channels, clip.frequency);
  }

  public static AudioClip TrimSilence(List<float> samples, float min, int channels, int hz) {
    return TrimSilence(samples, min, channels, hz, false, false);
  }

  public static AudioClip TrimSilence(List<float> samples, float min, int channels, int hz, bool _3D, bool stream) {
    int i;

    for (i=0; i<samples.Count; i++) {
      if (Mathf.Abs(samples[i]) > min) {
        break;
      }
    }

    samples.RemoveRange(0, i);

    for (i=samples.Count - 1; i>0; i--) {
      if (Mathf.Abs(samples[i]) > min) {
        break;
      }
    }

    samples.RemoveRange(i, samples.Count - i);

    var clip = AudioClip.Create("TempClip", samples.Count, channels, hz, _3D, stream);

    clip.SetData(samples.ToArray(), 0);

    return clip;
  }

  static FileStream CreateEmpty(string filepath) {
    var fileStream = new FileStream(filepath, FileMode.Create);
    byte emptyByte = new byte();

    for(int i = 0; i < HEADER_SIZE; i++) //preparing the header
    {
      fileStream.WriteByte(emptyByte);
    }

    return fileStream;
  }

  static void ConvertAndWrite(FileStream fileStream, AudioClip clip) {

    var samples = new float[clip.samples];

    clip.GetData(samples, 0);

    Int16[] intData = new Int16[samples.Length];
    //converting in 2 float[] steps to Int16[], //then Int16[] to Byte[]

    Byte[] bytesData = new Byte[samples.Length * 2];
    //bytesData array is twice the size of
    //dataSource array because a float converted in Int16 is 2 bytes.

    int rescaleFactor = 32767; //to convert float to Int16

    for (int i = 0; i<samples.Length; i++) {
      intData[i] = (short) (samples[i] * rescaleFactor);
      Byte[] byteArr = new Byte[2];
      byteArr = BitConverter.GetBytes(intData[i]);
      byteArr.CopyTo(bytesData, i * 2);
    }

    fileStream.Write(bytesData, 0, bytesData.Length);
  }

  static void WriteHeader(FileStream fileStream, AudioClip clip) {

    var hz = clip.frequency;
    var channels = clip.channels;
    var samples = clip.samples;

    fileStream.Seek(0, SeekOrigin.Begin);

    Byte[] riff = System.Text.Encoding.UTF8.GetBytes("RIFF");
    fileStream.Write(riff, 0, 4);

    Byte[] chunkSize = BitConverter.GetBytes(fileStream.Length - 8);
    fileStream.Write(chunkSize, 0, 4);

    Byte[] wave = System.Text.Encoding.UTF8.GetBytes("WAVE");
    fileStream.Write(wave, 0, 4);

    Byte[] fmt = System.Text.Encoding.UTF8.GetBytes("fmt ");
    fileStream.Write(fmt, 0, 4);

    Byte[] subChunk1 = BitConverter.GetBytes(16);
    fileStream.Write(subChunk1, 0, 4);

    UInt16 two = 2;
    UInt16 one = 1;

    Byte[] audioFormat = BitConverter.GetBytes(one);
    fileStream.Write(audioFormat, 0, 2);

    Byte[] numChannels = BitConverter.GetBytes(channels);
    fileStream.Write(numChannels, 0, 2);

    Byte[] sampleRate = BitConverter.GetBytes(hz);
    fileStream.Write(sampleRate, 0, 4);

    Byte[] byteRate = BitConverter.GetBytes(hz * channels * 2); // sampleRate * bytesPerSample*number of channels, here 44100*2*2
    fileStream.Write(byteRate, 0, 4);

    UInt16 blockAlign = (ushort) (channels * 2);
    fileStream.Write(BitConverter.GetBytes(blockAlign), 0, 2);

    UInt16 bps = 16;
    Byte[] bitsPerSample = BitConverter.GetBytes(bps);
    fileStream.Write(bitsPerSample, 0, 2);

    Byte[] datastring = System.Text.Encoding.UTF8.GetBytes("data");
    fileStream.Write(datastring, 0, 4);

    Byte[] subChunk2 = BitConverter.GetBytes(samples * channels * 2);
    fileStream.Write(subChunk2, 0, 4);

    //    fileStream.Close();
  }
}
```

## JSON 

출처: https://qiita.com/toRisouP/items/53be639f267da8845a42

### ObjectからJsonに変換する

- 第一引数にJson化したいオブジェクトインスタンスを渡す
- 第二引数のprettyPrintはJsonを読みやすく整形するかどうか（デフォルトfalse：整形しない）
- 対象オブジェクトのpublicフィールドがシリアライズされる
- プロパティは対象外
- privateフィールドをシリアライズに含みたい場合は[SerializeField]をつける
- publicフィールドをシリアライズから除外したい場合は[NonSerialized]をつける
- DictionaryやHashtableのような連想配列はシリアライズ対象外

```csharp
[Serializable] //なくても一応変換はされるが、一部挙動が変になるので必ずつけておくべき
class Data
{
    public int publicField = 123;
    public float publicFloat = float.MaxValue;
    public bool publicBool = true;
    public string publicString = "ABC";

    //NonSerializedをつけるとシリアライズされない
    [NonSerialized]
    public int ignoreFiled = 999;

    //配列にも対応
    public int[] publicIntArray = new[] { 1, 2, 3, 4, 5 };

    //プロパティはシリアライズされない
    public string publicProperty { get { return "Property"; } }

    //privateフィールドはシリアライズされない
    private int privateField = 123;

    //privateも含めたい場合は SerializeFieldAttribute をつける
    [SerializeField]
    private int privateField2 = 345;
}

var data = new Data();
var json = JsonUtility.ToJson(data,true); //整形する
```

### Jsonからオブジェクトに変換する

- 変換に失敗した場合はSystem.ArgumentExceptionが飛ぶ
- 要素がjson中に存在しない場合はデフォルト値が設定される(0,false,nullなど)
- 引数なしのコンストラクタを強制的に呼び出してインスタンス化する(引数ありのコンストラクタのみ定義して初期化している場合は注意!)

```csharp
[Serializable]
class Data3
{
    public int  intValue;
}

//json
var json = "{\"intValue\":123}";

//ジェネリック使わない版
var data = JsonUtility.FromJson(json, typeof(Data3)) as Data3;
//ジェネリックで型パラメータ渡す版
var data2 = JsonUtility.FromJson<Data3>(json);
```

### 既存のインスタンスをjsonの値で上書きする

```csharp
[Serializable]
private class Data5
{
    public int intValue;
    public int intValue2;

    public Data5(int i1,int i2)
    {
        intValue = i1;
        intValue2 = i2;
    }
}

var data = new Data5(100, 200);
var json = "{\"intValue\":300}";

//Jsonで上書き
JsonUtility.FromJsonOverwrite(json, data);

Debug.Log(data.intValue); // 300 ←上書きされている
Debug.Log(data.intValue2); // 200
```

## Tips 

### Clipboard에 복사 

```csharp
GUIUtility.systemCopyBuffer = "some text";
```

### HexColor Parsing 

```csharp
using UnityEngine;
using System;
using System.Globalization;

public class Util {

  public static Color ParseHexColor(string hexstring) {
    if (hexstring.StartsWith("#")) {
      hexstring = hexstring.Substring(1);
    }
    
    if (hexstring.StartsWith("0x")) {
      hexstring = hexstring.Substring(2);
    }

    byte a = 1;
    
    if (hexstring.Length == 8)  {
      a = byte.Parse(hexstring.Substring(6, 2), NumberStyles.HexNumber);
    }
    else {
      throw new Exception(string.Format("{0} is not a valid color string.", hexstring));
    }
    
    byte r = byte.Parse(hexstring.Substring(0, 2), NumberStyles.HexNumber);
    byte g = byte.Parse(hexstring.Substring(2, 2), NumberStyles.HexNumber);
    byte b = byte.Parse(hexstring.Substring(4, 2), NumberStyles.HexNumber);
    
    return new Color32(r, g, b, a);
  }

}
```
