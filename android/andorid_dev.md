# Android 개발

## 프로젝트 디렉토리 구조 및 명명 규칙 (예시)
### Source (java)
기본 구조

- model
- ui
  - 각각의 Activity
    - adapter
    - dialog
    -  fragment
  - shared
    - adapter
    - dialog
    - fragment
- lib

#### model
데이터에 대한 추상모델. 주로 DB나 서버 통신으로 교환되는 내용등을 담게 된다.

#### ui
실질적인 View역할은 res 디렉토리에 있는 Layout XML이 담당하고 있으므로, 여기서는 View와 Model을 연결하고, User Interface 부분을 구현하는 데 중점을 둔다. 가령 Activity, Fragment, Adapter, Dialog 관련 내용이 여기에 해당한다. 각 Activity 별로 하위 디렉토리를 구성한다.

##### adapter
ListView 등, Adapter 개념이 들어가 있는 UI 요소들의 Adapter 부분을 정의.

##### dialog
Android에서 제공하는 기본 형식이 아닌, 커스텀 된 형식의 Dialog UI를 정의.

##### fragment
Activity 내에서 부분적으로 구현되는 Fragment들을 정의. 필요한 경우 각 Fragment 별로 하위 디렉토리를 구성할 수도 있다.

#### shared 
App 전반에 걸쳐 공통적으로 사용될 수 있는 UI를 정의.

#### lib 
UI요소와는 거의 관계없는 독립된 로직을 정의.

### Resource (res)
Android 개발환경에서 자체적으로 정의된 구조를 따라야 함으로 특별히 추가할 내용은 없다. 다만, 리소스 디렉토리 내에서 서브 디렉토리 구조를 가지지 못하므로 대체로 xml 파일이름의 접두어로 성격을 구분지어야 할 필요가 있다.

#### 리소스 파일명 명명 규칙
단어 사이의 구분은 낙타법이 아닌 밑줄(\_)로 연결한다. 또한 전부 소문자로 쓴다.

##### layout

| 접두어 | 설명 |
| ---- | --- |
| activity_ | Activity |
| fragment_ | Fragment |
| adapter_ | Adapter |
| sub_ | Layout 내에서 include 태그로 불려들여지는 서브 레이아웃. |

#### drawable / mipmap

| 접두어 | 설명 |
| ---- | --- |
| ic_ | Icon |
| dw_ | XML로 정의되는 Drawable (백터 도형 등) |

이미지 파일 등을 import한 경우, 가능한 파일명을 그대로 남겨두지만 단어는 밑줄(\_)로 구분하고, 전부 소문자가 되도록 한다.

#### values
Android에서 정의하는 기본 파일명은 다음과 같다.
- colors.xml
- strings.xml
- styles.xml

가능하면 기본 파일에서 정의하되, 분리가 필요한 경우는, 기본 파일명에 밑줄(\_)을 붙여서 쓴다. <br />
예) colors_darktheme.xml

#### 리소스 ID 명명 규칙
ID는 낙타법 표기를 원칙으로 한다.

##### UI 컨트롤
접두어를 붙이고 낙타법으로 표기. 예) @+id/textUserName

| 접두어 | UI 컨트롤 |
| ---- | -------- |
| layout | LinearLayout, FrameLayout 등 |
| scroll | ScrollView |
| list | ListView, RecyclerView 등 |
| text | TextView |
| edit | EditText |
| btn | Button, ImageButton, 그외 버튼 용도로 사용되는 뷰 |
| chk | CheckBox, CheckedTextView |
| radio | RadioButton |
| rg | RadioGroup |
| switch | Switch |
| spinner | Spinner |
| prog | ProgressView |
| web | WebView |
| img | ImageView |
| calendar | CalendarView |
| datePic | DatePicker |
| timePic | TimePicker |
| view | View |

그외 UI 요소들은 클래스 이름의 앞 단어(중복되는 경우 그 다음 단어까지)부분을 소문자로 해서 접두어로 쓴다.

### 소스 파일 내 명명 규칙
대원칙
* 영어 표기가 원칙이다. (한국어, 일본어 발음을 그대로 옮기지 않는다)
* 타입명(Class, Enum 등)은 대문자로 시작한다.
* 그외는 전부 소문자로 시작한다.
* 낙타법 표기를 원칙으로 한다.

Class
* 기본적으로 클래스와 소스 파일은 1:1로 대응한다. 따라서 파일명도 클래스명과 동일해야 한다.

Method
* 동사로 시작한다.

## 시스템 정보

```java5
StringBuffer buf = new StringBuffer();

buf.append("VERSION.RELEASE {"+Build.VERSION.RELEASE+"}");
buf.append("\\nVERSION.INCREMENTAL {"+Build.VERSION.INCREMENTAL+"}");
buf.append("\\nVERSION.SDK {"+Build.VERSION.SDK+"}");
buf.append("\\nBOARD {"+Build.BOARD+"}");
buf.append("\\nBRAND {"+Build.BRAND+"}");
buf.append("\\nDEVICE {"+Build.DEVICE+"}");
buf.append("\\nFINGERPRINT {"+Build.FINGERPRINT+"}");
buf.append("\\nHOST {"+Build.HOST+"}");
buf.append("\\nID {"+Build.ID+"}");

Log.d("build",buf.toString()); 
```

### App 정보

```java5
// App Package Name
final String app_id = getApplicationContext().getPackageName();

// App Version.
PackageInfo pInfo = getPackageManager().getPackageInfo(getPackageName(), 0);
String version = pInfo.versionName;
```

## UI

[Android UI 개발](android_ui_dev.md) 참조.

## IO
### Preference

```java5
// Context의 getSharedPreferences()로 객체를 가져온다.
// 사용할 수 있는 MODE는 다음과 같다.
// MODE_PRIVATE = 他のアプリからアクセス不可
// MODE_WPRLD_READABLE = 他のアプリから読み込み可能
// MODE_WORLD_WRITEABLE = 他のアプリから書き込み可能
SharedPreferences pref = getSharedPreferences("ファイル名", MODE_PRIVATE);

// 값 가져오기
pref.getString("key", "default value");

// 값 쓰기
SharedPreferences.Editor editor = pref.edit();
editor.putString("key", "value");

editor.commit(); // 이전엔 commit()을 사용했지만,
editor.apply(); // 요즘엔 apply()가 추천된다. apply는 commit과 같지만 비동기로 처리됨.
```

#### 다른 앱에서 Shared Preference 가져오기

```java5
Context otherContext = createPackage("com.example.pref", mode);
settings = otherContext.getSharedPreferences("Name", mode);
```

### File
#### 파일이 존재하는지 확인

```java5
File file = this.getFileStreamPath("test.txt");  
boolean isExists = file.exists();  
```

#### 디렉토리 만들기

```java5
File dir = new File(context.getExternalFilesDirs()  + "/Test/Test2");
if(!dir.exists()){
  dir.mkdirs();
}
```

#### 파일 복사

```java5
    private boolean copyFile(File file , String save_file){
        boolean result;
        if (file != null && file.exists()) {
            try {
                FileInputStream fis = new FileInputStream(file);
                FileOutputStream newfos = new FileOutputStream(save_file);
                int readcount=0;
                byte[] buffer = new byte[1024];
                while ((readcount = fis.read(buffer,0,1024)) != -1) {
                    newfos.write(buffer,0,readcount);
                }
                newfos.close();
                fis.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
            result = true;
        } else {
            result = false;
        }
        return result;
    }
```

#### 텍스트 파일 읽기

```java5
public static String readAllText(InputStream in, String encoding) {
        if (encoding == null || encoding.length() < 1) {
            encoding = "UTF-8";
        }

        try {
            final InputStreamReader inReader = new InputStreamReader(in, encoding);
            final BufferedReader bufReader = new BufferedReader(inReader);

            final StringBuilder result = new StringBuilder();
            String line;
            // 1行ずつテキストを読み込む
            while ((line = bufReader.readLine()) != null) {
                result.append(line);
            }

            bufReader.close();
            inReader.close();
            in.close();

            return result.toString();
        }
        catch (UnsupportedEncodingException e) {
            e.printStackTrace();
            return null;
        }
        catch (IOException e) {
            e.printStackTrace();
            return null;
        }
}
```

