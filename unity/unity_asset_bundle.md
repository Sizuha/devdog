# Unity Asset Bundle

어셋 번들에 포함될 수 있는 형식은 다음과 같다.
* GameObject
* Prefab
* Material
* Texture

## AssetBundle Build Script
### from Simple TXT
단순한 파일 리스트 형태의 텍스트 파일로부터 Asset Bunlde 만들기.
```csharp
using UnityEngine;
using UnityEditor;
using System.Collections;
using System.IO;

public class MakeAssetBundle : MonoBehaviour {

  // 메뉴가 들어갈 위치.
  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (Android)")]
  static void GenerateAll_Android()  {
    ExportAll(BuildTarget.Android); 
  }  

  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (iOS)")]
  static void GenerateAll_IOS()  {
    ExportAll(BuildTarget.iPhone); 
  }  

  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (Windows)")]
  static void GenerateAll_Win()  {
    ExportAll(BuildTarget.StandaloneWindows); 
  }  
  
  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (OS X)")]
  static void GenerateAll_Mac()  {
    ExportAll(BuildTarget.StandaloneOSXIntel); 
  }  

  static void ExportAll(BuildTarget target) {
    // {ResourcePath}/patch_list.txt 파일에서 어셋 번들을 만들 리스트를 가져온다.
    TextAsset filelist_obj = Resources.Load("patch_list", typeof(TextAsset)) as TextAsset;    
    string[] filelist = filelist_obj.text.Split('\n');

    foreach (string original_path in filelist) {  
      string path = original_path.Trim();

      // # is remarks
      if (path.Length <= 0 || path.StartsWith("#"))
        continue;    

      string filename = Path.GetFileNameWithoutExtension(path);
      
      // 번들 파일이 생성될 경로.
      // {ProjectPath}/patch/{BuildTargetName}
      Directory.CreateDirectory(@"patch/" + target.ToString() + "/");
      string output_path = string.Format("patch/{0}/{1}.unity3d", target.ToString(), filename);    
      
      string included_path = "Assets/" + path;    
      UnityEngine.Object obj = Resources.LoadAssetAtPath(included_path, typeof(UnityEngine.Object));    
      if (obj == null)    
      {    
        Debug.LogError("Cannot find the resource. " + included_path);    
        continue;    
      }    
      
      // Asset Bundle 생성
      BuildPipeline.BuildAssetBundle(obj, null, output_path,    
                                     BuildAssetBundleOptions.CollectDependencies | BuildAssetBundleOptions.CompleteAssets,    
                                     target);    
      
      Debug.Log("completed... : " + included_path);
    }
  }
}
```

