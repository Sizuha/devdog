
# Storyboardを使わない、iOSプロジェクトの作成手順

まず、XCodeから新しい 「iOS App」プロジェクトを作成。

以後、下記の手順通りに。（※ Deployment tagetは「iOS 13.0」以上とする）

## Storyboard削除

1. プロジェクト選択 → TARGETSから、アプリ選択 → Generalタプ → Deployment Info → Main interface 項目を**空にする**.

1. プロジェクトから「Main.storybaord」を削除
   1. 注意！「LaunchScreen.storyboard」は削除しないこと！

1. Info.plistから、「Main」のStoryboard情報を削除<br/>
![Swift-del-storyboard-ios13](https://sizuha.github.io/devdog/images/Swift-del-storyboard-ios13.png)

1. SceneDelegate.swiftを修正
```swift
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        guard let windowScene = (scene as? UIWindowScene) else { return }
        window = UIWindow(frame: windowScene.coordinateSpace.bounds)
        window?.windowScene = windowScene
        
        let main = MainViewController() // メイン画面のクラス
        let navi = UINavigationController()
        navi.pushViewController(main, animated: false)
        
        window?.rootViewController = navi        
        window?.makeKeyAndVisible()
    }
```

## Development Language（アプリの基本言語）の変更

1. Close Xcode.
1. Open your **project.pbxproj** in a text editor
1. Look for **developmentRegion** and set its value (es for spanish, fr for french,...)
1. Look for **knownRegions** and add the new language to the list
1. Open Xcode: Your project info should now show the language name with Development Language next to it.
