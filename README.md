# AndroidStudio-Unity-Integration
안드로이드 스튜디오에 Unity를 통합하는 방법에 대해 작성합니다.

순서
- [게임 배포](#게임-배포)
- [안드로이드 스튜디오에서 실행 테스트](#안드로이드-스튜디오에서-실행-테스트)
- [안드로이드 스튜디오와 unity 통합](#안드로이드-스튜디오와-unity-통합)
# 버전
unity : 2021.3.21f1
NDK : 21.3.6528147
android studio : 
jdk : 11.0.16.1

## 게임 배포
### Build Settings
- file-build settings 에서 Platform을 Android로 설정해줍니다.
- 위의 Scene In Build에 배포하고 싶은 Scene을 드래그앤 드랍해줍니다.
- 오른쪽 Android 탭에서 Export Project를 체크해주고 아래의 Export 버튼을 선택하여 게임을 Export 합니다.

## 안드로이드 스튜디오에서 실행 테스트
- Export한 폴더를 Import 하여 게임이 안드로이드 스튜디오 환경에서 잘실행되는지 테스트

## 안드로이드 스튜디오와 unity 통합
- Export 된 폴더에서 unityLibrary를 사용하고자 하는 안드로이드 스튜디오 폴더 root에 복사합니다.
- unityLibrary - manifests 파일에서 아래 내용을 삭제해줍니다.
```
      <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
```

- app - manifests - AnroidManifests.xml 파일을 수정해줍니다.
```
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.INTERNET"/>

<!-- 아래 내용 추가  -->
    <meta-data android:name="unityplayer.UnityActivity" android:value="true" />

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/Theme.AndroidStudio"
        android:usesCleartextTraffic="true"
        tools:targetApi="31">
        <activity
            android:name=".Test"
            android:exported="false" />
        <activity
            android:name=".Game"
            android:exported="false" />
        <activity
            android:name=".GPSContainer"
            android:exported="false" />
        <activity
            android:name=".WebviewContainer"
            android:exported="false" />
            
          <!-- 아래 내용 추가  -->
        <activity android:name="com.unity3d.player.UnityPlayerActivity"
            android:theme="@style/UnityThemeSelector"
            android:screenOrientation="fullUser"
            android:launchMode="singleTask"
            android:configChanges="mcc|mnc|locale|touchscreen|keyboard|keyboardHidden|navigation|orientation|screenLayout|uiMode|screenSize|smallestScreenSize|fontScale|layoutDirection|density"
            android:resizeableActivity="false"
            android:hardwareAccelerated="false"

            android:process=".UnityKillsMe"
            android:exported="false"
            />
            
            
            
        <activity
            android:name=".MainActivity"
            android:exported="true" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>

```

- app-values-strings.xml 파일에 추가
```
    <string name="game_view_content_description">Game View</string>
```



### Gradle Scripts 수정    
- build.gradle(Module:app) 파일 추가
```
    implementation project(':unityLibrary')
    implementation fileTree(dir: project(':unityLibrary').getProjectDir().toString() + ('\\libs'), include: ['*.jar'])

```

- gradle.properties 파일 추가
```
unityStreamingAssets=.unity3d
```

- local.properties 파일에 NDK 속성 추가
- ndk 폴더 위치 확인하는 방법 Edit - preferences 파일에서 Android 탭의 NDK의 경로를 확인할 수 있음
```
ndk.dir=C\:\\Program Files/Unity\\Hub\\Editor\\2021.3.21f1-x86_64\\Editor\\Data\\PlaybackEngines\\AndroidPlayer\\NDK
```

- settings.gradle 파일에 추가
```
include ':unityLibrary'
project(':unityLibrary').projectDir=new File('.\\unityLibrary')
```

