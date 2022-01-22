---
title: "Thread and Handler"
author: seyoung
date: '2022-01-20 00:16:00 +0900'
categories: Android Thread
tags: [android,thread,handler,runnable]
math: true
mermaid: true
---

## Thread

새로운 프로젝트를 만들면 자동으로 생성되는 메인 액티비티는 앱이 실행될 때 하나의 프로세스에서
처리된다.

따라서 메인 액티비티 내에서 이벤트를 처리하거나 특정 메서드를 정의하여 기능을 구현 할 때도

같은 프로레스 안에서 실행된다. 같은 프로세스 안에서 일련의 기능이 순서대로 실행될 때 대부분은

문제가 없지만, 대기시간이 길어지는 네트워크 요청 등의 기능을 수행할 때는 화면에 보이는 UI도

멈춤 상태로 있게 되는 문제가 생길 수 있다.

이런 문제를 해결하려면 멀티 스레드 방식을 사용하면된다.

스레드란 동시 수행이 가능한 작업 단위이며, 현재 수행 중인 작업 이외의 기능을 동시에 처리할 때 새로운 
스레드를 만들어 처리한다.

이런 멀티 스레드 방식은 메모리 리소스를 공유해서 효울적인 처리가 가능해진다.

하지만 동시에 리소스(자원)에 접근할 때 DeadLock이 발생하여 시스템이 비정상적으로 동작할 수 있다.
<br>

**앱 처음 실행 시**

