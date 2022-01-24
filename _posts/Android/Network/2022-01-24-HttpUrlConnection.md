---
title: "HttpURLConnection"
author: seyoung
date: '2022-01-24 11:20:00 +0900'
categories: Android Network
tags: [android,network,http,url]
math: true
mermaid: true
---

##  HttpURLConnection으로 웹 서버 통신하기

안드로이드 어플리케이션이 서버와 통신하기 위한 방법에는 **HTTP통신과 Socket 통신 2가지가 있다.**

우리는 주로 DB에 존재하는 데이터를 가져오기 위해 서버 통신을 한다.

하지만 안드로이드의 특성상 외부 DB에 직접 접근할 수 가 없도록 되어있어 중간 매체인 **WEB**을 활용해야한다.

![1](https://user-images.githubusercontent.com/54762273/150799413-cb6ee2fb-5e33-4ac1-ab1b-3df4af0e9ec5.PNG)

자신의 DB와 WEB에서 데이터를 가져오기 위해선 호스팅이나 개인서버를 구축한 후, 

자신이 만든 **WEB**문서가 포함된 로컬호스트 주소에 접속해서 데이터를 얻어와야한다.

**또한 안드로이드에서 HTTP통신으로 서버와 통신을 할 때 AsyncTask(비동기)로 통신을 해야한다.**

왜냐하면 안드로이드의 메인쓰레드는 전부 UI 관련 처리를 위해 사용되기 때문에,

**비동기 방식을 사용하지 않고 개발자가 임의로 HTTPURLConnection을 하게되면 Runtime  Exception, **

**android.os.NetworkOnMainThreadException이 뜨고 ANR(Android  Not  Responding) 상황에 놓이게 된다.**

HTTP로 웹 서버에 접속하는 방법도 소켓의 경우와 마찬가지로 표준 자바 방식을 그대로 사용할 수 있다.

자바에서 HTTP 클라이언트를 만드는 가장 간단한 방법은 **URL 객체를 만들고 openConnection**

**메서드를 호출하여 HttpURLConnection 객체를 만드는 것이다.**

```java
public URLConnection openConnection()
```

URL 객체에 들어 있는 문자열이 "http://"를 포함하면 HTTP 연결을 위한 객체를 만들게 되므로 openConnection

메서드가 반환하는 URLConnection 객체를 HttpURLConnectoin으로 형 변환하여 사용할 수 있다.

**HttpURLConnection에는 POST와 GET 2가지 방식이 존재한다.**

먼저 POST의 경우에는 앱에서 URI를 통해 요청하게 되면 리소스를 생성하게 되는 경우이며, 
(서버의 상태나 데이터를 변경시킬 때 사용)

GET은 URL을 통해 접속해 리소스 값을 조회하는 행위이다. (서버로부터 정보 조회)

또한 리소스를 조회하고 해당 도큐먼트에 대한 자세한 정보도 가져온다.

POST의 경우에는 정상적으로 작동이 된다면 리소스 생성에 대한 응답 코드를 보낼 것이며,

GET의 경우에는 해당 도큐먼트(ex. 그림파일, DB, etc...)에 대한 정보를 불러오게 됨으로

두 가지 방식에는 약간의 차이가 존재하게 된다. 

따라서 POST와 GET에 대한 메소드를 만드는데 약간의 차이점이 존재하게 된다.

---
### 예제

**activity.main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPersonName"
        android:text="사이트 주소 입력" />

    <Button
        android:id="@+id/button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="요청하기" />

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical" >

            <TextView
                android:id="@+id/textView"
                android:layout_width="match_parent"
                android:layout_height="wrap_content" />
        </LinearLayout>
    </ScrollView>

</LinearLayout>
```

<br>

**MainActivity.java**
```java

public class MainActivity extends AppCompatActivity {

    EditText editText;
    TextView textView;

    Handler handler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        editText = findViewById(R.id.editText);
        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v){
                final String urlStr = editText.getText().toString();

                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        request(urlStr);
                    }
                }).start();
            }
        });
    }

    public void request(String urlStr){
        StringBuilder output = new StringBuilder();

        try{
            URL url = new URL(urlStr);

            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            if(conn != null){
                conn.setConnectTimeout(10000); // 웹 페이지 연결 대기 시간 최대 10초
                conn.setRequestMethod("GET"); // GET/POST 방식 설정
                conn.setDoInput(true);  	// InputStream으로 읽겠다고 설정

                int resCode = conn.getResponseCode();	// 응답 코드를 받아옴
                BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
                String line = null;
                while(true){
                    line = reader.readLine();

                    if(line == null)
                        break;
                    output.append(line + "\n");
                }
                reader.close();
                conn.disconnect();
            }
        }catch(Exception e){
            println("예외 발생함: " + e.toString());
        }

        println("응답-> " + output.toString());
    }

    public void println(final String data){
        handler.post(new Runnable() {
            @Override
            public void run() {
                textView.append(data + "\n");
            }
        });

    }
```

<br>

인터넷 권한을 사용하므로 Manifest 파일에 권한과 속성을 하나 추가해주자.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.techtown.myapplication">

    <uses-permission android:name="android.permission.INTERNET"/>

    <application
        android:usesCleartextTraffic="true"
```

---
**결과**

![1](https://user-images.githubusercontent.com/54762273/150798356-3f6e8be3-02e6-4b44-9953-12bc4cfd5bf0.PNG)
