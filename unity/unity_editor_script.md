# Unity Editor Script

## Inspector
스크립트에서 만든 개체 변수를 Inspector 인터페이스에 표시할 수 있다.

### C#

```csharp
[System.Serializable]
public class MyObject : MonoBehaviour {
  public float spd = 0.01f; // Initialize

  void Update () {
    transform.Translate(speed, 0, 0); // 매서드 안에서 변수를 사용하는 곳이 있어야 한다.
  }
}
```

또는 다음과 갈이 일부 필드에 대해서만 지정할 수도 있다.
```csharp
public class MyObject : MonoBehaviour {
  [SerializeField]  public float spd = 0.01f; // Initialize

  void Update () {
    transform.Translate(speed, 0, 0);
  }
}
```

SerializeField를 선언하지 않아도 public으로 설정된 필드에 대해서는 자동으로 인식.

### Java Script 

```javascript
var spd: float = 0.01;

function Update () {
  transform.position.x += spd;
}
```
자바 스크립트에서는 스크립트 안에 변수 선언만 있으면 된다.

## Custom Editor 만들기

```csharp
 [CustomEditor(typeof(DecalMeshHelper))]
 class DecalMeshHelperEditor : Editor {
   public override void OnInspectorGUI() {
     if(GUILayout.Button("Test"))
       Debug.Log("It's alive: " + target.name);
   }
 }
```

## Menu 항목 추가 
static 매소드에 \[MenuItem()] 속성을 추가한다.
```csharp
// Assets메뉴 하위.
[MenuItem("Assets/Make: Asset Bundles/Generate All from file (Android)")]
static void GenerateAll_Android()  {
  ExportAll(BuildTarget.Android); 
}  
```

## Editor Coroutine
본래 Coroutine은 Play Mode에서만 이용할 수 있다. 하지만 Editor Mode에서도 비슷하게 Coroutine을 흉내낼 수 있다.

소스 출처: https://qiita.com/k_yanase/items/686fc3134c363ffc5239