#### App에서 생성한 디렉토리/파일이 PC에서 안보일 때
Android OS에서 인덱싱이 되지 않았기 때문. MediaScannerConnection.scanFile() 메서드를 호출해서 인덱싱을 요청해야 한다.

파일 하나만 간단히 처리하고자 할 때는 다음과 같이 Intent를 이용하는 방법도 있다. 단, 이 방식은 스캔 완료 이벤트를 받지 않는다.

```kotlin
val contentUri = Uri.fromFile(File(filepath))
val mediaScanIntent = Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, contentUri)
context.sendBroadcast(mediaScanIntent)
```

### SQLite
#### DB 초기화/Open

```java5
public class LocalDbHelper extends SQLiteOpenHelper {
    private static final String DATABASE_NAME = "test.db";
    private static final int DATABASE_VERSION = 1;

    public LocalDbHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // create DB tables.
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```
```java5
dbHelper = new LocalDbHelper(context);

// open
SQLiteDatabase db = dbHelper.getWritableDatabase();

// close
if (db != null) db.close();
if (dbHelper != null) dbHelper.close();
```

#### Data Types

| type | 説明 |
| ---- | --- |
|NULL | NULL値 |
|INTEGER | 符号付整数。1, 2, 3, 4, 6, or 8 バイトで格納。날짜 등의 정보도 숫자로 변환해서 처리해야 한다. |
|REAL  | 浮動小数点数。8バイトで格納 |
|TEXT  | テキスト。UTF-8, UTF-16BE or UTF-16-LEのいずれかで格納 |
|BLOB | Binary Large OBject。入力データをそのまま格納 |

#### Create Table

```java5
db.execSQL("CREATE TABLE notice( " +
                "id INTEGER " +
                "title TEXT, " +
                "contents_data TEXT, " +
                "pub_date INTEGER, " +
                "PRIMARY KEY(date DESC, id ASC)" +
                ");");
```

#### Query

```java5
Cursor cursor = db.query("DB_TABLE_NAME", new String[] { cols, ... },
                "Selection Query", new String[] { query_params, ... }, "groupBy", "having", "orderBy", "limit");

while (cursor.moveToNext()) {
// TODO ...
}

cursor.close();
```

#### Insert/Replace
삽입 하면서, 이미 데이터가 존재하는 경우는 대체(Replace, ※주:  Update가 아니다)를 수행한다.
```java5
SQLiteDatabase db = dbHelper.getWritableDatabase();

ContentValues values = new ContentValues();
values.put("column1", 0);
values.put("column2", "string");
values.put("column3", 0.0);

try {
      db.insertWithOnConflict("TABLE_NAME", null, values, SQLiteDatabase.CONFLICT_REPLACE);
}
catch (IllegalStateException e) {
    // DB is already closed.
}
```

### Sound
#### MediaPlayer

```java5
public static void play(int sndResID, float speed) {
  MediaPlayer mp = MediaPlayer.create(context, sndResID);
  play(mp, speed);
}

public static void play(String uri, float speed) {
  MediaPlayer mp = MediaPlayer.create(context, Uri.parse(uri));
  play(mp, speed);
}

private static void play(MediaPlayer mp, float speed) {
  if (Build.VERSION.SDK_INT >= 23 /* Android 6.0 */) {
    PlaybackParams params = mp.getPlaybackParams();
    params.setSpeed(speed);

    mp.setPlaybackParams(params);
  }

  mp.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
    @Override
    public void onCompletion(MediaPlayer mp) {
      mp.release();
    }
  });
  mp.start();
}
```

#### Sound Pool
##### 초기화

```java5
SoundPool sndPool;

if (Build.VERSION.SDK_INT < 21 /* Android 5.0 */) {
    sndPool = new SoundPool(MAX_STREAMS, AudioManager.STREAM_MUSIC, 0);
}
else {
    sndPool = new SoundPool.Builder()
            .setMaxStreams(10)
            .build();
}
```

##### Load

```java5
sndPool.setOnLoadCompleteListener(new SoundPool.OnLoadCompleteListener() {
  @Override
  public void onLoadComplete(SoundPool soundPool, int soundId, int status) {
    // TODO
  }
});

int soundID = sndPool.load(audioPath, 1 /* priority */);

//--- or ---//

int res_id = 00000; // Resource ID
int soundID = sndPool.load(res_id, 1 /* priority */);
```

##### Play

```java5
boolean loop = false;
sndPool.play(sound_id, 1 /* Left Volume */, 1 /* Right Volume */, 1 /* priority */, loop ? -1 : 0, 1f /* play rate: 0.5f ~ 2.0f */);
```

### MediaRecorder
#### .m4a 포맷으로 녹음

```kotlin
val voiceRecorder = MediaRecorder().apply {
    setAudioSource(MediaRecorder.AudioSource.MIC)
    setOutputFormat(MediaRecorder.OutputFormat.MPEG_4) // 여기랑,
    setAudioEncoder(MediaRecorder.AudioEncoder.AAC) // 여기가 중요!
    setAudioEncodingBitRate(256000)
    setAudioSamplingRate(16000)
    setOutputFile(file)

    try {
        prepare()
        start()
    }
    catch (e: Exception) {
        Log.e(TAG, "エラー：$e")
    }
}
```

#### setNextOutputFile() 사용법

```kotlin
val file: File // 현재 녹화중인 파일
val next: File // 다음에 녹화할 파일

val voiceRecorder = MediaRecorder().apply {
    setAudioSource(MediaRecorder.AudioSource.MIC)
    setOutputFormat(MediaRecorder.OutputFormat.MPEG_4)
    setAudioEncoder(MediaRecorder.AudioEncoder.AAC)
    setAudioEncodingBitRate(256000)
    setAudioSamplingRate(16000)
    setOutputFile(file)
    setMaxFileSize(getMaxRecordFileSize())
    setOnInfoListener { mr, what, extra ->
        when (what) {
            MEDIA_RECORDER_INFO_MAX_FILESIZE_APPROACHING -> {
                Log.d(TAG, "MEDIA_RECORDER_INFO_MAX_FILESIZE_APPROACHING")
                mr.setNextOutputFile(next) // 이 타이밍에 NextOutputFile을 지정
            }
            MEDIA_RECORDER_INFO_MAX_FILESIZE_REACHED -> {
                Log.d(TAG, "MEDIA_RECORDER_INFO_MAX_FILESIZE_REACHED")
            }
            MEDIA_RECORDER_INFO_NEXT_OUTPUT_FILE_STARTED -> {
                Log.d(TAG, "MEDIA_RECORDER_INFO_NEXT_OUTPUT_FILE_STARTED")
            }
        }
    }

    try {
        prepare()
        start()
    }
    catch (e: Exception) {
        Log.e(TAG, "エラー：$e")
    }
}
```

setNextOutputFile()은, setMaxFileSize()로 지정한 크기가 다 차면, 자동으로 다음 파일로 넘어가는 기능이다.

주의점:
- setNextOutputFile()로 저정되는 파일은 반드시 Seek로 파일내 임의접근이 가능해야 한다.
- setNextOutputFile()로 지정된 파일은 빈 파일로 초기화가 이루어진다.
  - 이미 기록된 파일을 setNextOutputFile()로 지정하면 기존 내용이 사라진다. 
- setNextOutputFile()은 prepare() 이후에 지정되어야 한는데, 정확한 타이밍은 위의 코드를 참조.


2개 파일로 로테이션을 돌리고자 할 때는, MEDIA_RECORDER_INFO_MAX_FILESIZE_APPROACHING 시점에서 직전에 녹화된 파일을 백업한 다음에 setNextOutputFile을 지정하면 된다.