### from XML
XML 형식의 데이터 리스트로부터 Asset Bundle 만들기. 그룹 오프젝트 기능 추가.
```csharp
using UnityEngine;
using UnityEditor;
using System.Collections;
using System.Collections.Generic;
using System.IO;
using System.Xml;

public class MakeAssetBundle : MonoBehaviour {

  // 메뉴가 들어갈 위치입니다. Assets메뉴 하위.
  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (Android)")]
  static void GenerateAll_Android()  {
    ExportAll(BuildTarget.Android); 
  }  

  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (iOS)")]
  static void GenerateAll_IOS()  {
    ExportAll(BuildTarget.iPhone); 
  }  

  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (Windows)")]
  static void GenerateAll_Win()  {
    ExportAll(BuildTarget.StandaloneWindows); 
  }  
  
  [MenuItem("Assets/Make: Asset Bundles/Generate All from file (OS X)")]
  static void GenerateAll_Mac()  {
    ExportAll(BuildTarget.StandaloneOSXIntel); 
  }

  private static void WriteVersionInfo(StreamWriter wr, string file_name, int version, BuildTarget target) {
    string target_text = "";

    switch (target) {
    case BuildTarget.iPhone:
      target_text = "ios";
      break;
    
    case BuildTarget.StandaloneOSXIntel:
      target_text = "osx";
      break;

    case BuildTarget.StandaloneWindows:
      target_text = "win";
      break;

    case BuildTarget.Android: 
      target_text = "android";
      break;
    }

    wr.WriteLine(file_name + "," + version + "," + target_text);
  }

  static void ExportByNode(BuildTarget target, string output_path, XmlNode node, StreamWriter verWriter) {
    string groupName = node.Attributes["name"] != null ? node.Attributes["name"].Value : "";
    bool group_mode = !string.IsNullOrEmpty(groupName);

    string workpath = node.Attributes["path"] != null ? node.Attributes["path"].Value : "";

    string ver_str = node.Attributes["ver"] != null ? node.Attributes["ver"].Value : "0";
    int version = string.IsNullOrEmpty(ver_str) ? 0 : int.Parse(ver_str);

    List<string> files = new List<string>();
    string[] filelist_text = node.InnerText.Split('\n');

    foreach (string original_path in filelist_text) {
      string path = original_path.Trim();
      if (string.IsNullOrEmpty(path)) continue;

      files.Add("Assets/" + workpath + "/" + path);
    }

    List<UnityEngine.Object> obj_list = new List<UnityEngine.Object>();

    foreach (string src in files) {
      // 리스트에 기입된 경로에서 오브젝트를 로드합니다.  
      UnityEngine.Object obj = Resources.LoadAssetAtPath(src, typeof(UnityEngine.Object));
      if (obj == null) {
        Debug.LogError("Cannot find the resource. " + src);    
        continue;    
      }

      if (group_mode) {
        obj_list.Add(obj);
      }
      else {
        string file_name = Path.GetFileNameWithoutExtension(src) + ".unity3d";
        string out_name = output_path + file_name ;

        BuildPipeline.BuildAssetBundle(
          obj, new UnityEngine.Object[] { obj }, out_name,
          BuildAssetBundleOptions.CollectDependencies | BuildAssetBundleOptions.CompleteAssets,
          target);

        Debug.Log("completed... : " + src); 
        WriteVersionInfo(verWriter, file_name, version, target);
      }
    }

    if (group_mode && obj_list.Count > 0) {
      string file_name = groupName + ".unity3d";
      string out_name = output_path + file_name;
      
      BuildPipeline.BuildAssetBundle(
        obj_list[0], obj_list.ToArray(), out_name,
        BuildAssetBundleOptions.CollectDependencies | BuildAssetBundleOptions.CompleteAssets,
        target);

      Debug.Log("completed... : " + out_name);
      WriteVersionInfo(verWriter, file_name, version, target);
    }

    obj_list.Clear();
  }

  static void ExportAll(BuildTarget target) {
    XmlTextReader reader = new XmlTextReader("patch_list.xml");

    XmlDocument xml = new XmlDocument ();
    xml.Load(reader);
    XmlElement xmlRoot = xml.DocumentElement;

    string output_path = xmlRoot.Attributes["outpath"].Value + "/" + target.ToString() + "/";
    Directory.CreateDirectory(output_path);

    StreamWriter verFile = File.CreateText(output_path + "version.txt");

    // Root
    foreach (XmlNode node in xmlRoot.ChildNodes) {
      if (node.Name.Equals("AssetBundle")) {
        ExportByNode(target, output_path, node, verFile);
      }
    }

    reader.Close();
    verFile.Close();
  }
}
```

XML Sample.
```xml
<PatchList outpath="patch">

<AssetBundle name="bgm" path="NonBuildRes" ver="0">
  Musics/01 main menu.mp3
  Musics/02 stage 01.mp3
  Musics/03 stage 02.mp3
  Musics/04 stage 03.mp3
  Musics/05 gameover.mp3
  Musics/06 rank up.mp3
  Musics/07 randum drum.mp3
  Musics/08 get item.mp3
  Musics/09 shop.mp3
  Musics/10 fever.mp3
  Musics/12 op2.mp3
  Musics/shin_bgm_title.mp3
</AssetBundle>

<AssetBundle name="opening" path="NonBuildRes/Opening" ver="0">
  01_emotion_01.png
  01_emotion_ase.png
  01.jpg
  02_01.png
  02_02.png
  02_03.png
  02_hatena.png
  02_serif.png
  03_!!.png
  03_01.png
  03_02.png
  03_AKamen.png
  03_omocha.png
  03_serif.png
  03.png
  04.png
  05_bgeffect.png
  05_emotion.png
  05.jpg
  06_logo_cn.png
  06_logo_en.png
  06_logo_jp.png
  06_logo_kr.png
  06.jpg
  UFO.png
</AssetBundle>  

</PatchList>
```

