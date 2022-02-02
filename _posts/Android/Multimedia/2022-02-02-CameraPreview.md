---
title: "카메라에 미리보기 넣기"
author: seyoung
date: '2022-02-02 18:55:00 +0900'
categories: Android Multimedia
tags: [android,camera,preview ]
math: true
mermaid: true
---

## 카메라에 미리보기 넣기

카메라 미리보기 화면에 증강 현실을 표현할 아이콘이나 그래픽 등을 보여주고 싶다면 어떻게 해야할까?

우리가 만든 앱에 카메라 미리보가와 사진찍기 기능을 넣을 수 있지만 코드는 좀 더 많이지고 
**서피스(Surface View)** 라는 것으로 미리보기 화면이 구현되기 때문에 **서피스 뷰가 무엇인지 이해해야 한다.**

![Untitled Diagram drawio-3](https://user-images.githubusercontent.com/54762273/152110520-27509f28-82d5-4ff5-9301-4d6d1e9ed3a7.png)


카메라 미리보기 기능을 구현 하려면 일반 뷰(View)가 아니라 서피스 뷰를 사용해야 한다.

**서피스 뷰는 서피스홀더에 의해 제어 된다.**

만약 카메라 객체를 만든 후 미리보기 화면을 서피스 뷰에 보여주고 싶다면 **서피스 홀더** 객체의 **setPreviewDisplay** 메서드로 미리 보기를 설정해 주어야 한다.

**API**

``` java
public void setPreviewDisplay (Surface sv)
```

필요한 초기화 작업이 끝나면 카메라 객체의 `startPreview` 메서드를 호출할 수 있으며,
이때부터 입력된 영상을 **서피스 뷰로 화면을 보여주게 된다.**

---

### 예제

**activity_main,xml**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  xmlns:app="http://schemas.android.com/apk/res-auto"  
  xmlns:tools="http://schemas.android.com/tools"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:orientation="vertical"  
  tools:context=".MainActivity">  
  
    <Button  
  android:id="@+id/button"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:text="사진 찍기" />  
  
    <FrameLayout  
  android:id="@+id/previewFrame"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent">  
  
    </FrameLayout>  
</LinearLayout>
```

카메라 미리보기 화면을 FrameLayout안에 추가하기 위해 소스코드에서 **CameraSufaceView**라는 새로운 클래스를 **MainActivity** 클래스 안에 내부 클래스로 정의하고 그 클래스의 인스턴스 객체를 만들어 추가하자.

우선 CameraSurfaceVIew 클래스가 있다고 가정하고 메인 액티비트 코드를 작성해보자.
<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {  
    CameraSurfaceView cameraView;  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        FrameLayout previewFrame = findViewById(R.id.previewFrame);  
        cameraView = new CameraSurfaceView(this);  
        previewFrame.addView(cameraView);  
  
        Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener() {  
            public void onClick(View v){  
                takePicture();  
            }  
        });  
    }  
  
    public void takePicture(){  
        /* CameraSurfaceView의 capture 메서드 호출하기 */  
			    cameraView.capture(new Camera().PictureCallback() {  
            try {  
                /* 전달 받은 바이트 배열을 Bitmap 객체로 만들기 */  
			    Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);  
                String outUriStr = MediaStore.Images.Media.insertImage(  
                        getContentResolver(),  
                        bitmap,  
                        "Caputred Image",  
                        "Caputred Image using Camera.");  
  
                if (outUriStr == null) {  
                    Log.d("SampleCapture", "Image insert failed.");  
                    return;  
                } else {  
                    Uri outUri = Uri.parse(outUriStr);  
                    sendBroadcast(new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, outUri));  
                }  
  
                camera.startPreview();  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
        });  
    }  
  
}

```

카메라 미리보기를 구현하는 CameraSurfaceView는 SurfaceView를 상속받아 새로 정의한 후 XML 레이아웃에 정의된 FameLayout에 추가한다. 

사진 찍기 버튼을 누르면 사진을 한장 찍어 미디어 앨범에 추가하게 되는데 사진을 찍은 결과를 처리하는 코드는 **PictureCallback** 인터페이스를 구현하는 부분에 들어 있다.

**API**
```java
public abstarct void onPictureTaken (byte[] data, Camera camera)
```

즉, 사진을 찍을 때 자동으로 호출되는 `onPictureTaken` 메서드로 캡쳐한 이미지 데이터가 전달된다.

그런 다음 데이터 비트맵으로 만들고 `MediaStore.Images.Media`에 정의된 `insertImage` 메서드를 이용하여 미디어 앨범에 추가한다.

이미지 데이터를 비트맵으로 만들기 위해서는 **BitmapFactory** 클래스에 정의된 `decodebyteArray` 메서드를 이용한다.

`insertImage` 메서드는 간단한 방법으로 이미지를 추가할 수 있도록 정의된 메서드이다.

자세히 봐보자.

**API**

```java
public static final String insertImage(ContentResolver cr, Bitmap source,
									   String title,String description)
```

다음은 서피스 뷰를 확장하여 정의한 **CameraSurfaceView 클래스이다.**

**이 클래스는 SurfaceHolder에 정의된 Callback 인터페이스를 구현하고 있으므로 서피스 뷰의 상태가 변경될 때 자동으로 호출되는 세가지 메서드 (surfaceCreate, surfaveChanged, surfaceDestoryed)가 구현되어 있다.**

이 클래스는 **MainActivity.java** 클래스 안에 내부 클래스로 정의 한다.

**CameraSurfaceView.java**

```java
public class MainActivity extends AppCompatActivity {  
    CameraSurfaceView cameraView;  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        FrameLayout previewFrame = findViewById(R.id.previewFrame);  
        cameraView = new CameraSurfaceView(this);  
        previewFrame.addView(cameraView);  
  
        Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener() {  
            public void onClick(View v) {  
                takePicture();  
            }  
        });  
  
        AndPermission.with(this)  
                .runtime()  
                .permission(  
                        Permission.CAMERA,  
                        Permission.READ_EXTERNAL_STORAGE,  
                        Permission.WRITE_EXTERNAL_STORAGE)  
                .onGranted(new Action<List<String>>() {  
                    @Override  
  public void onAction(List<String> permissions) {  
                        showToast("허용된 권한 갯수 : " + permissions.size());  
                    }  
                })  
                .onDenied(new Action<List<String>>() {  
                    @Override  
  public void onAction(List<String> permissions) {  
                        showToast("거부된 권한 갯수 : " + permissions.size());  
                    }  
                })  
                .start();  
  
    }  
  
    public void showToast(String message) {  
        Toast.makeText(this, message, Toast.LENGTH_LONG).show();  
    }  
  
    public void takePicture() {  
        cameraView.capture(new Camera.PictureCallback() {  
            public void onPictureTaken(byte[] data, Camera camera) {  
                try {  
                    Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);  
                    String outUriStr = MediaStore.Images.Media.insertImage(  
                            getContentResolver(),  
                            bitmap,  
                            "Captured Image",  
                            "Captured Image using Camera.");  
  
                    if (outUriStr == null) {  
                        Log.d("SampleCapture", "Image insert failed.");  
                        return;  
                    } else {  
                        Uri outUri = Uri.parse(outUriStr);  
                        sendBroadcast(new Intent(  
                                Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, outUri));  
                    }  
  
                    camera.startPreview();  
                } catch (Exception e) {  
                    e.printStackTrace();  
                }  
            }  
        });  
    }  
  
  
    private class CameraSurfaceView extends SurfaceView implements SurfaceHolder.Callback {  
        private SurfaceHolder mHolder;  
        private Camera camera = null;  
  
        public CameraSurfaceView(Context context) {  
            super(context);  
  
            mHolder = getHolder();  
            mHolder.addCallback(this);  
        }  
  
        public void getCameraInstance(){  
            try {  
                camera = Camera.open();  
            } catch (Exception e){  
                showToast("카메라가 다른 앱에서 사용중입니다.");  
            }  
        }  
  
        public void surfaceCreated(SurfaceHolder holder) {  
            getCameraInstance();  
            setCameraOrientation();  
  
            try {  
                camera.setPreviewDisplay(mHolder);  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
        }  
  
        public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {  
            try {  
                camera.startPreview();  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
        }  
  
        public void surfaceDestroyed(SurfaceHolder holder) {  
            try {  
                camera.stopPreview();  
                camera.release();  
                camera = null;  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
        }  
  
        public boolean capture(Camera.PictureCallback handler) {  
            if (camera != null) {  
                camera.takePicture(null, null, handler);  
                return true;  
            } else {  
                return false;  
            }  
        }  
  
        public void setCameraOrientation() {  
            if (camera == null) {  
                return;  
            }  
  
            Camera.CameraInfo info = new Camera.CameraInfo();  
            Camera.getCameraInfo(0, info);  
  
            WindowManager manager = (WindowManager) getSystemService(Context.WINDOW_SERVICE);  
            int rotation = manager.getDefaultDisplay().getRotation();  
  
            int degrees = 0;  
            switch (rotation) {  
                case Surface.ROTATION_0: degrees = 0; break;  
                case Surface.ROTATION_90: degrees = 90; break;  
                case Surface.ROTATION_180: degrees = 180; break;  
                case Surface.ROTATION_270: degrees = 270; break;  
            }  
  
            int result;  
            if (info.facing == Camera.CameraInfo.CAMERA_FACING_FRONT) {  
                result = (info.orientation + degrees) % 360;  
                result = (360 - result) % 360;  
            } else {  
                result = (info.orientation - degrees + 360) % 360;  
            }  
  
            camera.setDisplayOrientation(result);  
        }  
  
    }  
  
}

```
<br>

서피스 뷰가 만들어지면서 호출되는 `surfaceCreated` 메서드 안에서는` Camera.open` 메서드를 이용해 카메라를 오픈하고 이를 통해 참조한 카메라 객체에 서피스 홀더 객체를 지정해야 한다.

카메라 객체에 서피스홀더 객체를 지정하기 위해 `setPreviewDisplay` 메서드를 사용한다.서피스뷰의 크기가 변경되거나 할 때 호출되는 `surfaceChanged` 메서드 안에서는 `startPreview` 메서드를 이용해 미리보기를 시작한다.

서피스 뷰의 리소스를 해체하면서 호출되는 `surfaceDestroyed` 메서드 안에서는 카메라 객체의 `stopPreview` 메서드를 호출하여 미리보기를 끝낸 후 변수를 NULL 값으로 지정한다.

버튼을 클릭했을 때 사진을 찍기 위해 호출하는 caputre 메서드 안을 보면 카메라 객체의 `takePicture` 메서드를 호출하여 사진을 찍고 있으며, 이때 PictureCallback 인터페이스를 구현한 객체를 매개변수로 전달함으로써 사진을 찍었을 때 이 객체의 `onPictureTaken` 메서드가 자동으로 호출되도록 한다.

우리가 만드는 앱은 Camera와 SD 카드를 접근하므로 이 권한을 매니페스트에 추가하고 위험권한에 대한 권한 부여를 요청하는 코드를 추가해야 한다.

먼저 `AndroidManifest.xml` 파일을 열고 다음과 같이 권한을 추가한다.

```xml
<uses-permission android:name="android.permission.CAMERA" />  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />  
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />  
<uses-feature android:name="android.hardware.camera" />
```

이 권한들은 위험 권한으로 분류 되어 있으므로 자동으로 위험 권한을 부여를 위한 대화상자를 보여주는 코드를 추가한다.

**build.gradle(Module: SampleCaputre.app** 파일을 열고 자동으로 위험 권한을 부여하기 위한 외부 라이브러리를 추가한다.

```xml
dependencies {  
  ...
  implementation 'com.yanzhenjie:permission:2.0.3'  
}

```

카메라 미리보기의 화면이 세로모드로 보이도록 하고 싶다면 MainActivity 클래스의 내부 클래스로 정의한 **CameraSurfaceView** 클래스 안의 surfaceCreated 메서드를 수정한다.

<br>

```java
public void surfaceCreated(SurfaceHolder holder) {  
    camera = Camera.open();  
    setCameraOrientation();  
  
    try {  
        camera.setPreviewDisplay(mHolder);  
    } catch (Exception e) {  
        e.printStackTrace();  
    }  
}

public void setCameraOrientation() {  
    if (camera == null) {  
        return;  
    }  
  
    Camera.CameraInfo info = new Camera.CameraInfo();  
    Camera.getCameraInfo(0, info);  
  
    WindowManager manager = (WindowManager) getSystemService(Context.WINDOW_SERVICE);  
    int rotation = manager.getDefaultDisplay().getRotation();  
  
    int degrees = 0;  
    switch (rotation) {  
        case Surface.ROTATION_0: degrees = 0; break;  
        case Surface.ROTATION_90: degrees = 90; break;  
        case Surface.ROTATION_180: degrees = 180; break;  
        case Surface.ROTATION_270: degrees = 270; break;  
    }  
  
    int result;  
    if (info.facing == Camera.CameraInfo.CAMERA_FACING_FRONT) {  
        result = (info.orientation + degrees) % 360;  
        result = (360 - result) % 360;  
    } else {  
        result = (info.orientation - degrees + 360) % 360;  
    }  
  
    camera.setDisplayOrientation(result);  
}
```

`setCameraOrientation` 메서드는 `onSurfaceCreated` 메서드 안에서 호출되는 메서드이며 카메라 미리 보기 방향을 설정한다.