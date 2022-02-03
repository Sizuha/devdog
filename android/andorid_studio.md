# Android Studio

## 설정

1. in AndroidStudio's settings > compile enable checkbox named Compile independent modules in parallel.
1. Under Help> Edit Custom Vmoptions
```
-Xms1024m
-Xmx4096m
-XX:MaxPermSize=1024m
-XX:ReservedCodeCacheSize=256m
-XX:+UseCompressedOops
```

## Build 문제 해결
### MissingTranslation
strings.xml 등에서 다른 언어에 대한 번역본이 없는 경우.

의도된 거라면 다음과 같이 에러를 무시할 수 있다.
```xml
<resources>
    <string name="foo" translatable="false">bar</string>
</resources>
```

strings.xml 파일 전체에 대해 무시를 하려면,
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:tools="http://schemas.android.com/tools" tools:ignore="MissingTranslation">
    <string name="api_url">http://foo.bar/api/</string>
</resources>
```

## Import AAR

### 방법1: App 프로젝트에 AAR 추가하기
File > New Module... > Import .JAR/.AAR Pacakge

AAR 파일을 선택한다.

File > Project Structure... > Dependencies 에서 추가한 라이브러리 모듈을 추가해 주거나, App의 build.gradle에 다음 내용을 추가.
```
dependencies {
//...
    compile project(':모듈이름')
//...
}
```

###  방법2: Gradle 설정
Project의 build.gradle
```
allprojects {
    repositories {
        jcenter()
        mavenCentral()

        flatDir {
            dirs 'libs' // aar 파일이 있는 경로
        }
    }
}
```

App의 build.gradle
```
// compile '패키지:라이브러리_이름:버전@aar'
compile 'com.library.slib:slib:0.2.3@aar'
```

## Export AAR 
Module의 build.gradle
```
android {
    defaultConfig {
        versionCode 1
        versionName "0.2.7" // Version

        project.archivesBaseName = "라이브러리 이름"
        project.version = android.defaultConfig.versionName
    }

    libraryVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            if (outputFile != null && outputFile.name.endsWith('.aar')) {
                def fileName = "${archivesBaseName}-${version}.aar" // 빌드 후 만들어질 AAR 파일에 대한 네이밍.
                output.outputFile = new File(outputFile.parent, fileName)
            }
        }
    }
}
```

## Gradle Script

### 복수의 Application ID 설정
참고
* https://developer.android.com/studio/build/build-variants.html#product-flavors

**app.gradle**에서 productFlavors 설정을 추가하여 제품별로 Application ID 설정 등을 적용할 수 있다.

```
android {
    ...
    defaultConfig {...}
    buildTypes {...}
    productFlavors {
        demo {
            applicationIdSuffix ".demo"
            versionNameSuffix "-demo"
        }
        full {
            applicationIdSuffix ".full"
            versionNameSuffix "-full"
        }
    }
}
```

특정 제품을 실행/빌드 하고자 할 경우, 메뉴에서 **Build > Select Build Variant** 항목을 선택.

Android Studio 3.0에서 flavorDimensions 선언이 필수가 되었다.
```
flavorDimensions "main"

productFlavors {
    general {
        dimension "main"
        applicationIdSuffix ".general"
    }
    extend {
        dimension "main"
        applicationIdSuffix ".extend"
    }
}
```

### JAR export
참고
* http://www.thegamecontriver.com/2015/04/android-plugin-unity-android-studio.html

#### App/build.gradle 편집

```
//apply plugin: 'com.android.application'
apply plugin: 'com.android.library' // 반드시 라이브러리 프로젝트로 빌드해야 함.

android {
    compileSdkVersion 22
    buildToolsVersion "21.1.2"

    sourceSets {
        main {
            //Path to your source code
            java {
                srcDir 'src/main/java'
            }
        }
    }

    defaultConfig {
        //applicationId "jp.co.gngs.projectd" // 라이브러리 프로젝트에서는 Application ID 등은 지정 불가.
        minSdkVersion 11
        targetSdkVersion 22
        //versionCode 1
        //versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

    lintOptions {
        abortOnError false
    }
}

dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile 'com.android.support:appcompat-v7:22.2.0'
    compile files('libs/classes.jar')
}

//task to delete the old jar
task deleteOldJar(type: Delete) {
    delete 'release/AndroidPlugin.jar'
}

//task to export contents as jar
task exportJar(type: Copy) {
    from('build/intermediates/bundles/release/')
    into('release/')
    include('classes.jar')
    ///Rename the jar
    rename('classes.jar', 'AndroidPlugin.jar')
}

exportJar.dependsOn(deleteOldJar, build)
```

#### 실행
Gradle tasks 리스트에서 Taks/others/exportJar 태스크를 실행하면, App/release/xxxx.jar 파일이 생성된다.

### 빌드 후의 APK/ARR 이름 변경
예를들어, 파일명 뒤에 버전 정보를 넣고자 하는 경우.

app 모듈의 build.gradle 수정.

APK
```
android {
    defaultConfig {
        project.archivesBaseName = "xxxxx"
        project.version = android.defaultConfig.versionName + "." + android.defaultConfig.versionCode
    }
    applicationVariants.all { variant ->
        if (variant.buildType.name == "release") {
            variant.outputs.all { output ->
                outputFileName = "${archivesBaseName}-${version}.apk"
            }
        }
    }
}
```

ARR
```
android {
    defaultConfig {
        project.archivesBaseName = "xxxxx"
        project.version = android.defaultConfig.versionName + "." + android.defaultConfig.versionCode         
    }

    libraryVariants.all { variant ->
        variant.outputs.all { output ->
            if (output.outputFileName != null && output.outputFileName.endsWith('.aar')) {
                outputFileName = "${archivesBaseName}-${version}.aar"
            }
        }
    }
}
```