![1](https://user-images.githubusercontent.com/54762273/150146946-dc0db9bf-12a1-405a-8723-81a4b10b5126.PNG)


**별도의 스레드 생성시**

![1](https://user-images.githubusercontent.com/54762273/150147269-dd469136-7a11-4c89-b7b5-6782eadc035a.PNG)

--- 

지연 시간이 길어질 수 있는 앱이라면 오랜 시간 작업을 수행하는 코드를 별도로 분리한 후

UI에 응답하는 방식을 사용한다. 아래는 안드로이드가 제공하는 두 가지 시나리오이다.

|구분  | 시나리오 |
|:--:|:--:|
|(1) 서비스 사용하기  | 백그라운드 작업은 서비스로 실행하고 사용자에게는 알림 서비스로 알려준다.<br> 만약 메인 액티비티로 결과 값을 전달하고 이를 이용해서 다른 작업을 수행하고자 <br> 한다면 브로드 캐스팅으로 결과 값을 전달할 수 있다. |
|(2) 스레드 사용하기| 스레드는 같은 프로세스 안에 있기 때문에 작업 수행의 결과를 바로 처리할 수 있다.<br> 그러나 UI 객체는 직접 접근할 수 없으므로 핸들러(Handler) 객체를 사용한다.|

안드로이드에서 UI 처리할 때 사용되는 기본 스레드를 **메인 스레드**라고 부른다.

메인 스레드에서 이미 UI에 접근하고 있으므로 새로 생성한 다른 스레드에서는 Handler 객체를 사용해서

메시지를 전달함으로써 메인 스레드에서 처리하도록 만들 수 있다.

---

### 스레드 사용하기

안드로이드에서는 표준 자바의 스레드를 그대로 사용할 수 있다.


**MainActiviy.java**

```java

public class MainActivity extends AppCompatActivity {
    int value = 0;

    TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick (View v){
                // 스레드 객체 생성 후 실행
                BackgroundThread thread = new BackgroundThread();
                thread.start();
            }
        });
    }

    class BackgroundThread extends Thread{
        public void run(){
            for(int i=0; i<100; i++){
                try{
                    sleep(1000);
                }catch(Exception e){}
            }
            value += 1;
            textView.setText("Value 값: " + value);
            Log.d("Thread" , "value : " + value);
        }
    }

```

하지만 이렇게 하면 UI 객체를 직접 접근하지 못하기 때문에 Logcat 창에서 오류 메시지가 나오고

앱이 제대로 실행되지 않는다. 

그 이유는 우리가 직접 만드는 스레드 객체에서는 UI객체에 접근 할 수 없기 때문이다.

**이 문제는 핸들러를 사용하면 해결 가능하다.**

--- 

### 핸들러로 메시지 전송하기 

앱을 실행할 때 프로세스가 만들어지면 그 안에 메인 스레드가 함께 만들어지고,

최상위에서 관리되는 앱 구성 요소인 Activity, BroadCast Receiver 등과 새로 만들어지는 윈도우를 관리하기 위한 Message Queue를 실행한다.

Message Queue를 사용하면 순차적으로 코드를 수행할 수 있다.

**이렇게 Message Queue로 메인 스레드에서 처리할 메시지를 전달하는 역할을 핸들러 클래스가 담당한다.**

핸들러를 이용하면 특정 메시지가 미래의 어떤 시점에 스케줄링 된다.


**핸들러를 사용할 때 필요한 세 가지 단계**
![1](https://user-images.githubusercontent.com/54762273/150152204-51f34ebb-95fb-4174-b5c6-8889bf5feb95.PNG)

> 출처 Do it! 안드로이드 프로그래밍 

<br>

| 메소드 | 설명 |
|:--:|:--:|
|obtainMessage() | 메시지 큐에 있는 메시지 객체를 하나 참조 할 수 있다. |
|sendMessage()| 메시지 객체에 필요 정보를 넣은 후 메시지 큐로 전달 할 수 있다.|
|HandleMessage()| 이 메서드 안에 있는 코드가 메인 스레드에서 수행된다.|

자 인제 사용 예시를 보자.
<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {
    int value = 0;

    TextView textView;
    MainHandler handler;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick (View v){
                // 스레드 객체 생성 후 실행
                BackgroundThread thread = new BackgroundThread();
                thread.start();
            }
        });
        handler = new MainHandler();
    }

    class BackgroundThread extends Thread{
        public void run(){
            for(int i=0; i<100; i++){
                try{
                    sleep(1000);
                }catch(Exception e){}
                value += 1;
                Log.d("Thread" , "value : " + value);

                Message message = handler.obtainMessage();
                Bundle bundle = new Bundle();
                bundle.putInt("value", value);
                message.setData(bundle);    // message에 번들 집어 넣기

                handler.sendMessage(message); // 핸들러로 메시지 객체 보내기
            }
        }
    }

    class MainHandler extends Handler {

        @Override
        public void handleMessage(Message msg){
            super.handleMessage(msg);

            // 번들은 Map 형태로 생긴 데이터 묶음 (key, value)
            Bundle bundle = msg.getData();
            int value = bundle.getInt("value");
            textView.setText("value 값: " + value);
        }
    }
```
---

**결과**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/150156533-b878426f-db66-43f1-acf9-3def61b9c142.gif)

---

### Runnable 객체 실행하기

위 방법이 가장 일반적이지만 코드가 복잡해 보이는 단점이 있다. 

조금 더 간단한 방법으로 메인 스레드에서 실행 시킬 수 있다.

핸들러 클래스는 메시지 전송 방법 이외에 Runnable 객체를 실행시킬 수 있는 방법을 제공하는데

Runnable 객체를 핸들러의 post 메서드로 전달해주면 이 객체에 정의된 run 메서드 안의 코드들은

메인 스레드에서 실행된다.

예제를 봐보자 ! 

<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {
    int value = 0;

    TextView textView;
    Handler handler = new Handler(); // API의 기본 핸들러 객체 생성

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick (View v){
                // 스레드 객체 생성 후 실행
                BackgroundThread thread = new BackgroundThread();
                thread.start();
            }
        });
    }

    class BackgroundThread extends Thread{
        public void run(){
            for(int i=0; i<100; i++){
                try{
                    sleep(1000);
                }catch(Exception e){}
                value += 1;
                Log.d("Thread" , "value : " + value);

                handler.post(new Runnable() {   // 핸들러의  post 메서드 호출
                    @Override
                    public void run() {
                        textView.setText("value 값: " + value);
                    }
                });
            }
        }
    }
}
```

이렇게 하면 메시지 처리를 위해 새로 정의했던  MainHandler 클래스는 이제 더 이상 필요가 없으므로

일반적으로 사용하는 Handler 객체를 생성한다.

Handler 객체를 만들어 변수에 할당해두면 이 객체의 post 메서드를 호출 할 수 있다. 

이 코드에서 보는 것처럼 post 메서드로 전달되는 Runnable 객체는 스레드의 작업 결과물로 만들어지는

데이터를 처리해야 한다. 따라서 결과물을 화면에 보여주어야 하는 부분이 있을 경우 new 연산자로

Runnable 인터페이스를 구현하는 새로운 객체를 만들어 사용하는 것이 일반적이다.