## AssetBundle Download
アセットバンドルをダウンロードする方法は二つあります。
*キャッシュなし: これは新しい WWW object を作成することで出来ます。アセットバンドルは、ローカルストレージデバイスのUnityのCacheフォルダにキャッシュされません。
```csharp
using System;
using UnityEngine;
using System.Collections; class NonCachingLoadExample : MonoBehaviour {
   public string BundleURL;
   public string AssetName;
   IEnumerator Start() {
     // URLからファイルをダウンロード。キャッシュにセーブされません。
     using (WWW www = new WWW(BundleURL)) {
       yield return www;
       if (www.error != null)
         throw new Exception("WWWダウンロードにエラーがありました:" + www.error);
       AssetBundle bundle = www.assetBundle;
       if (AssetName == "")
         Instantiate(bundle.mainAsset);
       else
         Instantiate(bundle.Load(AssetName));
                   // メモリ節約のため圧縮されたアセットバンドルのコンテンツをアンロード
                   bundle.Unload(false);

     } // memory is freed from the web stream (www.Dispose() gets called implicitly)
   }
}
```

* キャッシュあり: これは WWW.LoadFromCacheOrDownload をコールします。アセットバンドルはローカルストレージデバイスのUnityのCacheフォルダにキャッシュされます。WebPlayerの共有キャッシュは最大50MBまでのキャッシュされたアセットバンドルを共有できます。PC/MacスタンドアローンのアプリケーションおよびiOS/Androidアプリケーションは上限が4GBです。専用キャッシュを使用するWebPlayerアプリケーションは、キャッシングのライセンス条項に指定されたバイト数が上限となります。他のプラットフォームについてはスクリプティング ドキュメントを参照下さい。
```csharp
using System;
using UnityEngine;
using System.Collections;

public class CachingLoadExample : MonoBehaviour {
  public string BundleURL;
  public string AssetName;
  public int version;

  void Start() {
    StartCoroutine (DownloadAndCache());
  }

  IEnumerator DownloadAndCache (){
    // キャッシュシステムの準備が完了するのを待ちます
    while (!Caching.ready)
      yield return null;

    // 同じバージョンが存在する場合はアセットバンドルをキャッシュからロードするか、またはダウンロードしてキャッシュに格納します。
    using(WWW www = WWW.LoadFromCacheOrDownload (BundleURL, version)){
      yield return www;
      if (www.error != null)
        throw new Exception("WWWダウンロードにエラーがありました:" + www.error);
      AssetBundle bundle = www.assetBundle;
      if (AssetName == "")
        Instantiate(bundle.mainAsset);
      else
        Instantiate(bundle.Load(AssetName));
                  // メモリ節約のため圧縮されたアセットバンドルのコンテンツをアンロード
                  bundle.Unload(false);

    } // memory is freed from the web stream (www.Dispose() gets called implicitly)
  }
}
```

## Object Load/Unload

```csharp
using UnityEngine;

// 注意: このサンプルはエラーチェックを行いません。詳細についてはDownloadingAssetBundlesのサンプルを参照下さい。
IEnumerator Start () {
  // 特定URLからダウンロードを開始
  WWW www = WWW.LoadFromCacheOrDownload (url, 1);

  // ダウンロード完了を待機
  yield return www;

  // アセットバンドルをロードし取得
  AssetBundle bundle = www.assetBundle;

  // オブジェクトを非同期ロード
  AssetBundleRequest request = bundle.LoadAsync ("myObject", typeof(GameObject));

  // 完了を待機
  yield return request;

  // ロードされたオブジェクトの参照を取得
  GameObject obj = request.asset as GameObject;

        // メモリ節約のため圧縮されたアセットバンドルのコンテンツをアンロード
        bundle.Unload(false);

        // Frees the memory from the web stream
        www.Dispose();
}
```

