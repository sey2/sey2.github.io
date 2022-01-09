---
title: "Touch Event"
author: seyoung
date: '2022-01-06 20:00:00 +0900'
categories: Android Event
tags: [android,event,touch,listener]
math: true
mermaid: true
---


## 터치 이벤트 처리하기 

가장 두개의 뷰를 가장 상단에 배치,
 
스크롤 뷰 안에 텍스트 뷰를 넣어서 가장 위에 있는 뷰를 터치하면 이벤트가 

발생하게 처리 해보았다. 밑에 있는 뷰의 이벤트도 처리 하고 싶으면,

View view = findViewById(R.id.view2);로 뷰 객체를 불러와서

불러온 객체에 리스너 등록 처리를 해주면 된다.


```java
public class MainActivity extends AppCompatActivity  {
    TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView); //Text 뷰 객체 불러오기
        View view = findViewById(R.id.view);    // View 객체 불러오기
        view.setOnTouchListener(new MyListener());
    }

    class MyListener implements View.OnTouchListener {
        @SuppressLint("ClickableViewAccessibility")
        @Override
        public boolean onTouch(View view, MotionEvent motionEvent){
            // 발생한 이벤트 가져옴
            // 이벤트는 상수 값으로 정의 되어 있음
            int action = motionEvent.getAction();
            // 이벤트가 발생한 좌표 얻어오기
            float curX = motionEvent.getX();
            float curY = motionEvent.getY();

            if (action == MotionEvent.ACTION_DOWN)
                println("손가락 눌림 : " + curX + ", " + curY);
            else if (action == MotionEvent.ACTION_MOVE)
                println("손가락 움직임: " + curX + ", " + curY);
            else if (action == MotionEvent.ACTION_UP)
                println("손가락 뗌 : "+ curX + ", " +  curY);

            return true;
        }

        public void println(String data){
            textView.append(data+"\n");
        }
    }
}

```


```xml 

<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  xmlns:app="http://schemas.android.com/apk/res-auto"  
  xmlns:tools="http://schemas.android.com/tools"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:orientation="vertical"  
  tools:context=".MainActivity">  
  
 <View  
  android:id="@+id/view"  
  android:layout_width="wrap_content"  
  android:layout_height="0dp"  
  android:layout_weight="1"  
  android:background="#8BC34A" />  
  
 <View  
  android:id="@+id/view2"  
  android:layout_width="wrap_content"  
  android:layout_height="0dp"  
  android:layout_weight="1"  
  android:background="#FF9800" />  
  
 <ScrollView  
  android:layout_width="match_parent"  
  android:layout_height="0dp"  
  android:layout_weight="1">  
  
 <LinearLayout  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:orientation="vertical">  
  
 <TextView  
  android:id="@+id/textView"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent" />  
 </LinearLayout>  
 </ScrollView>  
</LinearLayout>

```

> MotionEvent.ACTION_DOWN -> 손가락이 눌렸을 때

> MotionEvent.ACTION_MOVE -> 손가락이 눌린 상태로 움직일 때

> MotionEvent.ACTION_UP -> 손가락이 떼졌을 때 


![1](https://user-images.githubusercontent.com/54762273/148373351-8015036f-9ea7-4568-abed-13997f4d850e.PNG)
 