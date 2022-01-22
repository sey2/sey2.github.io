---
title: "Handler 지연 시간 (postDelayed) "
author: seyoung
date: '2022-01-22 22:44:00 +0900'
categories: Android Thread
tags: [android,thread,handler,runnable,postdelayed]
math: true
mermaid: true
---

## 일정 시간 후에 실행하기 

웹 서버와 같은 원격 서버에 접속한 후 웹페이지를 요청할 때 응답이 늦어지거나 응답이 없으면

앱이 대기하고 있는 상황이 지속되는 문제가 생긴다.

이런 경우에는 기본적으로 별도의 스레드를 만들어 처리하면 된다.

버튼을 클릭해 간단하게 접속 처리하는 경우에는 메인 스레드내에서 Thread.sleep()을 사용해 

UI의 멈춤 현상을 방지 할 수 있다. 

하지만 핸들러로 실행되는 코드는 메시지 큐를 통과하면서 순차적으로 실행이 되기 때문에

`postDelayed`을 이용해 지연시간을 주면 UI 객체들에게 영향을 주지 않게 된다. 

---

### 예제 

**MainActivity.java**
```java
public class MainActivity extends AppCompatActivity {

    TextView textView;
    Handler handler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                request();
            }
        });
    }

    private void request(){
        String title = "title";
        String message = "데이터를 요청하시겠습니까?";
        String titleButtonYes="예";
        String titleButtonNo="아니오";
        AlertDialog dialog = makeRequestDialog(title, message, titleButtonYes, titleButtonNo);
        dialog.show();

        textView.setText("대화상자 표시중..");
    }

    private AlertDialog makeRequestDialog(CharSequence title, CharSequence message,
                                          CharSequence titleButtonYes, CharSequence titleButtonNO){
        AlertDialog.Builder requestDialog = new AlertDialog.Builder(this);
        requestDialog.setTitle(title);
        requestDialog.setMessage(message);
        requestDialog.setPositiveButton(titleButtonYes, new DialogInterface.OnClickListener(){
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                textView.setText("5초 후에 결과 표시됨.");

                handler.postDelayed(new Runnable() {
                    @Override
                    public void run() {
                        textView.setText("요청 완료됨.");
                    }
                }, 5000);
            }
            });
        requestDialog.setNegativeButton(titleButtonNO, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {}
        });
        return requestDialog.create();
    }
```
---
**결과**
![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/150636859-d85cb00f-55eb-450d-83ec-e164025781da.gif)


---

**`postDelayed` 메서드를 사용할 때 주의할 점이 있다.**

아래 첫 번째 메서드를 1초후에 실행한 후 두 번째 메서드를 첫 번째 메서드가 끝나고 나서

3초뒤(실행 시간 기준 4초)에 실행하고 싶어서 아래처럼 코드를  짰다. 


```java
handler.postDelayed(new DelayMessage(countTime,flag), 1000);
handler.postDelayed(new DelayMessage(countTime,flag), 3000);
```

이렇게 하면 우리가 의도한대로 작동하지 않는다.

왜냐하면 위의 두 번째 `postDelayed`는 실행 시간 기준 3초뒤에 실행 되기 때문이다.

따라서 우리가 의도하고 싶은대로 작동하게 할려면 특정한 시간 기준으로 누적되는 시간으로 

적용해주어야 한다. 

아래 코드로 그 예를 확인해보자.




**MainActivity.java**
```java

public class MainActivity extends AppCompatActivity {

    TextView textView;
    Handler handler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                request();
            }
        });
    }

    private void request(){
        String title = "title";
        String message = "데이터를 요청하시겠습니까?";
        String titleButtonYes="예";
        String titleButtonNo="아니오";
        AlertDialog dialog = makeRequestDialog(title, message, titleButtonYes, titleButtonNo);
        dialog.show();

        textView.setText("대화상자 표시중..");
    }

    private AlertDialog makeRequestDialog(CharSequence title, CharSequence message,
                                          CharSequence titleButtonYes, CharSequence titleButtonNO){
        AlertDialog.Builder requestDialog = new AlertDialog.Builder(this);
        requestDialog.setTitle(title);
        requestDialog.setMessage(message);
        requestDialog.setPositiveButton(titleButtonYes, new DialogInterface.OnClickListener(){
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {
                int delay = 1000;
                boolean flag = true;
                for(int countTime = 5; countTime >= 0; countTime--) {
                    flag = (countTime!=0) ? true : false;
                    handler.postDelayed(new DelayMessage(countTime,flag), delay);
                    delay+=1000;
                }
            }
        });
        requestDialog.setNegativeButton(titleButtonNO, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialogInterface, int i) {}
        });
        return requestDialog.create();
    }

    public class DelayMessage implements Runnable{
        int time;
        boolean flag;

        public DelayMessage(int time,boolean flag){
            this.time = time;
            this.flag = flag;
        }
        @Override
        public void run() {
            if(flag == true)
                textView.setText(time + "초 후에 요청 됨");
            else if(flag == false)
                textView.setText("요청됨");
        }
    }
}
```

--- 

**결과**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/150636573-c563d80a-564d-42eb-9195-1da9a48e7f6e.gif)
