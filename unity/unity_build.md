# Unity 빌드자동화

## Unity 스크립트로 빌드하기
Assets 경로 아래에 Editor 디렉토리에 해당 스크립트를 작성.

### 빌드 전에 리소스 교체

```csharp
static void SwapAsset(string source, string target) {
  UnityEngine.Debug.Log("Using: " + source);
  if (System.IO.File.Exists(target)) {
    UnityEngine.Debug.Log("Updating: " + target);
    FileUtil.ReplaceFile(source, target);
  } 
  else {
    UnityEngine.Debug.Log("Creating: " + target);
    FileUtil.CopyFileOrDirectory(source, target);
  }
}

static void SetupResourcesFor(BuildTarget target, bool debug_mode, bool is_trial) {
  IS_TRIAL_BUILD = is_trial;
  string icon_file = "";
  string splash_file = "";

  PlayerSettings.bundleVersion = Defines.APP_VERSION;
  PlayerSettings.iOS.buildNumber = Defines.APP_VERCODE.ToString();
  PlayerSettings.Android.bundleVersionCode = Defines.APP_VERCODE;
  PlayerSettings.Android.keystorePass = "installeng";
  PlayerSettings.Android.keyaliasPass = "installeng";

  if (is_trial) {
    splash_file = "logo_trial_v";
    icon_file = target != BuildTarget.iOS ? "icon-trial-android" : "icon-trial-ios";

    switch (target) {
      case BuildTarget.Android:
        PlayerSettings.applicationIdentifier = PACKAGE_ID_ANDROID_TRIAL;
        PlayerSettings.productName = APP_TITLE_TRIAL;
        break;

      case BuildTarget.iOS:
        PlayerSettings.applicationIdentifier = PACKAGE_ID_IOS_TRIAL;
        PlayerSettings.productName = APP_TITLE_TRIAL_IOS;
        break;
    }
  }
  else {
    splash_file = "logo_sp";
    icon_file = target != BuildTarget.iOS ? "icon-android-512px" : "icon-ios-512px";

    switch (target) {
      case BuildTarget.Android:
        PlayerSettings.applicationIdentifier = PACKAGE_ID_ANDROID;
        PlayerSettings.productName = APP_TITLE;
        break;

      case BuildTarget.iOS:
        PlayerSettings.applicationIdentifier = PACKAGE_ID_IOS;
        PlayerSettings.productName = APP_TITLE_IOS;
        break;
    }
  }

  SwapAsset(resourcePath + icon_file + ".png", Icon);
  SwapAsset(resourcePath + splash_file + ".png", SplashScreen);
  AssetDatabase.Refresh();
}
```

### 빌드 셋팅

```csharp
// Common
PlayerSettings.bundleVersion = Defines.APP_VERSION;

PlayerSettings.applicationIdentifier = PACKAGE_ID;
PlayerSettings.productName = APP_TITLE;


// Android 
PlayerSettings.Android.bundleVersionCode = APP_VERCODE;
PlayerSettings.Android.keystorePass = "xxxxx";
PlayerSettings.Android.keyaliasPass = "xxxxx";

// iOS
PlayerSettings.iOS.buildNumber = APP_VERSION;


// Defins
UNITY_DEFINES = "XXX;YYYY;ZZZZ";
PlayerSettings.SetScriptingDefineSymbolsForGroup(BuildTargetGroup.Android, UNITY_DEFINES);
```

### 빌드 수행

```csharp
private static string[] FindEnabledEditorScenes() {
  List<string> EditorScenes = new List<string>();
  foreach(EditorBuildSettingsScene scene in EditorBuildSettings.scenes) {
    if (!scene.enabled) continue;
    EditorScenes.Add(scene.path);
  }
  return EditorScenes.ToArray();
}

static void createBuildDirs() {
  var build_path = Application.dataPath + "/../" + TARGET_DIR;
  UnityEngine.Debug.Log("createBuildDirs..");

  Directory.CreateDirectory(build_path + "/osx/package");
  Directory.CreateDirectory(build_path + "/win");
  Directory.CreateDirectory(build_path + "/android");
  Directory.CreateDirectory(build_path + "/ios");

  Directory.CreateDirectory(build_path + "/Trial/ios");
  Directory.CreateDirectory(build_path + "/Trial/android");
  Directory.CreateDirectory(build_path + "/Trial/osx/package");
  Directory.CreateDirectory(build_path + "/Trial/win");
}

static void GenericBuild(string[] scenes, string target_filename, BuildTarget build_target, BuildOptions build_options) {
  createBuildDirs();

  EditorUserBuildSettings.SwitchActiveBuildTarget(build_target);

  UnityEngine.Debug.Log("--- RebuildAtlasCacheIfNeeded ---");
  UnityEditor.Sprites.Packer.RebuildAtlasCacheIfNeeded(build_target, true);
  UnityEngine.Debug.Log("--- END ---");

  string res = BuildPipeline.BuildPlayer(scenes, target_filename, build_target, build_options);
  if (res.Length > 0) {
    throw new Exception("BuildPlayer failure: " + res);
  }
}

// Example: Android Build
[MenuItem ("Build/Android")]
static void PerformAndroidBuild() {
  PerformAndroidBuild(false, false);
}

static void PerformAndroidBuild(bool debug_mode, bool is_trial_build) {
  ResetDefines();
  string target_filename = null;

  if (is_trial_build) {
    IS_TRIAL_BUILD = true;
    target_filename = TARGET_DIR + "/Trial/android/";
    target_filename += OUTPUT_APK_TRIAL;
  }
  else {
    IS_TRIAL_BUILD = false;
    target_filename = TARGET_DIR + "/android/";
    target_filename += (debug_mode ? "Debug_" : "") + OUTPUT_APK;
  }

  UNITY_DEFINES += ";MOBILE_INPUT";
  var build_options = ApplyDefines(debug_mode, is_trial_build);
  PlayerSettings.SetScriptingDefineSymbolsForGroup(BuildTargetGroup.Android, UNITY_DEFINES);

  SetupResourcesFor(BuildTarget.Android, debug_mode, is_trial_build);
  GenericBuild(FindEnabledEditorScenes(), target_filename, BuildTarget.Android, build_options);
}
```

