---
title: "동영상 재생하기"
author: seyoung
date: '2022-02-02 20:30:00 +0900'
categories: Android Multimedia
tags: [android,video ]
math: true
mermaid: true
---

## 동영상 재생하기

동영상을 재생하고 싶으면 **Video View** 위젯을 사용하면 되는데 XML 레이아웃에 \<Video View> 태그를 추가하기만 하면 동영상 플레이어를 바로 만들 수 있다.

> 동영상 화면에 디스플레이 되는 영역이 있으므로 위젯으로 XML 레이아웃에 추가할 수 있다. XML 레이아웃에 태그를 추가하고 한두줄의 코드만 구현하면 동영상을 재생할 수 있다.


---

### 예제

<img width="653" alt="스크린샷 2022-02-02 오후 8 05 25" src="https://user-images.githubusercontent.com/54762273/152142222-ad0685a8-09bb-4b9d-8a6a-50ed4e68da72.png">

위 처럼 VideoView를 추가 해준다.

그 다음 레이아웃에 추가한 비디오 뷰 객체를 자바 코드에서 참조한 후 동영상 파일의 위치를 `setVideoURI` 메서드로 지정하기만 하면 동영상을 재생할 수 있다.

동영상의 상태를 보거나 동영상을 제어할 때 사용되는 **미디어 컨트롤러 객체**는 `setMediaController` 메서드로 설정할 수 있는데 손가락으로 터치하면 컨트롤러 부분을 보여주게 된다.

이 컨트롤러로 비디오 재생 상태를 확인할 수 있다.

비디오 뷰 객체에는 `getDuration` 이나 `pause`와 같이 동영상을 제어하는데 필요한 다른 메서드들로 정의되어 있다.

이제 버튼을 클릭했을때 동작하는 코드를 작성해보자.


**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {  
  
    public static final String VIDEO_URL = "https://sites.google.com/site/ubiaccessmobile/sample_video.mp4";  
    VideoView videoView;  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        videoView = findViewById(R.id.videoView);  
  
        MediaController mc = new MediaController(this);  
        videoView.setMediaController(mc);   // Video View에 MediaController 설정하기  
  
	    Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener(){  
            @Override  
		    public void onClick(View v){  
                videoView.setVideoURI(Uri.parse(VIDEO_URL));    // VideoView에 재생할 대상 설정하고 재생 시작  
			    videoView.requestFocus();  
                videoView.start();  
            }  
        });  
    }  
}

```

**이 앱도 인터넷에서 파일을 받아 오기 때문에 INTERNET 권한이 필요하다.**

**AndroidManifest.xml 파일을 열고 INTERNET 권한을 추가한 후**

**\<application> 태그에 속성을 하나 더 추가한다.**

<br>

**AndroidManifest.xml**

```xml
<uses-permission android:name="android.permission.INTERNET"/>  
  
<application  
  android:usesCleartextTraffic="true"

```


---
### 결과

<img width="478" alt="스크린샷 2022-02-02 오후 8 23 43" src="https://user-images.githubusercontent.com/54762273/152144781-bd2abb7e-d5ce-4a51-acfc-3ed7df0062c1.png">


**동영상을 좀 더 세밀하게 제어하고 싶을때는 미디어 플레이어 객체를 사용하면 된다.**