### JSON

```javascript
{
"sys":
   {
      "country":"GB",
      "sunrise":1381107633,
      "sunset":1381149604
   },
"weather":[
   {
      "id":711,
      "main":"Smoke",
      "description":"smoke",
      "icon":"50n"
   }
],
"main":
   {
      "temp":304.15,
      "pressure":1009,
   }
}
```

```java
String in;
JSONObject reader = new JSONObject(in);

JSONObject sys  = reader.getJSONObject("sys");
country = sys.getString("country");
      
JSONObject main  = reader.getJSONObject("main");
temperature = main.getString("temp");

JSONArray weather = reader.getJSONArray("weather");
for (int i = 0; i < weather.length(); i++) {
      JSONObject item = weather.getJSONObject(i);
}

// JSON to String
reader.toString();
```

### Speech To Text

http://www.androidhive.info/2014/07/android-speech-to-text-tutorial/

Sample:
```java5
package unity.sizuha.util;

import android.app.Activity;
import android.content.ActivityNotFoundException;
import android.content.Intent;
import android.speech.RecognizerIntent;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;
import android.widget.Toast;

import com.unity3d.player.UnityPlayer;

import java.util.ArrayList;

public class ApiActivity extends Activity {

    public final static int API_NONE = 0;
    public final static int API_SPEECH_TO_TEXT = 1000;

    private int api_code;
    private String message;
    private String receiveObj, receiveFunc;

    public static String dummy() {
        return "";
    }


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        //Remove title bar
        this.requestWindowFeature(Window.FEATURE_NO_TITLE);

        //Remove notification bar
        this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);

        super.onCreate(savedInstanceState);
        final Intent intent = getIntent();

        api_code = intent.getIntExtra("api", API_NONE);
        message = intent.getStringExtra("message");
        receiveObj = intent.getStringExtra("recv_obj");
        receiveFunc = intent.getStringExtra("recv_func");
    }

    @Override
    protected void onStart() {
        super.onStart();

        switch (api_code) {
            case API_SPEECH_TO_TEXT:
                startSpeechToText();
                break;

            default:
                finish();
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode) {
            case API_SPEECH_TO_TEXT:
                if (resultCode == RESULT_OK && null != data) {
                    ArrayList<String> result = data.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS);
                    UnityPlayer.UnitySendMessage(receiveObj, receiveFunc, result.get(0));
                }
                else {
                    UnityPlayer.UnitySendMessage(receiveObj, receiveFunc, "");
                }
                break;
        }

        finish();
    }


    protected void startSpeechToText() {
        final Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, "en-US");
        intent.putExtra(RecognizerIntent.EXTRA_PROMPT, message);

        try {
            startActivityForResult(intent, API_SPEECH_TO_TEXT);
        }
        catch (ActivityNotFoundException e) {
            Toast.makeText(getApplicationContext(),
                    e.getMessage(),
                    Toast.LENGTH_SHORT).show();
        }
    }

}
```


### Stream 
#### Stream Copy

```java5
    public static void copyStream(InputStream is, OutputStream os) {
        final int buffer_size = 1024;
        try {
            byte[] bytes = new byte[buffer_size];
            for (;;) {
                int count = is.read(bytes, 0, buffer_size);
                if (count == -1)
                    break;
                os.write(bytes, 0, count);
            }
        }
        catch (Exception ex) {}
    }
```

### Print (PDF)

https://developer.android.com/training/printing/html-docs

```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_report_view)

        supportActionBar?.setDisplayHomeAsUpEnabled(true)

        web_view.run {
            webViewClient = object : WebViewClient() {
                override fun shouldOverrideUrlLoading(view: WebView, request: WebResourceRequest): Boolean {
                    return false
                }
                override fun onPageFinished(view: WebView, url: String) {

                }
            }

            val htmlDocument = "<html><body><h1>Test Content</h1><p>Testing, " + "testing, testing...</p></body></html>"
            loadDataWithBaseURL(null, htmlDocument, "text/HTML", "UTF-8", null)

            //loadUrl("http://developer.android.com/about/index.html")
        }
    }

    override fun onOptionsItemSelected(item: MenuItem?): Boolean {
        when (item?.itemId) {
            android.R.id.home -> {
                finish()
                return true
            }
            R.id.action_print -> {
                createWebPrintJob()
                return true
            }
        }

        return super.onOptionsItemSelected(item)
    }

    override fun onPrepareOptionsMenu(menu: Menu?): Boolean {
        menuInflater?.inflate(R.menu.report_view_actions, menu)
        return super.onPrepareOptionsMenu(menu)
    }

    private fun createWebPrintJob() {
        // Get a PrintManager instance
        val printManager = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            getSystemService(PrintManager::class.java)
        }
        else {
            getSystemService(Context.PRINT_SERVICE) as PrintManager
        }

        val jobName = getString(R.string.app_name) + " Document"

        // Get a print adapter instance
        val printAdapter = web_view.createPrintDocumentAdapter(jobName)

        // Create a print job with name and adapter instance
        printManager.print(jobName, printAdapter, PrintAttributes.Builder().build())
    }
```

### Uri
#### File 경로를 Uri로 변환

```java5
    public static Uri getContentUri(Context c, String path) {
        final File file = new File(path);
        Logger.d("target file: " + file.getAbsolutePath());

        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.N) {
            return Uri.fromFile(file);
        }

        final String authority = c.getApplicationContext().getPackageName() + ".provider";
        return FileProvider.getUriForFile(c, authority, file);
    }
```

AndroidManifest.xml에서, application 태그 안에 다음 내용을 추가한다.
```xml
        <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="${applicationId}.provider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/filepaths" />
        </provider>
```

그리고 res/xml/filepaths.xml 파일 내용은 다음과 같다.
```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <root-path name="root" path="/" />
    <external-path name="external_files" path="."/>
</paths>
```

#### Uri로 부터 File 경로 구하기

```java5
    /**
     * Get a file path from a Uri. This will get the the path for Storage Access
     * Framework Documents, as well as the _data field for the MediaStore and
     * other file-based ContentProviders.
     *
     * @param context The context.
     * @param uri The Uri to query.
     * @author paulburke
     */
    public static String getPath(final Context context, final Uri uri) {

        final boolean isKitKat = Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT;

        // DocumentProvider
        if (isKitKat && DocumentsContract.isDocumentUri(context, uri)) {
            // ExternalStorageProvider
            if (isExternalStorageDocument(uri)) {
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];

                if ("primary".equalsIgnoreCase(type)) {
                    return Environment.getExternalStorageDirectory() + "/" + split[1];
                }

                // TODO handle non-primary volumes
            }
            // DownloadsProvider
            else if (isDownloadsDocument(uri)) {

                final String id = DocumentsContract.getDocumentId(uri);
                final Uri contentUri = ContentUris.withAppendedId(
                        Uri.parse("content://downloads/public_downloads"), Long.valueOf(id));

                return getDataColumn(context, contentUri, null, null);
            }
            // MediaProvider
            else if (isMediaDocument(uri)) {
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];

                Uri contentUri = null;
                if ("image".equals(type)) {
                    contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
                } else if ("video".equals(type)) {
                    contentUri = MediaStore.Video.Media.EXTERNAL_CONTENT_URI;
                } else if ("audio".equals(type)) {
                    contentUri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
                }

                final String selection = "_id=?";
                final String[] selectionArgs = new String[] {
                        split[1]
                };

                return getDataColumn(context, contentUri, selection, selectionArgs);
            }
        }
        // MediaStore (and general)
        else if ("content".equalsIgnoreCase(uri.getScheme())) {
            return getDataColumn(context, uri, null, null);
        }
        // File
        else if ("file".equalsIgnoreCase(uri.getScheme())) {
            return uri.getPath();
        }

        return null;
    }

    /**
     * Get the value of the data column for this Uri. This is useful for
     * MediaStore Uris, and other file-based ContentProviders.
     *
     * @param context The context.
     * @param uri The Uri to query.
     * @param selection (Optional) Filter used in the query.
     * @param selectionArgs (Optional) Selection arguments used in the query.
     * @return The value of the _data column, which is typically a file path.
     */
    public static String getDataColumn(Context context, Uri uri, String selection,
                                       String[] selectionArgs) {

        Cursor cursor = null;
        final String column = "_data";
        final String[] projection = {
                column
        };

        try {
            cursor = context.getContentResolver().query(uri, projection, selection, selectionArgs,
                    null);
            if (cursor != null && cursor.moveToFirst()) {
                final int column_index = cursor.getColumnIndexOrThrow(column);
                return cursor.getString(column_index);
            }
        } finally {
            if (cursor != null)
                cursor.close();
        }
        return null;
    }


    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is ExternalStorageProvider.
     */
    public static boolean isExternalStorageDocument(Uri uri) {
        return "com.android.externalstorage.documents".equals(uri.getAuthority());
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is DownloadsProvider.
     */
    public static boolean isDownloadsDocument(Uri uri) {
        return "com.android.providers.downloads.documents".equals(uri.getAuthority());
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is MediaProvider.
     */
    public static boolean isMediaDocument(Uri uri) {
        return "com.android.providers.media.documents".equals(uri.getAuthority());
    }

    public static boolean isJpegFile(String filePath) {
        final String src = filePath.toLowerCase();
        return src.endsWith(".jpg") || src.endsWith(".jpeg");
    }
```