```csharp
/* EditorCoroutine.cs */
using UnityEngine;
using UnityEditor;
using System.Collections;
using System.Collections.Generic;
using System.Linq;

/// <summary>
/// エディター上でStartCorutineのような処理を使用可能にするクラスです 
/// </summary>
[InitializeOnLoad]
public sealed class EditorCoroutine {

  static EditorCoroutine() {
    EditorApplication.update += Update;
    Debug.Log("EditorCoroutine SetUp");
  }

  static Dictionary<IEnumerator, EditorCoroutine.Coroutine> asyncList = new Dictionary<IEnumerator, Coroutine>();
  static List<EditorCoroutine.WaitForSeconds> waitForSecondsList = new List<EditorCoroutine.WaitForSeconds>();

  static void Update() {

    CheackIEnumerator();
    CheackWaitForSeconds();
  }

  static void CheackIEnumerator() {
    List<IEnumerator> removeList = new List<IEnumerator>();
    foreach (KeyValuePair<IEnumerator, EditorCoroutine.Coroutine> pair in asyncList) {
      if (pair.Key != null) {

        //IEnumratorのCurrentがCoroutineを返しているかどうか 
        EditorCoroutine.Coroutine c = pair.Key.Current as EditorCoroutine.Coroutine;
        if (c != null) {
          if (c.isActive) continue;
        }
        //wwwクラスのダウンロードが終わっていなければ進まない 
        WWW www = pair.Key.Current as WWW;
        if (www != null) {
          if (!www.isDone) continue;
        }
        //これ以上MoveNextできなければ終了 
        if (!pair.Key.MoveNext()) {
          if (pair.Value != null) {
            pair.Value.isActive = false;
          }
          removeList.Add(pair.Key);
        }
      }
      else {
        removeList.Add(pair.Key);
      }
    }

    foreach (IEnumerator async in removeList) {
      asyncList.Remove(async);
    }
  }

  static void CheackWaitForSeconds() {
    for (int i = 0; i < waitForSecondsList.Count; i++) {
      if (waitForSecondsList[i] != null) {
        if (EditorApplication.timeSinceStartup - waitForSecondsList[i].InitTime > waitForSecondsList[i].Time) {
          waitForSecondsList[i].isActive = false;
          waitForSecondsList.RemoveAt(i);
        }
      }
      else {
        Debug.LogError("rem");
        waitForSecondsList.RemoveAt(i);
      }
    }
  }

  //=====================================================================================
  //関数 

  /// <summary>
  /// コルーチンを起動します 
  /// </summary>
  static public EditorCoroutine.Coroutine Start(IEnumerator iEnumerator) {
    if (Application.isEditor && !Application.isPlaying) {
      EditorCoroutine.Coroutine c = new Coroutine();
      if (!asyncList.Keys.Contains(iEnumerator)) asyncList.Add(iEnumerator, c);
      iEnumerator.MoveNext();
      return c;
    }
    else {
      Debug.LogError("EditorCoroutine.Startはゲーム起動中に使うことはできません");
      return null;
    }
  }

  /// <summary>
  /// コルーチンを停止します 
  /// </summary>
  static public void Stop(IEnumerator iEnumerator) {
    if (Application.isEditor) {
      if (asyncList.Keys.Contains(iEnumerator)) {
        asyncList.Remove(iEnumerator);
      }
    }
    else {
      Debug.LogError("EditorCoroutine.Startはゲーム中に使うことはできません");
    }
  }

  /// <summary>
  /// 使用不可
  /// WaitForSecondsのインスタンスを登録します 
  /// </summary>
  static public void AddWaitForSecondsList(EditorCoroutine.WaitForSeconds coroutine) {
    if (waitForSecondsList.Contains(coroutine) == false) {
      waitForSecondsList.Add(coroutine);
    }
  }


  //=====================================================================================
  //待機処理用クラス 

  public class Coroutine {
    //trueなら待機させる 
    public bool isActive;

    public Coroutine() {
      isActive = true;
    }
  }

  public sealed class WaitForSeconds : EditorCoroutine.Coroutine {
    private float time;
    private double initTime;

    public float Time {
      get { return time; }
    }
    public double InitTime {
      get { return initTime; }
    }

    public WaitForSeconds(float time) : base() {
      this.time = time;
      this.initTime = EditorApplication.timeSinceStartup;
      EditorCoroutine.AddWaitForSecondsList(this);
    }
  }
}
```

## Utils
### File Utils 

* 파일, 디렉토리 이동

```csharp
FileUtil.MoveFileOrDirectory(from_path, to_path);
```

## 빌드 후 처리
Assets/Editor 경로에 다음과 같은 C# 코드를 작성.
```csharp
using UnityEditor;
using UnityEditor.Callbacks;
using System.Diagnostics;

public class CustomPostprocessScript
{
  //IMPORTANT!!!
  //100 is order , it means this one will execute after e.g 1 as default one is 1 
  //it means our script will run after all other scripts got run
  [PostProcessBuild(100)]
  public static void OnPostprocessBuild(BuildTarget target, string pathToBuildProject)
  {        
    UnityEngine.Debug.Log("----Custome Script---Executing post process build phase.");    
    string objCPath = Application.dataPath + "/ObjC";
    Process myCustomProcess = new Process();    
    myCustomProcess.StartInfo.FileName = "python";
    myCustomProcess.StartInfo.Arguments = string.Format("Assets/Editor/post_process.py \"{0}\" \"{1}\"", pathToBuildProject, objCPath);
    myCustomProcess.StartInfo.UseShellExecute = false;
    myCustomProcess.StartInfo.RedirectStandardOutput = false;
    myCustomProcess.Start(); 
    myCustomProcess.WaitForExit();
    UnityEngine.Debug.Log("----Custome Script--- Finished executing post process build phase.");      
  }
}
```
