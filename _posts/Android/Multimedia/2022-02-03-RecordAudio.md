---
title: "오디오 녹음하여 저장하기"
author: seyoung
date: '2022-02-03 01:31:00 +0900'
categories: Android Multimedia
tags: [android,audio,record]
math: true
mermaid: true
---

## 오디오 녹음하여 저장하기

오디오나 동영상을 재생할 때 미디어 플레이어가 사용되는 것처럼 오디오 녹음이나 동영상 녹화를 위해서는
 **미디어리코더(MediaRecorder)** 가 사용된다. 

아래와 같은 과정을 거쳐 음성 녹음을 할 수 있다.

|구분|설명|
|:--:|:--:|
|1. 미디어 리코더 객체 생성  | new 연산자를 이용하여 만든다. |
|2. 오디오 입력 및 출력 형식 설정| 오디오 정보를 입력받을 데이터 소스와 함께 출력 형식을 설정한다.|
|3. 오디오 인코더와 파일 지정| 오디오 파일을 만들 때 필요한 인코더(Encorder)와 함께 파일 이름을 지정|
|4. 녹음 시작| 녹음을 시작하면 오디오 파일이 만들어지고 인코딩된 바이트 스트림이<br> 저장됨|
|5. 매니페스트 권한 설정| RECORDE_AUDIO 권한이 있어야 하므로 권한을 추가해준다.|


---

### 예제

**MainActivity.java**

```java
package org.techtown.myapplication;
import android.content.ContentValues;
import android.media.MediaPlayer;
import android.media.MediaRecorder;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.provider.MediaStore;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.yanzhenjie.permission.Action;
import com.yanzhenjie.permission.AndPermission;
import com.yanzhenjie.permission.runtime.Permission;

import java.io.File;
import java.util.List;

public class MainActivity extends AppCompatActivity {
    MediaRecorder recorder;
    MediaPlayer player;

    File file;
    String filename;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = findViewById(R.id.button2);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                startRecording();
            }
        });


        Button button2 = findViewById(R.id.button3);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                stopRecording();
            }
        });

        Button button3 = findViewById(R.id.button4);
        button3.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startPlay();
            }
        });

        Button button4 = findViewById(R.id.button5);
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
        if(recorder == null){
            recorder = new MediaRecorder();
        }

        /* MediaRecorder 설정하기 */
        recorder.setAudioSource(MediaRecorder.AudioSource.MIC);     // 마이크로 입력 받기
        recorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);    // MPEG4 포맷으로 지정
        recorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);   // 디폴트 인코터 사용
        recorder.setOutputFile(filename);       // 결과물 파일을 설정하는데 사용

        try{
            /* MediaRecorder 시작하기 */
            recorder.prepare();
            recorder.start();
        } catch(Exception e){
            e.printStackTrace();
        }
    }


    public void stopRecording() {
        if (recorder == null) {
            return;
        }

        recorder.stop();
        recorder.release(); // 리소스 해제하는 역할
        recorder = null;

        //  내용 제공자에 새로운 값을 추가하기 위해 CotentValue 객체에 필요한 정보를 put
        ContentValues values = new ContentValues(10);

        values.put(MediaStore.MediaColumns.TITLE, "Recorded");
        values.put(MediaStore.Audio.Media.ALBUM, "Audio Album");
        values.put(MediaStore.Audio.Media.ARTIST, "Mike");
        values.put(MediaStore.Audio.Media.DISPLAY_NAME, "Recorded Audio");
        values.put(MediaStore.Audio.Media.IS_RINGTONE, 1);
        values.put(MediaStore.Audio.Media.IS_MUSIC, 1);
        values.put(MediaStore.MediaColumns.DATE_ADDED,
                System.currentTimeMillis()/1000);
        values.put(MediaStore.MediaColumns.MIME_TYPE, "audio/mp4");
        values.put(MediaStore.Audio.Media.DATA, filename);

        // ContentValues를 내용 제공자에 넣어준다.
        Uri audioUri = getContentResolver().insert(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, values);
        if (audioUri == null) {
            Log.d("SampleAudioRecorder", "Audio insert failed.");
            return;
        }
    }

    public void startPlay() {
        killMediaPlayer();

        try {
            player = new MediaPlayer();
            player.setDataSource(filename);
            player.prepare();
            player.start();
        } catch(Exception e) {
            e.printStackTrace();
        }
    }

    public void stopPlay() {
        if (player != null) {
            player.stop();
        }
    }

    private void killMediaPlayer() {
        if (player != null) {
            try {
                player.release();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```
<br>

**권한 추가**
```xml
  
<uses-permission android:name="android.permission.RECORD_AUDIO"/>  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>  
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />  
  
<application  
  android:requestLegacyExternalStorage="true"
```


### 결과
![1](https://user-images.githubusercontent.com/54762273/152193252-6019d370-6128-4fdb-9c84-eaeefe2f051d.PNG)

