---
title: "음악 파일 재생하기"
author: seyoung
date: '2022-02-02 19:56:00 +0900'
categories: Android Multimedia
tags: [android,music ]
math: true
mermaid: true
---

## 음악 파일 재생하기

멀티미디어를 위해 제공하는 미디어 **API는 android.media** 패키지에 들어 있다.

그 안의 클래스들 중에서 핵심이 되는 것은 **MediaPlayer** 클래스이다.

**미디어 플레이어 클래스**는 음악 파일과 같은 오디오의 재생은 물론 동영상 재생까지 담당한다.

출시되는 단말에 따라 지원하는 음성/영상 코덱이 다르므로 재생할 수 있는 파일의 종류가 다를 수 있다. 하지만 기본적으로 제공되는 코덱만으로도 오디오와 동영상 재생을 할 수 있다.

오디오 파일을 재생하려면 대상을 지정해야 하는데 이때 사용되는 **데이터 소스 지정 방법**은 크게 세가지로 나눌 수 있다.

|구분 | 설명 |
|:--:|:--:|
|인터넷에 있는 파일 위치 지정  | 미디어가 있는 위치를 URL로 지정한다.  |
|프로젝트 파일에 포함한 후 위치 지정| 앱을 개발하여 배포하는 과정에서 프로젝트의 리소스 또는 애셋 폴더에 넣은 후 그 위치를 지정한다.|
|단말 SD 카드에 넣은 후 위치 지정| 단말에 넣어 둔 SD 카드에 파일을 넣은 후 그 위치를 지정한다.|

미디어플레이어로 음악 파일을 재생하는 과정은 다음 그림처럼 세 단계로 나눌 수 있다.


![Untitled Diagram drawio](https://user-images.githubusercontent.com/54762273/152134574-42394c50-3bdd-4a79-8e73-4e2900f05631.png)


1.**setDataSource 메서드로 URL을 지정한다.**
2. **prepare 메서드를 호출하여 재생을 준비한다.**
3. **start 메서드를 호출하여 음악 파일을 재생한다.**

---

**예제**

```java
public class MainActivity extends AppCompatActivity {  
  
    public static final String AUDIO_URL = "https://sites.google.com/site/ubiaccessmobile/sample_audio.mp3";;  
  
    MediaPlayer mediaPlayer;  
    int position = 0;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener(){  
            @Override  
		    public void onClick(View v){  
                playAudio(AUDIO_URL);  
                Toast.makeText(getApplicationContext(), "음악 파일 재생 시작됨.",Toast.LENGTH_LONG).show();  
            }  
        });  
  
        Button button2 = findViewById(R.id.button2);  
        button2.setOnClickListener(new View.OnClickListener() {  
            @Override  
		    public void onClick(View view){  
                if(mediaPlayer != null){  
                    mediaPlayer.stop();  
                    Toast.makeText(getApplicationContext(), "음악 파일 재생 중지됨.", Toast.LENGTH_LONG).show();  
                }  
            }  
        });  
  
        Button button3 = findViewById(R.id.button3);  
        button3.setOnClickListener(new View.OnClickListener(){  
            @Override  
		    public void onClick(View view){  
                if(mediaPlayer != null){  
                    position = mediaPlayer.getCurrentPosition();  
                    mediaPlayer.pause();  
                    Toast.makeText(getApplicationContext(), "음악 파일 재생 일시 정지됨.", Toast.LENGTH_LONG).show();  
                }  
            }  
        });  
  
        Button button4 = findViewById(R.id.button4);  
        button4.setOnClickListener(new View.OnClickListener(){  
            @Override  
		    public void onClick(View view){  
                if(mediaPlayer != null && !mediaPlayer.isPlaying()){  
                    mediaPlayer.start();  
                    mediaPlayer.seekTo(position);  
                    Toast.makeText(getApplicationContext(),"음악 파일 재생 재시작됨.", Toast.LENGTH_LONG).show();  
                }  
            }  
        });  
    }  
  
    private void playAudio(String url){  
        killMediaPlayer();  
        try{  
            /* MediaPlayer 객체 만들어 시작하기 */  
		    mediaPlayer = new MediaPlayer();  
            mediaPlayer.setDataSource(url);  
            mediaPlayer.prepare();  
            mediaPlayer.start();  
        }catch(Exception e){  
            e.printStackTrace();  
        }  
    }  
  
    protected void onDestroy(){  
        super.onDestroy();  
        killMediaPlayer();  
    }  
  
    private void killMediaPlayer(){  
        if (mediaPlayer != null){  
            try{  
                /* MediaPlayer 객체의 리소스 해체하기 */  
			     mediaPlayer.release();  
            }catch(Exception e){  
                e.printStackTrace();  
            }  
        }  
    }  
  
}
```

**MediaPlayer** 객체를 이용해 음악을 재생하는 `playAudio` 메서드의 구조를 보면 `killMediaPlayer` 메서드를 호출한 후 차례로 `setDataSource, prepare, start` 메서드를 호출하고 있다.

`killMediaPlayer` 메서드는 미디어플레이어 객체가 이미 리소스를 사용하고 있을 경우에 release 메서드를 호출하여 리소스 해체하는 역할을 한다.

이렇게 하는 이유는 미디어 플레이어를 앱 내에서 재사용하려면 기존에 사용하던 리소스를 먼저 해제 해주어야 한다.

재생을 중지하고 다시 시작하기 위해서는 중지한 지점의 위치를 알아야 하므로 일시정지 버튼을 눌렀을 때는 `getCurrentPosition` 메서드를 이용해 현 지점의 위치를 알아오고 재시작 버튼을 눌렀을 때는 `seekTo` 메서드로 중지 했을 때의 지점에서부터 재생하도록 만든다.

위의 예제는 인터넷의 다른 서버에 미리 올려둔 음악 파일을 사용하고 있다.

인터넷을 사용하기 때문에 INTERNET 권한이 필요하다. 

**따라서 AndroidManifest.xml` 파일을 열고 다음과 같이 INTERNET 권한을 추가한다.**

그리고 \<application> 태그에 속성을 하나 더 추가한다.

<br>

**AndroidManifest.xml**
```xml
<uses-permission android:name="android.permission.INTERNET"/>  
  
<application  
  android:usesCleartextTraffic="true"
```

---

### 결과

<img width="405" alt="스크린샷 2022-02-02 오후 7 53 14" src="https://user-images.githubusercontent.com/54762273/152140442-34f214da-6c10-4319-b578-74f44ed0061c.png">

미디어 플레이어의 stop 메서드로 재생을 중지했을 때 또 다른 작업을 수행하고 싶다면 `MediaPlayer.OnCompletionListener` 를 구현한 후 미디어 플레이어 객체에 등록하면 된다.

재생이 중지되었을 때 호출되는 메서드는 다음과 같다.

**API**

```java
public abstarct void onCompletoin (MediaPlayer mp)
```