## Library

### Asset Bundle Manager

```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class AssetBundleManager : MonoBehaviour {

  private static AssetBundleManager _instance;

  public static AssetBundleManager Instance {
    get {
      if (!_instance) {
        _instance = GameObject.FindObjectOfType<AssetBundleManager>();

        if (!_instance) {
          GameObject container = new GameObject();
          container.name = "AssetBundleManager";
          _instance = container.AddComponent<AssetBundleManager>();
          DontDestroyOnLoad(_instance);
        }
      }

      return _instance;
    }
  }

  public delegate void OnObjLoad(UnityEngine.Object loaded_obj);
  public delegate void OnLoadFinish();
  public delegate void OnBundleLoad(AssetBundle bundle);


  private UnityEngine.Object lastObj;
  public Dictionary<string, UnityEngine.Object> objDic = new Dictionary<string, UnityEngine.Object>();

  public  T GetLastObj<T>() where T : UnityEngine.Object {
    return (this.lastObj == null) ? null : this.lastObj as T;
  }

  public void ClearAll(bool destroy = true) {
    if (destroy) {
      foreach (UnityEngine.Object o in this.objDic.Values) Destroy(o);

      if (this.lastObj !=  null) {
        Destroy(this.lastObj);
        this.lastObj = null;
      }
    }

    this.objDic.Clear();
  }

  public IEnumerator LoadBundle(string url, int version, OnBundleLoad onLoad) {
    // キャッシュシステムの準備が完了するのを待ちます.
    while (!Caching.ready) yield return null;
    
    // 特定URLからダウンロードを開始.
    using (WWW www = WWW.LoadFromCacheOrDownload (url, version)) {
      // ダウンロード完了を待機.
      yield return www;
      
      // アセットバンドルをロードし取得.
      AssetBundle bundle = www.assetBundle;
      onLoad(bundle);

      // メモリ節約のため圧縮されたアセットバンドルのコンテンツをアンロード.
      bundle.Unload(false);
    } // Frees the memory from the web stream.
  }

  public IEnumerator LoadObjects<T>(string url, int version, OnObjLoad onLoad, OnLoadFinish onFinished, params string[] objNames) 
    where T : UnityEngine.Object 
  {
    // キャッシュシステムの準備が完了するのを待ちます.
    while (!Caching.ready) yield return null;

    UnityEngine.Object last_obj = null;

    // 特定URLからダウンロードを開始.
    using (WWW www = WWW.LoadFromCacheOrDownload (url, version)) {
      // ダウンロード完了を待機.
      yield return www;
    
      // アセットバンドルをロードし取得.
      AssetBundle bundle = www.assetBundle;

      if (objNames.Length < 1) {
        last_obj = Instantiate(bundle.mainAsset);
        if (onLoad != null) onLoad(last_obj);
      }
      else {
        foreach (string obj_name in objNames) {
          // オブジェクトを非同期ロード.
          AssetBundleRequest request = bundle.LoadAsync(obj_name, typeof(T));
        
          // 完了を待機.
          yield return request;
        
          objDic[obj_name] = last_obj = Instantiate(request.asset);
          if (onLoad != null) onLoad(last_obj);
        }
      }

      // メモリ節約のため圧縮されたアセットバンドルのコンテンツをアンロード.
      bundle.Unload(false);
    } // Frees the memory from the web stream.

    if (onFinished != null) {
      onFinished();
    }

    this.lastObj = last_obj;
  }

}
```

## 참고
* http://docs-jp.unity3d.com/Documentation/Manual/AssetBundlesIntro.html