## 통신

### 네트워크가 사용 가능한지 확인

```java5
    // ネットワークの接続を確認
    public static boolean networkCheck(Context context){
        final ConnectivityManager cm =  (ConnectivityManager)context.getSystemService(Context.CONNECTIVITY_SERVICE);
        if (cm == null) return false;

        final NetworkInfo info = cm.getActiveNetworkInfo();
        if (info != null ) {
            return info.isConnected();
        } 
        else {
            return false;
        }
    }
```

### 전화번호 가져오기

```java5
// <uses-permission android:name="android.permission.READ_PHONE_STATE" />
TelephonyManager tMgr = (TelephonyManager)getSystemService(Context.TELEPHONY_SERVICE);
String mPhoneNumber = tMgr.getLine1Number();
```

### HTTP

네트워크 통신을 메인 스레드에서 처리하면 런타임에 android.os.NetworkOnMainThreadException 예외가 발생한다. 네트워크 통신은 반드시 AsyncTask 등을 이용해서 비동기로 처리해야 한다.

#### Apache HTTP Client
Android 6에서 Apache HTTP Client 라이브러리가 제거되었다. 대신 [https://developer.android.com/reference/java/net/HttpURLConnection.html| HttpURLConnection] 을 권장하고 있다.

그래도 계속 사용하고 싶다면, App/build.gradle 파일의 첫 부분을 다음과 같이 수정한다.
```
apply plugin: 'com.android.application'

android {
    useLibrary 'org.apache.http.legacy'

...
```

#### HttpURLConnection

```java5
public class NetUtil {

    public interface IOnHttpTaskEvent {
        void onResponse(Response response);
    }

    public static class Response {
        public boolean succeed;
        public String error;
        public String content;
        private JSONObject json = null;

        public JSONObject getJSONObject() {
            if (json != null) return json;

            if (content != null) {
                try {
                    json = new JSONObject(content);
                    return json;
                }
                catch (JSONException e) {
                    error = e.toString();
                    succeed = false;
                }
            }

            return json = new JSONObject();
        }
    }

    public static Response http_get(String url, Map<String,String> params) {
        return http_request(url, params, RequestMethod.GET);
    }

    public static Response http_post(String url, Map<String,String> params) {
        return http_request(url, params, RequestMethod.POST);
    }

    public static Response http_post(String url, String post_params) {
        return http_request_raw(url, post_params, RequestMethod.POST);
    }

    public static Response http_post(String url, JSONObject json) {
        return http_request_raw(url, json.toString(), RequestMethod.POST);
    }

    public enum RequestMethod {
        GET(0),
        POST(1),
        DELETE(2);

        int method;

        RequestMethod(int method_code) {
            method = method_code;
        }

        public String toString() {
            switch (method) {
                case 1: return "POST";
                case 2: return "DELETE";
                default: return "GET";
            }
        }

        public boolean is_post() {
            return method == 1;
        }

    }

    public static String mapToString(Map<String,String> params) {
        if (params == null) return null;

        String param_data = "";
        boolean first = true;

        for (Map.Entry<String,String> kv: params.entrySet()) {
            try {
                param_data += (first ? "" : "&") +
                        URLEncoder.encode(kv.getKey(), "UTF-8") + "=" + URLEncoder.encode(kv.getValue(), "UTF-8");

                first = false;
            }
            catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }

        return param_data;
    }


    public static Response http_request(String url, Map<String,String> params, RequestMethod request_method) {
        return http_request_raw(url, mapToString(params), request_method);
    }

    protected static Response http_request_raw(String url, String param_str, RequestMethod request_method) {
        final Response response = new Response();
        HttpURLConnection urlConn = null;

        try {
            URL _url;

            if (request_method.is_post()) {
                _url = new URL(url);
            }
            else if (param_str != null && param_str.length() > 0) {
                _url = new URL(url + "?" + param_str);
            }
            else {
                _url = new URL(url);
            }

            // for DEBUG
            Logger.d("http_request_raw[URL]: " + _url.toString());

            urlConn = (HttpURLConnection) _url.openConnection();
            urlConn.setReadTimeout(60000);
            urlConn.setConnectTimeout(60000);
            urlConn.setDoInput(true);
            urlConn.setDoOutput(request_method.is_post());
            urlConn.setRequestMethod(request_method.toString());

            if (param_str != null && param_str.length() > 0 && request_method.is_post()) {
                final OutputStream os = urlConn.getOutputStream();
                BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(os, "UTF-8"));
                writer.write(param_str);
                writer.flush();
                writer.close();
                os.close();
            }

            urlConn.connect();

            // HTTPレスポンスコード
            final int status = urlConn.getResponseCode();
            response.succeed = status == HttpURLConnection.HTTP_OK;

            if (response.succeed) {
                // 取得したテキストを格納する変数
                final StringBuilder result = new StringBuilder();

                // 通信に成功した
                // テキストを取得する
                final InputStream in = urlConn.getInputStream();

                String encoding = urlConn.getContentEncoding();
                Logger.d("encoding: " + encoding);
                if (encoding == null || encoding.length() < 1) {
                    encoding = "UTF-8";
                }

                final InputStreamReader inReader = new InputStreamReader(in, encoding);
                final BufferedReader bufReader = new BufferedReader(inReader);

                String line;
                // 1行ずつテキストを読み込む
                while ((line = bufReader.readLine()) != null) {
                    result.append(line);
                }

                bufReader.close();
                inReader.close();
                in.close();

                response.content = result.toString();

                // for DEBUG
                Logger.d("http_request_raw[response.content]: " + response.content);
            }
            else {
                response.error = String.valueOf(status);

                // for DEBUG
                Logger.d("http_request_raw[response.error]: " + response.error);
            }
        }
        catch (MalformedURLException e) {
            e.printStackTrace();
            response.succeed = false;
            response.error = e.toString();
        }
        catch (IOException e) {
            e.printStackTrace();
            response.succeed = false;
            response.error = e.toString();
        }
        finally {
            if (urlConn != null) urlConn.disconnect();
        }

        return response;
    }


    public static class HttpAsyncTask extends AsyncTask<String, Void, Response> {
        private String request_params;
        private IOnHttpTaskEvent eventRecv;
        private RequestMethod method = RequestMethod.GET;

        public HttpAsyncTask setMethod(RequestMethod request_method) {
            method = request_method;
            return this;
        }

        public HttpAsyncTask setParams(Map<String,String> params) {
            request_params = mapToString(params);
            return this;
        }

        public HttpAsyncTask setParams(JSONObject params) {
            request_params = params.toString();
            method = RequestMethod.POST;
            return this;
        }

        public HttpAsyncTask setListener(IOnHttpTaskEvent listener) {
            eventRecv = listener;
            return this;
        }


        @Override
        protected Response doInBackground(String... params) {
            String url = null;
            for (String p: params) {
                url = p;
                break;
            }

            Logger.d("request params: " + request_params);
            return http_request_raw(url, request_params, method);
        }

        @Override
        protected void onPostExecute(Response response) {
            if (eventRecv != null)
                eventRecv.onResponse(response);
        }
    }

}
```

#### 파일 내려받기

```java5
public class FileDownloader extends AsyncTask<String, String, Integer> {

    final static int BUFFER_SIZE = 4096;

    private String storagePath = "";
    public void setStagePath(String path) {
        storagePath = path;
    }

    public static interface IOnDownloadEvent {
        void onOneFileDownloadEnd(String filenameWithPath);
        void onDownloadTotalEnd(int download_count);
    }

    public IOnDownloadEvent downloadEventRecv;

    public boolean overwrite = false;


    @Override
    protected Integer doInBackground(String... urls) {
        int dn_cnt = 0;

        for (String url_str : urls) {
            // Escape early if cancel() is called
            if (isCancelled()) break;

            File outFile = null;

            try {
                URL url = new URL(url_str);

                final String out_filename = URLUtil.guessFileName(url_str, null, null);
                final String out_filenameWithPath = Environment.getExternalStorageDirectory().toString() +
                        "/" + storagePath + "/" + out_filename;

                outFile = new File(out_filenameWithPath);
                if (!overwrite &&outFile.exists()) continue;

                URLConnection connection = url.openConnection();
                connection.connect();

                outFile.mkdirs();

                InputStream input = new BufferedInputStream(url.openStream(), BUFFER_SIZE);
                OutputStream output = new FileOutputStream(outFile);

                byte data[] = new byte[BUFFER_SIZE];

                int read_count;
                while ((read_count = input.read(data)) != -1) {
                    output.write(data, 0, read_count);
                    dn_cnt++;
                }

                output.flush();
                output.close();
                input.close();

                if (downloadEventRecv != null) downloadEventRecv.onOneFileDownloadEnd(out_filenameWithPath);
            }
            catch (MalformedURLException e) {
                e.printStackTrace();
                continue;
            }
            catch (IOException e) {
                e.printStackTrace();
                if (outFile != null) outFile.delete();

                continue;
            }
        }

        return dn_cnt;
    }

    @Override
    protected void onPostExecute(Integer dnCnt) {
        if (downloadEventRecv != null) downloadEventRecv.onDownloadTotalEnd(dnCnt);

        super.onPostExecute(dnCnt);
    }
}
```

##### 다운 받기 전에 파일 크기 알아내기

```java5
URL url = new URL("http://server.com/file.mp3");
URLConnection urlConnection = url.openConnection();
urlConnection.connect();
int file_size = urlConnection.getContentLength();
```

#### multipart/form-data

http://qiita.com/informationsea/items/778d9525c3aaded73577

```java5
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.HttpURLConnection;
import java.net.ProtocolException;
import java.util.Map;
import java.util.UUID;

/**
 * Copyright (C) 2014 Yasunobu OKAMURA
 * MIT License
 */
public class HttpMultipartSender {
    private HttpMultipartSender(){} // cannot make instance

    // this function is implemented based on http://www.androidsnippets.com/multipart-http-requests
    public static void sendMultipart(HttpURLConnection connection, String filefield, File filepath, Map<String, String> textdata) throws IOException {

        final String twoHyphens = "--";
        final String boundary =  "*****"+ UUID.randomUUID().toString()+"*****";
        final String lineEnd = "\r\n";
        final int maxBufferSize = 1024*1024*3;

        DataOutputStream outputStream;

        connection.setDoInput(true);
        connection.setDoOutput(true);
        connection.setUseCaches(false);

        connection.setRequestMethod("POST");
        connection.setRequestProperty("Connection", "Keep-Alive");
        connection.setRequestProperty("Content-Type", "multipart/form-data; boundary="+boundary);

        outputStream = new DataOutputStream(connection.getOutputStream());
        outputStream.writeBytes(twoHyphens + boundary + lineEnd);
        outputStream.writeBytes("Content-Disposition: form-data; name=\"" + filefield + "\"; filename=\"" + filepath.getName() +"\"" + lineEnd);
        outputStream.writeBytes("Content-Type: application/octet-stream" + lineEnd);
        outputStream.writeBytes("Content-Transfer-Encoding: binary" + lineEnd);
        outputStream.writeBytes(lineEnd);

        FileInputStream fileInputStream = new FileInputStream(filepath);
        int bytesAvailable = fileInputStream.available();
        int bufferSize = Math.min(bytesAvailable, maxBufferSize);
        byte[] buffer = new byte[bufferSize];

        int bytesRead = fileInputStream.read(buffer, 0, bufferSize);
        while(bytesRead > 0) {
            outputStream.write(buffer, 0, bufferSize);
            bytesAvailable = fileInputStream.available();
            bufferSize = Math.min(bytesAvailable, maxBufferSize);
            bytesRead = fileInputStream.read(buffer, 0, bufferSize);
        }

        outputStream.writeBytes(lineEnd);

        for (Map.Entry<String, String> entry : textdata.entrySet()) {
            outputStream.writeBytes(twoHyphens + boundary + lineEnd);
            outputStream.writeBytes("Content-Disposition: form-data; name=\"" + entry.getKey() + "\"" + lineEnd);
            outputStream.writeBytes("Content-Type: text/plain"+lineEnd);
            outputStream.writeBytes(lineEnd);
            outputStream.writeBytes(entry.getValue());
            outputStream.writeBytes(lineEnd);
        }

        outputStream.writeBytes(twoHyphens + boundary + twoHyphens + lineEnd);

        outputStream.close();
    }
}
```

### SMS
필요한 퍼미션
```xml
<uses-permission android:name="android.permission.SEND_SMS" />
```

#### SmsManager API

```java5
SmsManager smsManager = SmsManager.getDefault();
smsManager.sendTextMessage("phoneNo", null, "sms message", null, null);
```

#### Built-in SMS application

```java5
Intent sendIntent = new Intent(Intent.ACTION_VIEW);
sendIntent.putExtra("sms_body", "--- SMS 내용 ---"); 
sendIntent.setType("vnd.android-dir/mms-sms");
startActivity(sendIntent);
```

### Email

```java5
protected void sendEmail() {
      Log.i("Send email", "");
      String[] TO = {""};
      String[] CC = {""};
      Intent emailIntent = new Intent(Intent.ACTION_SEND);
      
      emailIntent.setData(Uri.parse("mailto:"));
      emailIntent.setType("text/plain");
      emailIntent.putExtra(Intent.EXTRA_EMAIL, TO);
      emailIntent.putExtra(Intent.EXTRA_CC, CC);
      emailIntent.putExtra(Intent.EXTRA_SUBJECT, "Your subject");
      emailIntent.putExtra(Intent.EXTRA_TEXT, "Email message goes here");
      
      try {
         startActivity(Intent.createChooser(emailIntent, "Send mail..."));
         finish();
         Log.i("Finished sending email...", "");
      }
      catch (android.content.ActivityNotFoundException ex) {
         Toast.makeText(MainActivity.this, "There is no email client installed.", Toast.LENGTH_SHORT).show();
      }
}
```

### URL Scheme
일단은 다음과 같은 intent 스킴을 이용하는 방법을 권장하고 있다.
```
   intent:
   HOST/URI-path // Optional host 
   #Intent; 
      package=[string]; 
      action=[string]; 
      category=[string]; 
      component=[string]; 
      scheme=[string]; 
   end; 
```

Example:
```xml
   <a href="intent://scan/#Intent;scheme=zxing;package=com.google.zxing.client.android;S.browser_fallback_url=http%3A%2F%2Fzxing.org;end"> Take a QR code </a>
```

간단히 iOS처럼 App Scheme 이름을 그대로 활용할 수도 있다.
```java5
    public static void openIntentByUrl(Context c, String url) {
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.addCategory(Intent.CATEGORY_DEFAULT);
        intent.addCategory(Intent.CATEGORY_BROWSABLE);
        intent.setData(Uri.parse(url)); // ex) "bill2://test2?r=received"
        c.startActivity(intent);
    }
```

## 위치정보
### Google Play Location Service

## Date/Time
### 현재 날짜/시간 가져오기

```java
Calendar calendar = Calendar.getInstance();
```

### 날짜/시간 셋팅

```java5
Calendar calendar = Calendar.getInstance();
calendar.clear(); // 중요: set 하기 전에 반드시 clear를 해줄 것!
calendar.set(year, month, day, hour, min, sec);
long datetime = calendar.getTimeInMillis();
```

### Date/String 변한

```java5
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

public class DataUtil {
    private static SimpleDateFormat api_date_format = new SimpleDateFormat("yyyy-MM-DD");

    public static Date toDate(String date_str) throws ParseException {
        return api_date_format.parse(date_str);
    }

    public static String toString(Date date) {
        return api_date_format.format(date);
    }
}
```

#### SimpleDateFormat 패턴정의 예제

| Date and Time Pattern | Result |
| --------------------- | ------ | 
| "yyyy.MM.dd G 'at' HH:mm:ss z" | 2001.07.04 AD at 12:08:56 PDT |
| "EEE, MMM d, ''yy" | Wed, Jul 4, '01 |
| "h:mm a" | 12:08 PM |
| "hh 'o''clock' a, zzzz" | 12 o'clock PM, Pacific Daylight Time |
| "K:mm a, z" | 0:08 PM, PDT |
| "yyyyy.MMMMM.dd GGG hh:mm aaa"  | 02001.July.04 AD 12:08 PM |
| "EEE, d MMM yyyy HH:mm:ss Z" | Wed, 4 Jul 2001 12:08:56 -0700 |
| "yyMMddHHmmssZ" | 010704120856-0700 |
| "yyyy-MM-dd'T'HH:mm:ss.SSSZ" | 2001-07-04T12:08:56.235-0700 |
| "yyyy-MM-dd'T'HH:mm:ss.SSSXXX" | 2001-07-04T12:08:56.235-07:00 |
| "YYYY-'W'ww-u" | 2001-W27-3 |

TimeZone Format
* z: General time zone
* Z: RFC 822 time zone
* X:  ISO 8601 time zone

## Threading
### AsyncTask

http://developer.android.com/reference/android/os/AsyncTask.html

```java5
 private class DownloadFilesTask extends AsyncTask<URL, Integer, Long> {
     protected Long doInBackground(URL... urls) {
         int count = urls.length;
         long totalSize = 0;
         for (int i = 0; i < count; i++) {
             totalSize += Downloader.downloadFile(urls[i]);
             publishProgress((int) ((i / (float) count) * 100));
             // Escape early if cancel() is called
             if (isCancelled()) break;
         }
         return totalSize;
     }

     protected void onProgressUpdate(Integer... progress) {
         setProgressPercent(progress[0]);
     }

     protected void onPostExecute(Long result) {
         showDialog("Downloaded " + result + " bytes");
     }
 }
```

```java
new DownloadFilesTask().execute(url1, url2, url3);
```

#### AsyncTask's generic types
The three types used by an asynchronous task are the following:
1. Params, the type of the parameters sent to the task upon execution.
1. Progress, the type of the progress units published during the background computation.
1. Result, the type of the result of the background computation.

Not all types are always used by an asynchronous task. To mark a type as unused, simply use the type Void:
```java5
 private class MyTask extends AsyncTask<Void, Void, Void> { ... }
```

#### Events
When an asynchronous task is executed, the task goes through 4 steps:
1. onPreExecute()
1. doInBackground(Params...)
1. onProgressUpdate(Progress...)
1. onPostExecute(Result)

#### 주의할 점
* The AsyncTask class must be loaded on the UI thread. This is done automatically as of JELLY_BEAN.
* The task instance must be created on the UI thread.
* execute(Params...) must be invoked on the UI thread.
* Do not call onPreExecute(), onPostExecute(Result), doInBackground(Params...), onProgressUpdate(Progress...) manually.
* The task can be executed only once (an exception will be thrown if a second execution is attempted.)

## 암호화
### SHA1

```java
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class Sha1Hex {
    public String makeSHA1Hash(String input) throws NoSuchAlgorithmException {
            MessageDigest md = MessageDigest.getInstance("SHA1");
            md.reset();
            byte[] buffer = input.getBytes();
            md.update(buffer);
            byte[] digest = md.digest();

            String hexStr = "";
            for (int i = 0; i < digest.length; i++) {
                hexStr +=  Integer.toString( ( digest[i] & 0xff ) + 0x100, 16).substring( 1 );
            }
            return hexStr;
    }
}
```

## Reource
### 리소스를 참조해 코드에서 텍스트 크기 설정

```xml
<dimen name="text_medium">18sp</dimen>
```
Set the size in code:
```java
textView.setTextSize(TypedValue.COMPLEX_UNIT_PX, getResources().getDimension(R.dimen.text_medium));
```

### More Resource Types

http://developer.android.com/guide/topics/resources/more-resources.html

### Color

```java5
// getColor(int id) is deprecated now, this must be used :
ContextCompat.getColor(context, R.color.your_color);
```

## Cloud Messaging (Push)
FCM: [Firebase Cloud Messaging](/lib/fcm.md) 참조

## Alarm 구현

```java
/* MainActivity class */
    public void setAlram(String title, String message, int year, int month, int day, int hour, int min, int sec) {
        Intent alarmIntent = new Intent(this, AlarmReceiver.class);
        alarmIntent.putExtra("title", title);
        alarmIntent.putExtra("message", message);

        if (Config.DEBUG_MODE) {
            Log.i("PROJECT_D", "alarm: " + title + " | "+ message);
        }

        PendingIntent pendingIntent = PendingIntent.getBroadcast(this, 0, alarmIntent, PendingIntent.FLAG_UPDATE_CURRENT);

        AlarmManager alarmManager = (AlarmManager) getSystemService(ALARM_SERVICE);

        Calendar startTime = Calendar.getInstance();
        startTime.set(Calendar.YEAR, year);
        startTime.set(Calendar.MONTH, month-1);
        startTime.set(Calendar.DAY_OF_MONTH, day);
        startTime.set(Calendar.HOUR_OF_DAY, hour);
        startTime.set(Calendar.MINUTE, min);
        startTime.set(Calendar.SECOND, sec);

        long alarmStartTime = startTime.getTimeInMillis();
        alarmManager.set(AlarmManager.RTC_WAKEUP, alarmStartTime , pendingIntent);
    }
```

```java
public class AlarmReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        String title = intent.getStringExtra("title");
        String message = intent.getStringExtra("message");

        UILib.showNoti(context, title, message, message);
    }
}

public class UILib {
    static final int REQUEST_CODE_MAIN_ACTIVITY = 1000;
    public static int noti_id_start = 0;

    public static void showNoti(Context context, String title, String message, String ticker) {
        Intent intent = new Intent(context, MainActivity.class);

        // Intent の作成
        PendingIntent contentIntent = PendingIntent.getActivity(
                context, REQUEST_CODE_MAIN_ACTIVITY, intent, PendingIntent.FLAG_UPDATE_CURRENT);

        //// LargeIcon の Bitmap を生成
        //Bitmap largeIcon = BitmapFactory.decodeResource(context.getResources(), R.drawable.app_icon);

        // NotificationBuilderを作成
        NotificationCompat.Builder builder = new NotificationCompat.Builder(context.getApplicationContext());
        builder.setContentIntent(contentIntent);
        // ステータスバーに表示されるテキスト
        builder.setTicker(ticker);
        // Notificationを開いたときに表示されるタイトル
        builder.setContentTitle(title);
        // Notificationを開いたときに表示されるサブタイトル
        builder.setContentText(message);

        // アイコン
        builder.setSmallIcon(android.R.drawable.ic_dialog_info);
        // Notificationを開いたときに表示されるアイコン
        //builder.setLargeIcon(largeIcon);

        // 通知するタイミング
        builder.setWhen(System.currentTimeMillis());
        // 通知時の音・バイブ・ライト
        builder.setDefaults(Notification.DEFAULT_SOUND
                | Notification.DEFAULT_VIBRATE
                | Notification.DEFAULT_LIGHTS);
        // タップするとキャンセル(消える)
        builder.setAutoCancel(true);

        // NotificationManagerを取得
        NotificationManager manager = (NotificationManager) context.getSystemService(Service.NOTIFICATION_SERVICE);
        // Notificationを作成して通知


        manager.notify(noti_id_start++, builder.build());

        if (noti_id_start >= Integer.MAX_VALUE) {
            noti_id_start = 0;
        }
    }
}
```

## Graphics

[Android Graphics](android_graphics.md) 참조.

## Speech Recognizer (音声認識)
### Android의 기본UI를 활용한 방법

```java
    public final static int API_SPEECH_TO_TEXT = 1000;

    protected void startSpeechToText() {
        final Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
        intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, "en-US");
        intent.putExtra(RecognizerIntent.EXTRA_PROMPT, message);

        try {
            startActivityForResult(intent, API_SPEECH_TO_TEXT);
        }
        catch (ActivityNotFoundException e) {
            Toast.makeText(getApplicationContext(),
                    e.getMessage(),
                    Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode) {
            case API_SPEECH_TO_TEXT:
                if (resultCode == RESULT_OK && null != data) {
                    final ArrayList<String> result = data.getStringArrayListExtra(RecognizerIntent.EXTRA_RESULTS);
                    // TODO ...
                }
                else {
                    // TODO ...
                }
                break;
        }

        finish();
    }
```

### 자체 UI로 구현할 경우

```java
final Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, "en-US");
intent.putExtra(RecognizerIntent.EXTRA_CALLING_PACKAGE, this.getPackageName());
intent.putExtra(RecognizerIntent.EXTRA_PARTIAL_RESULTS, true); // onPartialResults 이벤트를 받을 경우
//intent.putExtra(RecognizerIntent.EXTRA_PREFER_OFFLINE, true);

m_recognizer = SpeechRecognizer.createSpeechRecognizer(a);
m_recognizer.setRecognitionListener(new RecognitionListener() {
    @Override
    public void onReadyForSpeech(Bundle params) {
        Log.i("recognizer", "onReadyForSpeech");
    }

    @Override
    public void onBeginningOfSpeech() {
        Log.i("recognizer", "onBeginningOfSpeech");
    }

    @Override
    public void onRmsChanged(float rmsdB) {
        //Log.i("recognizer", "onRmsChanged");
    }

    @Override
    public void onBufferReceived(byte[] buffer) {
        Log.i("recognizer", "onBufferReceived");
    }

    @Override
    public void onEndOfSpeech() {
        Log.i("recognizer", "onEndOfSpeech");
    }

    @Override
    public void onError(int error) {
        Log.i("recognizer", "onError=>"+error);
        // 음성입력이 없을 때 자동으로 종료되는 경우에도 onError 이벤트가 발생한다. [error = 6]
    }

    @Override
    public void onResults(Bundle results) {
        final List<String> recData = results.getStringArrayList(android.speech.SpeechRecognizer.RESULTS_RECOGNITION);
        if (recData != null) {
            Log.i("recognizer", "onResults=>"+ recData.get(0));
        }
        else {
            
        }
    }

    @Override
    public void onPartialResults(Bundle partialResults) {
        final List<String> recData = partialResults.getStringArrayList(android.speech.SpeechRecognizer.RESULTS_RECOGNITION);
        if (recData != null) {
            Log.i("slrecognizerib", "onPartialResults=>"+recData.get(0));
        }
    }

    @Override
    public void onEvent(int eventType, Bundle params) {
        Log.i("recognizer", "onEvent=>"+eventType);
    }
});
m_recognizer.startListening(intent);
```

## Tips
### Email 형식 검사

```java
    public final static boolean isValidEmail(CharSequence target) {
        if (target == null || target.length() < 3) {
            return false;
        } else {
            return android.util.Patterns.EMAIL_ADDRESS.matcher(target).matches();
        }
    }
```

### open URL

```java
Intent browserIntent = new Intent(Intent.ACTION_VIEW, Uri.parse("http:// . . ."));
startActivity(browserIntent);
```

### Locale 설정 가져오기

```java
Locale systemLocale = getResources().getConfiguration().locale;
String strDisplayCountry = systemLocale.getDisplayCountry();
String strCountry = systemLocale.getCountry();
String strLanguage = systemLocale.getLanguage();
```

### 네트워크 사용 가능 여부

```java
// ネットワーク接続確認
public static boolean networkCheck(Context context){
    ConnectivityManager cm =  (ConnectivityManager)context.getSystemService(Context.CONNECTIVITY_SERVICE);
    if (cm == null) return false;

    NetworkInfo info = cm.getActiveNetworkInfo();
    if( info != null ){
        return info.isConnected();
    } 
    else {
        return false;
    }
}
```

### Mac Address 가져오기

```java
// need permission: "android.permission.ACCESS_WIFI_STATE"
public static String getMacAddr(Context context) {
    WifiManager wifiManager = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
    WifiInfo wInfo = wifiManager.getConnectionInfo();
    String mac = wInfo.getMacAddress();

    return mac;
}
```

### 화면 꺼짐(절전 모드) 방지

```java
// Activity에서
getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
```

### 카메라 혹은 갤러리에서 사진 가져오기

```java
    public static void openImageBrowser(Activity from, int request_code) {
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("image/*");
        from.startActivityForResult(intent, request_code);
    }

    public static void openCamera(Activity from, int request_code, String img_filename) {
        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
        if (takePictureIntent.resolveActivity(from.getPackageManager()) != null) {
            from.startActivityForResult(takePictureIntent, request_code);
        }
    }

    public static void openCameraOrSelect(final Activity from, final int request_code_camera, final int request_code_select) {
        final String title = "Open Photo";
        final CharSequence[] itemlist = {
                "Take a Photo",
                "Pick from Gallery"};

        AlertDialog.Builder builder = new AlertDialog.Builder(from)
            .setTitle(title)
            .setItems(itemlist, new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    switch (which) {
                        case 0:// Take Photo
                            // Do Take Photo task here
                            openCamera(from, request_code_camera, "temp_pic.jpg");
                            break;
                        case 1:// Choose Existing Photo
                            // Do Pick Photo task here
                            openImageBrowser(from, request_code_select);
                            break;
                        default:
                            break;
                    }
                }
            });
        AlertDialog alert = builder.create();
        alert.setCancelable(true);
        alert.show();
    }

    public static String getFilepathFromMediaStore(Context context, Intent data) {
        final Uri uri = data.getData();

        String[] filepath = { MediaStore.Images.Media.DATA };
        Cursor cursor = context.getContentResolver().query(uri, filepath, null, null, null);
        cursor.moveToFirst();

        int columnIndex = cursor.getColumnIndex(filepath[0]);
        String file_path = cursor.getString(columnIndex);
        cursor.close();

        Logger.i("data: " + data.getData().toString());
        Logger.i("image file path: " + file_path);

        return file_path;
    }

    public static String getRealPathFromURI_API19(Context context, Uri uri){
        if (android.os.Build.VERSION.SDK_INT < 19) return null;

        if ("com.android.providers.downloads.documents".equals(uri.getAuthority())) {
            // ダウンロードからの場合
            String id = DocumentsContract.getDocumentId(uri);
            Uri docUri = ContentUris.withAppendedId(Uri.parse("content://downloads/public_downloads"), Long.valueOf(id));
            Cursor cursor = context.getContentResolver().query(docUri, new String[]{MediaStore.MediaColumns.DATA}, null, null, null);
            if (cursor.moveToFirst()) {
                File file = new File(cursor.getString(0));
                return file.getAbsolutePath();
            }
        }

        String filePath = "";
        String wholeID;
        String id;

        try {
            wholeID = DocumentsContract.getDocumentId(uri);
            Logger.d("wholeID: " + wholeID);

            final String[] result = wholeID.split(":");
            id = result[1];
        }
        catch (Exception e) {
            Logger.d(e.toString());
            return null;
        }

        String[] column = { MediaStore.Images.Media.DATA };

        // where id is equal to
        String sel = MediaStore.Images.Media._ID + "=?";

        Cursor cursor = context.getContentResolver().query(MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
                column, sel, new String[]{ id }, null);

        int columnIndex = cursor.getColumnIndex(column[0]);
        if (cursor.moveToFirst()) {
            filePath = cursor.getString(columnIndex);
        }
        cursor.close();
        return filePath;
    }


    public static String getRealPathFromURI_API11to18(Context context, Uri contentUri) {
        if (android.os.Build.VERSION.SDK_INT < 11 || android.os.Build.VERSION.SDK_INT > 18)
            return null;

        String[] proj = { MediaStore.Images.Media.DATA };
        String result = null;

        CursorLoader cursorLoader = new CursorLoader(
                context,
                contentUri, proj, null, null, null);
        Cursor cursor = cursorLoader.loadInBackground();

        if(cursor != null){
            int column_index =
                    cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA);
            cursor.moveToFirst();
            result = cursor.getString(column_index);
        }
        return result;
    }
```

## Google Play Service
최신 버전 확인
* https://developers.google.com/android/guides/setup

## 문제 및 해결
### Runtime Permissions
Android 6.0 (Marshmallow, SDK 23) 이상에서는 AndroidManifest.xml 에서 권한을 지정하는 것만으로는 안된다.

일부 권한은 Runtime Permission 요청을 통해서 유저로부터 직접 확인을 받아야만 한다.
```java
public static final class Permissions {
    public static final int REQUEST_PERMISSIONS = 777;
    private static String[] PERMISSIONS = {
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE,
            Manifest.permission.CAMERA,
    };

    public static void verifyPermissions(Activity activity) {
        if (android.os.Build.VERSION.SDK_INT < 23) return;

        boolean is_ok = true;
        for (String p : PERMISSIONS) {
            int check_self = ActivityCompat.checkSelfPermission(activity, p);
            if (check_self != PackageManager.PERMISSION_GRANTED) {
                is_ok = false;
            }
        }

        if (!is_ok) {
            // We don't have permission so prompt the user
            ActivityCompat.requestPermissions(
                    activity,
                    PERMISSIONS,
                    REQUEST_PERMISSIONS
            );
        }
    }
}
```

권한 요청에 대한 결과는 Activity의 onRequestPermissionsResult 이벤트로 확인 할 수 있다.
```java
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        if (requestCode == Permissions.REQUEST_PERMISSIONS) {
            for (int r : grantResults) {
                if (r != PackageManager.PERMISSION_GRANTED) {
                    Alert.showError(getContext(), getString(R.string.err_app_permissions), new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialogInterface, int i) {
                            finish();
                        }
                    });
                }
            }
        }
    }
```

### formatted string을 strings.xml 리소스로 뺄 때의 문제
http://androidgamepark.blogspot.jp/2013/05/multiple-substitutions-specified-in-non.html

String.format() 등에서 사용하는 형식 문자열(formatted string)을 그대로 xml 리소스로 빼내면 다음과 같은 에러가 발생.
 Multiple substitutions specified in non-positional format; did you mean to add the formatted="false" attribute? 
이유는 %문자가 xml에서 사용되는 특수 기호이기 때문. 다음과 같이 처리하자.
```xml
<!-- 이것은 에러 -->
<string name="msg_format_date">%4d年%02d月%02d日</string>

<!-- 이렇게 한다 -->
<string name="msg_format_date">%1$4d年%2$02d月%3$02d日</string>
```
1$, 2$ 등의 의미는 1번째 인자, 2번째 인자, ... 를 의미한다.

### ConnectivityManager.getNetworkInfo() 문제

ICS 버전에서 ConnectivityManager의 getNetworkInfo() 호출이 _null_이 되는 경우가 있다.
```java
ConnectivityManager cm = 
  (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
NetworkInfo ni = cm.getNetworkInfo(ConnectivityManager.TYPE_MOBILE);
```
3G 기능이 없는 기기일 때, 위 코드에서 ni는 _null_ 값이 되버린다.<br />
그러니 반드시 getNetworkInfo()의 반환값이 _null_ 인지 먼저 확인하고 진행할 것.

### MapView 추가할 때

MapActivity에서 super.onCreate()가 먼저 호출된 다음에 MapView가 추가되어야 오류가 발생하지 않는다. 

layout.xml 등으로 MapView를 추가할 때도 setContentView() 호출이 supoer.onCreate() 다음에 되도록 주의.

### Debug Certificate expired
어느날 프로젝트를 빌드하는데 Debug Certificate expired 오류가 뜬다.
* 해결: clean project 후 rebuild를 시도. 그래도 저 오류가 뜬다면 debug.keystore 인증기간이 지난 것이다. (인증기간이 1년으로 설정되어 있다) 윈도우라면 Windows '사용자(Users)' 폴더 밑에 .android 라는 폴더에서 debug.keystore 파일을 지워보고 다시 빌드하면 된다.

### 네트워크 스레드 문제 
Android 3.0 이상에서 **android.os.NetworkOnMainThreadException** 오류를 만났다면, 이것은 메인 스레드에서 네트워크 작업을 수행했기 때문이다. 

3.0부터 안드로이드는 이같은 동작을 오류로 판단하고 강제 종료시킨다.

### 앱 우선순위 높이기
시스템 자원 부족으로 프로세스 킬이 진행될 때, 조금이라도 오래 살고 싶으면 Service(비록 아무일도 안하더라도)를 이용해서 앱의 우선순위(중요도)를 높여야 한다.

남용하지는 말자. 리소스를 많이 사용하는 게임이나 네비게이션, 영상 처리 등의 앱에서 사용하는게 좋을 듯.

서비스 작성(예시)
```java
public class NokillService extends Service {
  @Override
  public IBinder onBind(Intent intent) {
    return null;
  }
  
  @Override
  public int onStartCommand(Intent intent, int flags, int startId) {
    setForegroundService();
    return Service.START_STICKY;
  }
  
  void setForegroundService() {
    final Intent it = new Intent(this, MainActivity.class);
    final PendingIntent pi = PendingIntent.getActivity(this, 0, it, 0);
    
    Notification noti = new NotificationCompat.Builder(this, "channel_id")
      .setContentTitle("AppTitle")
      .setContentText("running...")
      .setTicker("running...")
      .setSmallIcon(R.drawable.ic_launcher)
      .setContentIntent(pi)
      .build();
    
    startForeground(1, noti);
  }
}
```

중요한건, 서비스 내에서 startForeground()를 호출해서 foreground 서비스로 활성화되어야 한다는 것.

foreground 서비스로 등록하기 위해서는 Notification이 필수로 제공되어야 한다.

AndroidManifest.xml 등록
```xml
<service android:name="com.xxx.service.NokillService"/>
```

서비스 호출 및 종료(예시)
```java
public void startNokillService() {
  nokillServ = new Intent(this, NokillService.class);
  startService(nokillServ);
}

public void stopNokillService() {
  if (nokillServ != null)
    stopService(nokillServ);
  
  nokillServ = null;
}
```
