---
title: "동영상 녹화하기"
author: seyoung
date: '2022-02-03 02:00:00 +0900'
categories: Android Multimedia
tags: [android,viedio,record]
math: true
mermaid: true
---

## 동영상 녹화하기

오디오 녹음에 사용하는 **MediaRecorder** 객체는 동영상 녹화에도 그대로 이용할 수 있다.

동영상 녹화가 오디오 녹음과 다른 점은 영상을 녹음하기 위한 입력 소스로 카메라를 지정하여  사용자가 카메라 미리 보기를 할 수 있도록 만들어 주어야 한다는 것이다.

입력 소스로 지정할 수 있는 마이크는 MIC라는 상수로 정의되어 있고, 카메라는 CAMERA라는 상수로 정의되어 있다.

**API**

```java
MediaRecorder.AudioSource.MIC
MediaRecorder.VideoSource.CAMERA
```

---

### 예제

```java
package org.techtown.video.recorder;

import androidx.appcompat.app.AppCompatActivity;

import android.content.ContentValues;
import android.content.Intent;
import android.media.MediaPlayer;
import android.media.MediaRecorder;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.provider.MediaStore;
import android.util.Log;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.widget.Button;
import android.widget.FrameLayout;
import android.widget.Toast;

import java.io.File;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    MediaPlayer player;
    MediaRecorder recorder;
    File file;
    String filename;

    SurfaceHolder holder;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        SurfaceView surface = new SurfaceView(this);    // SurfaceView 객체 만들기
        holder = surface.getHolder();

        FrameLayout frame = findViewById(R.id.container);
        frame.addView(surface);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startRecording();
            }
        });
        Button button2 = findViewById(R.id.button2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                stopRecording();
            }
        });

        Button button3 = findViewById(R.id.button3);
        button3.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startPlay();
            }
        });

        Button button4 = findViewById(R.id.button4);
        button4.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                stopPlay();
            }
        });

        file = getOutputFile();
        if (file != null) {
            filename = file.getAbsolutePath();
        }

        AndPermission.with(this)
                .runtime()
                .permission(
                        Permission.CAMERA,
                        Permission.RECORD_AUDIO,
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

    public File getOutputFile() {
        File mediaFile = null;
        try {
            File mediaStorageDir = new File(Environment.getExternalStoragePublicDirectory(
                    Environment.DIRECTORY_PICTURES), "MyApp");
            if (!mediaStorageDir.exists()){
                if (!mediaStorageDir.mkdirs()){
                    Log.d("MyCameraApp", "failed to create directory");
                    return null;
                }
            }

            mediaFile = new File(mediaStorageDir.getPath() + File.separator + "recorded.mp4");
        } catch(Exception e) {
            e.printStackTrace();
        }

        return mediaFile;
    }


    public void startRecording(){
        if(recorder == null)
            recorder = new MediaRecorder();

        recorder.setAudioSource(MediaRecorder.AudioSource.MIC);
        recorder.setVideoSource(MediaRecorder.VideoSource.CAMERA);
        recorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
        recorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
        recorder.setOutputFile(filename);

        // MediaRecorder에 미리보기 화면을 보여줄 객체 설정하기
        recorder.setPreviewDisplay(holder.getSurface());

        try{
            recorder.prepare();
            recorder.start();
        }catch(Exception e){
            e.printStackTrace();

            recorder.release();
            recorder = null;
        }
    }

    public void stopRecording(){
        if (recorder == null)
            return;

        recorder.stop();
        recorder.reset();
        recorder.release();
        recorder = null;

        ContentValues values = new ContentValues(10);

        values.put(MediaStore.MediaColumns.TITLE, "RecordedVideo");
        values.put(MediaStore.Audio.Media.ALBUM, "Video Album");
        values.put(MediaStore.Audio.Media.ARTIST, "Mike");
        values.put(MediaStore.Audio.Media.DISPLAY_NAME, "Recorded Video");
        values.put(MediaStore.MediaColumns.DATE_ADDED, System.currentTimeMillis() / 1000);
        values.put(MediaStore.MediaColumns.MIME_TYPE, "video/mp4");
        values.put(MediaStore.Audio.Media.DATA, filename);

        Uri videoUri = getContentResolver().insert(MediaStore.Video.Media.EXTERNAL_CONTENT_URI, values);

        if(videoUri == null){
            Log.d("SampleVideoRecorder", "Video insert failed");
            return;
        }

        sendBroadcast(new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, videoUri));
    }


    public void startPlay() {
        if (player == null) {
            player = new MediaPlayer();
        }

        try {
            player.setDataSource(filename);
            player.setDisplay(holder);

            player.prepare();
            player.start();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void stopPlay() {
        if (player == null) {
            return;
        }

        player.stop();
        player.release();
        player = null;
    }

}

```

미디어 앨범에 녹화된 동영상을 저장하고 싶을 때는 오디오의 경우처럼 내용 제공자를 사용하면 된다.

이때 미디어 앨범에 저장되었다는 정보를 다른 앱에도 알려주고 싶다면 Intent.ACTION_MEDIA_SCANNER_SCAN_FILE 액션을 이용해 새로 만들어진 URI 객체를 브로드 캐스팅하면 된다.

접근 권한 추가 

**AndroidManifest.xml**

```xml
<uses-permission android:name="android.permission.RECORD_AUDIO"/>  
<uses-permission android:name="android.permission.CAMERA"/>  
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

<br>

외부 라이브러리 추가

**build.gradle:app**
```xml
dependencies {
	...
	implementation 'com.yanzhenjie:permission:2.0.3'
	}
```

---

**결과**

![1](https://user-images.githubusercontent.com/54762273/152200607-8cafb36a-41dd-4d2a-bb99-b55cd235770b.PNG)