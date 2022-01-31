# Jenkins

[공식 사이트](http://jenkins-ci.org)

## Installation

### Plugins

플러그인 리스트: https://wiki.jenkins-ci.org/display/JENKINS/Plugins

사용가능한 플러그인 페이지( http://localhost:8080/pluginManager/available )에서 Plugin을 찾아서 체크하고 설치 후 젠킨스를 리스타트 해준다.

### 문제 및 해결

#### http://localhost:8080 에 접속이 되지 않는다

최신 버전 JDK가 설치되어 있는지 확인해 보라.

정확한 에러는 다음 로그를 확인. 
```
$ nano /var/log/jenkins/jenkins.log
```

그래도 해결이 안된다면, log 파일의 권한 문제일 수 있다. 
- 참고: http://stackoverflow.com/questions/26483089/launchd-is-not-starting-jenkins-server-on-os-x-yosemite

다음과 같이 진행.
```
$ sudo chown jenkins /var/log/jenkins/jenkins.log
$ sudo nano /etc/newsyslog.d/jenkins.conf
```
/etc/newsyslog.d/jenkins.conf 파일 내용을 다음과 같이 바꾼다.
```
/var/log/jenkins/jenkins.log         644  3    *   $D0   J
```
to
```
/var/log/jenkins/jenkins.log   jenkins:jenkins    644  3    *   $D0   J
```
이는 다음에 생성되는 로그파일들도 계속해서 jenkins 유저가 소유자 권한을 가지도록 한다.

## 관리
### Restart

```
$ sudo launchctl unload /Library/LaunchDaemons/org.jenkins-ci.plist
$ sudo launchctl load /Library/LaunchDaemons/org.jenkins-ci.plist
```

### SSH
빌드 프로세스에서 ssh 연결을 사용하고자 할 때, Jenkins 계정에 ssh 설정이 필요하다.
```
$ sudo su - Jenkins
$ mkdir .ssh
$ cd ~/.ssh
$ sudo nano config
```
Jenkins 계정의 홈 디렉토리 경로: \*/Users/Shared/Jenkins/ (macOS)

## Unity3D Build
### 설치해야될 플러그인

- Git 등의 버전관리 플러그인
  - Git의 경우: Git Client, Git Server, Git Parameter
- Unity3d Build plugin
- Xcode integration (iOS 빌드용)

### Build Settings
Job에 Unity3dBuilder 플러그인을 추가하고, Invoke Unity3d Editor 항목의 Editor command line arguments에 다음과 같은 형식으로 추가.
```
-quit -batchMode -executeMethod ProjectBuilder.PerformAndroidBuild
```

빌드 시점에서 DEFINE 심볼을 설정하고자 할 때는,
```csharp
static void Pattern1 {
    PlayerSettings.SetScriptingDefineSymbolsForGroup (BuildTargetGroup.Android, "TEST1;TEST2");
}

static void Pattern2 {
    PlayerSettings.SetScriptingDefineSymbolsForGroup (BuildTargetGroup.Android, "TEST3;TEST4");
}
```

* [참고](http://docs.unity3d.com/es/current/Manual/CommandLineArguments.html)

### Unity에서의 작업
Unity프로젝트의 Assets/Editor/ 경로에 다음 내용으로 ProjectBuilder.cs 파일을 생성.
```csharp
using UnityEngine;
using UnityEditor;
using System;
using System.Collections;
using System.Collections.Generic;
 
 
class ProjectBuilder {
	static string[] SCENES = FindEnabledEditorScenes();
	static string APP_NAME = "YourProject";
	static string TARGET_DIR = "build"; // !! 중요: Standalone 빌드 등의 경우에, 타겟 경로를 지정해 주지 않으면 빌드에 실패한다.
 
	[MenuItem ("Custom/CI/Build Android")]
	static void PerformAndroidBuild () {
		string target_filename = TARGET_DIR + "/" + APP_NAME + ".apk";
		GenericBuild(SCENES, target_filename, BuildTarget.Android ,BuildOptions.None);
	}
 
	private static string[] FindEnabledEditorScenes() {
		List<string> EditorScenes = new List<string>();
		foreach(EditorBuildSettingsScene scene in EditorBuildSettings.scenes) {
			if (!scene.enabled) continue;
			EditorScenes.Add(scene.path);
		}
		return EditorScenes.ToArray();
	}
 
	static void GenericBuild(string[] scenes, string target_filename, BuildTarget build_target, BuildOptions build_options) {
		EditorUserBuildSettings.SwitchActiveBuildTarget(build_target);
		string res = BuildPipeline.BuildPlayer(scenes, target_filename, build_target, build_options);
		if (res.Length > 0) {
			throw new Exception("BuildPlayer failure: " + res);
		}
	}
}
```

각 플랫폼 빌드 수행용 Static Method에는 다음과 같은 속성 부여. (이것은 유니티의 메인메뉴에 항목을 표시하기 위함.)
```csharp
[MenuItem ("Custom/CI/Build Android")]
[MenuItem ("Custom/CI/Build IOS")]
[MenuItem ("Custom/CI/Build Mac OS X")]
[MenuItem ("Custom/CI/Build Win32")]
```

#### Android Build
주의할 점은 keystore의 password를 지정해 줘야 한다는 것.
```csharp
	[MenuItem ("Custom/CI/Build Android")]
	static void PerformAndroidBuild() {
		string target_filename = APP_NAME + "_" + PlayerSettings.bundleVersion.Replace(".", "_") + ".apk";

		PlayerSettings.Android.keystorePass = "password";
		PlayerSettings.Android.keyaliasPass = "password";

		GenericBuild(FindEnabledEditorScenes(), target_filename, BuildTarget.Android ,BuildOptions.None);
	}
```

어셋을 분할 해서 apk + obb 형식으로 빌드하고자 할 경우,
```csharp
PlayerSettings.Android.useAPKExpansionFiles = true;
```

### 문제 및 해결
유니티 빌드 도중에 WindowsServer에 접속하지 못한다는 오류가 뜬다. 이는 LaunchDeamon 권한으로 Jenkins가 실행되기 때문.
* [참고](http://la-stranger.blogspot.kr/2013/10/unity-os-jenkins-2.html)

#### Jenkins Slave Agent를 이용해서 해결
1. 노드 관리자에서 Unity 빌드용 노드를 새로 추가.
1. Remote FS Root 경로 설정: /Users/{user}/Jenkins/slave
1. JNLP를 경유해서 실행 옵션 선택
1. Job 설정에서 _실행할 노드 제한(実行するノードを制限)_ 항목에서 위에서 설정한 노드 이름을 넣음.