## 빌드 후 처리
http://docs-jp.unity3d.com/Documentation/Manual/BuildPlayerPipeline.html

빌드 후에 추가 작업을 스크립트로 작성할 수 있다.

### Unity에서 처리하는 경우

[Unity Editor Script](unity_editor_script.md#빌드-후-처리) 참조.

### Mac OS X 환경 
**Assets/Editor** 폴더에 **PostprocessBuildPlayer**(확장자 없음) 파일을 만들고, perl, sh, ruby, python 등의 스크립트로 작성.

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

from mod_pbxproj import XcodeProject
import sys
import os
import os.path
import shutil

selfpath = sys.argv[0]
installpath = sys.argv[1]
target = sys.argv[2]

print "Postprocess Build Target: " + target + "\n" 
buildpath = os.path.join(os.path.dirname(selfpath),'../../Builds')

UNITY_PLUGINS_IOS = os.path.join(os.path.dirname(selfpath),'../Plugins/iOS')
UNITY_IOS_BUNDLES = os.path.join(os.path.dirname(selfpath),'../../iOS_Bundles')


def Edit_BuiildSettings(src_filename, new_filename):
  fs = open(src_filename)
  fn = open(new_filename, "w")

  lineNo = 0
  lines = fs.readlines()
  for line in lines:
    lineNo = lineNo + 1
    
    if line.endswith('FRAMEWORK_SEARCH_PATHS = (\n'):
      fn.write('ENABLE_BITCODE = NO;\n') #for XCode 7
      fn.write(line)
    else:
      fn.write(line)

  fs.close()
  fn.close()


###------- Main -------###


if target == "iPhone":
  projpath = installpath + "/Unity-iPhone.xcodeproj/project.pbxproj" 
  print ".pbxproj " + projpath + "\n";
  project = XcodeProject.Load(projpath)

  # add frameworks
  project.add_file('System/Library/Frameworks/CoreMedia.framework', tree='SDKROOT')
  project.add_file('System/Library/Frameworks/EventKit.framework', tree='SDKROOT')
  project.add_file('System/Library/Frameworks/EventKitUI.framework', tree='SDKROOT')
  project.add_file('System/Library/Frameworks/MessageUI.framework', tree='SDKROOT')

  #add flags
  #project.add_other_ldflags("-all_load")

  #add search path
  #project.add_header_search_paths(UNITY_PLUGINS_IOS)
  
  if project.modified:
    project.backup()
    project.save()

  # disable ATS (iOS 9)
  cmd = '/usr/libexec/PlistBuddy -c "Add :NSAppTransportSecurity dict" {0}/info.plist'.format(installpath)
  os.system(cmd)
  cmd = '/usr/libexec/PlistBuddy -c "Add :NSAppTransportSecurity:NSAllowsArbitraryLoads bool true" {0}/info.plist'.format(installpath)
  os.system(cmd)

  # add a URL scheme
  #cmd = '/usr/libexec/PlistBuddy -c "Add :CFBundleURLTypes array" {0}/info.plist'.format(installpath)
  #os.system(cmd)

  #cmd = '/usr/libexec/PlistBuddy -c "Add :CFBundleURLTypes:0:CFBundleURLSchemes array" {0}/info.plist'.format(installpath)
  #os.system(cmd)

  #cmd = '/usr/libexec/PlistBuddy -c "Add :CFBundleURLTypes:0:CFBundleURLSchemes:0 string jp.co.nexon.csrun" {0}/info.plist'.format(installpath)
  #os.system(cmd)

  # edit: Buiid Settings
  src_mm = projpath
  new_mm = projpath + ".bak"
  
  Edit_BuiildSettings(src_mm, new_mm)
  shutil.move(src_mm, projpath+'.___backup___')
  shutil.move(new_mm, src_mm)


#elif target == "android":
# pass
```

#### Windows 환경
Windows上で、PostprocessBuildPlayer スクリプトはサポートされていませんが、エディタ スクリプティングを使用して同じ効果を得られます。 BuildPipeline.BuildPlayer を使用してビルドを実行して、自分が必要なポストプロセッシングコードを続けることが出来ます。
```csharp
using UnityEditor;
using System.Diagnostics;

public class ScriptBatch : MonoBehaviour 
{
    [MenuItem("MyTools/Windows Build With Postprocess")]
    public static void BuildGame ()
    {
        // ファイル名の取得
        string path = EditorUtility.SaveFolderPanel("Choose Location of Built Game", "", "");

        // プレイヤーをビルド
        BuildPipeline.BuildPlayer(levels, path + "/BuiltGame.exe", BuildTarget.StandaloneWindows, BuildOptions.None);

        // ファイルをプロジェクトフォルダからビルドフォルダへビルドしたゲームと一緒にコピー
        FileUtil.CopyFileOrDirectory("Assets/WebPlayerTemplates/Readme.txt", path + "Readme.txt");

        // ゲームを実行（System.Diagnostics の Process クラス）
        Process proc = new Process();
        proc.StartInfo.FileName = path + "BuiltGame.exe";
        proc.Start();
    }
}
```
