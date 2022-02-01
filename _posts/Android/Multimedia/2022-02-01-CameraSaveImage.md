---
title: "카메라로 사진 찍어 저장하기"
author: seyoung
date: '2022-02-01 15:46:00 +0900'
categories: Android Multimedia
tags: [android,camera,saveimage ]
math: true
mermaid: true
---

## 카메라로 사진 찍어 저장하기

카메라로 사진을 찍기 위해 사용되는 방법은 크게 두가지로 나눌 수 있다.

 - 인텐트로 단말의 카메라 앱을 실행한 후 결과 사진을 받아 처리하기
 - 앱 화면에 카메라 미리보기를 보여주고 직접 사진을 찍어 처리하기

스마트폰 단말에는 카메라 앱이 미리 설치되어 있는데 이 앱을 사용하면 가장 간단하게 다른 기능의 앱을 구현할 수 있다.

단말의 카메라 앱은 다른 개발자가 미리 만들어 설치해둔 것이므로 우리가 만들려는 앱에서 카메라 앱의 화면을 띄우려면 인텐트를 만들어 시스템에 요청하면 된다.

---

### 인텐트를 사용해 단말의 카메라 앱을 실행한 후 결과 사진 처리하기

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {  
    ImageView imageView;  
  
    File file;  
    Uri uri;  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        imageView = findViewById(R.id.imageView);  
  
        Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener() {  
            @Override  
			 public void onClick(View v) {  
                takePicture();  
            }  
        });  
  
    }  
  
    public void takePicture() {  
        try {  
            file = createFile();  
            if (file.exists()) {  
                file.delete();  
            }  
  
            file.createNewFile();  
        } catch(Exception e) {  
            e.printStackTrace();  
        }  
  
        if(Build.VERSION.SDK_INT >= 24) {  
            uri = FileProvider.getUriForFile(this, BuildConfig.APPLICATION_ID, file);  
        } else {  
            uri = Uri.fromFile(file);  
        }  
  
        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);  
        intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);  
        intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);  
  
        startActivityForResult(intent, 101);  
    }  
  
    private File createFile() {  
        String filename = "capture.jpg";  
        File outFile = new File(getFilesDir(), filename);  
        Log.d("Main", "File path : " + outFile.getAbsolutePath());  
  
        return outFile;  
    }  
  
    @Override  
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {  
        super.onActivityResult(requestCode, resultCode, data);  
  
        if (requestCode == 101 && resultCode == RESULT_OK) {  
            try {  
                Bitmap bitmap = BitmapFactory.decodeStream(getContentResolver().openInputStream(uri));  
                imageView.setImageBitmap(bitmap);  
            } catch (FileNotFoundException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
}
```

버튼을 클릭하면 `takePicture` 메서드를 호출한다. 

이 메서드가 호출되면 먼저 파일을 만드는데 이 파일은 카메라 앱에서 사진을 찍은 후에 그 결과물을 저장할 파일이다.

`FileProvider.getUriForFile` 메서드를 사용하면 카메라 앱에서 공유하며 사용할 수 있는 파일의 정보를 Uri 객체로 만들 수 있다.

`putExtra` 메서드는 다른 액티비티에 부가 데이터를 전달할 때 사용하는데 <키,value>로 전달한다.

Uri 객체는 `MediaStore.EXTRA_OUTPUT` 키를 사용해서 인텐트에 부가 데이터를 추가한다.

인텐트 객체를 만들었으므로 `startActivityForResult` 메서드를 이용해서 시스템으로 인텐트 객체를 전달한다.

단말의 카메라 앱을 띄워달라는 액션 정보는 MediaStore.ACTION_IMAGE_CAPTURE이다.

인텐트 객체를 만들어 카메라 앱을 실행한 후 사진을 찍고 나면 카메라 앱의 액티비티를 닫게 되는데 그때 응답을 받는 부분은 `onActicityResult` 메서드이다.

 `onActicityResult`  호출되면 카메라 앱에서 찍은 사진을 파일에서 확인할 수 있다.
 
 일반적으로 카메라 해상도가 높은 경우 비트맵 객체의 크기도 커지므로 적당한 비율로 축소하여 만들게된다. 여기에서는 1/8 크기로 축소했으며 bitmap 객체로 만들때는 **BitmapFactory** 클래스의 `decodeFile` 메서드를 호출하면 된다.

<br>

**external.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>  
<paths xmlns:android="http://schemas.android.com/apk/res/android">  
    <cache-path name="cache" path="/" />  
    <files-path name="files" path="/" />  
    <external-files-path name="external_files" path="." />  
</paths>
```

**external.xml** 파일 안에 있는 **\<path> 태그는 \<cache-path>, \<files-path>,** **<\external-files-path>** 태그를 포함하고 있으며 이는 앱의 cache 폴더, files 폴더, externalFiles 폴더를 접근할 수 있도록 허용한다.

<br>

**AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
  package="org.techtown.capture.intent">  
  
    <application  
  android:allowBackup="true"  
  android:icon="@mipmap/ic_launcher"  
  android:label="@string/app_name"  
  android:roundIcon="@mipmap/ic_launcher_round"  
  android:supportsRtl="true"  
  android:theme="@style/Theme.SampleCaptureIntent">  
        <activity android:name=".MainActivity" android:exported="true">  
            <intent-filter>  
                <action android:name="android.intent.action.MAIN" />  
  
                <category android:name="android.intent.category.LAUNCHER" />  
            </intent-filter>  
        </activity>  
  
        <provider  
			  android:name="androidx.core.content.FileProvider"  
			  android:authorities="${applicationId}"  
			  android:exported="false"  
			  android:grantUriPermissions="true">  
            <meta-data  
				  android:name="android.support.FILE_PROVIDER_PATHS"  
				  android:resource="@xml/external" />  
        </provider>  
  
    </application>  
  
</manifest>
```

**AndroidManifest.xml** 파일을 열고 **\<provider>** 태그로 내용 제공자를 추가한다.

내용 제공자는 androidx 패키지 안에 들어 있는 **FileProvider**를 사용한다.

**\<provider>** 태그 안에는 name 속성이 들어 있고 android.core.content.FileProvider 클래스를 지정하고 있다. 

**FileProvider 특정 폴더를 공유하는데 사용하는 내용 제공자이다.**

**\<authorities>** 속성에 설정한 값은 이 앱의 패키지 이름이다.

**\<meta-data>** 태그 안에는 **name과 resource** 속성이 들어가며 resource 속성으로 /app/res/xml 폴더 안에 만들었던  **external.xml** 파일을 지정한다.

이때 파일 확장자는 제외하므로 **@xml/external** 값으로 설정된다.


---

**결과**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/151919220-2bc6e692-9986-4e3a-b53b-ec1e072f7374.gif)
