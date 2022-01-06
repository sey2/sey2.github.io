---
title: "Gesture Event"
author: seyoung
date: '2022-01-06 21:05:00 +0900'
categories: Android Event
tags: [andorid,event,gesture]
math: true
mermaid: true
---

```java
public class MainActivity extends AppCompatActivity {
    TextView textView;

    GestureDetector detector;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        View view = findViewById(R.id.view);
        view.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View view, MotionEvent motionEvent) {
                int action = motionEvent.getAction();
                float curX = motionEvent.getX();
                float curY = motionEvent.getY();

                if (action == MotionEvent.ACTION_DOWN) {
                    println("손가락 눌림 : " + curX + ", " + curY);
                } else if (action == MotionEvent.ACTION_MOVE) {
                    println("손가락 움직임 : " + curX + ", " + curY);
                } else if (action == MotionEvent.ACTION_UP) {
                    println("손가락 뗌 : " + curX + ", " + curY);
                }

                return true;
            }
        });

        detector = new GestureDetector(this, new GestureDetector.OnGestureListener() {
            @Override
            public boolean onDown(MotionEvent motionEvent) {
                println("onDown() 호출됨.");
                return true;
            }

            @Override
            public void onShowPress(MotionEvent motionEvent) {
                println("onShowPress() 호출됨.");
            }

            @Override
            public boolean onSingleTapUp(MotionEvent motionEvent) {
                println("onSingleTapUp() 호출됨.");
                return true;
            }

            @Override
            public boolean onScroll(MotionEvent motionEvent, MotionEvent motionEvent1, float v, float v1) {
                println("onScroll() 호출됨 : " + v + ", " + v1);
                return true;
            }

            @Override
            public void onLongPress(MotionEvent motionEvent) {
                println("onLongPress() 호출됨.");
            }

            @Override
            public boolean onFling(MotionEvent motionEvent, MotionEvent motionEvent1, float v, float v1) {
                println("onFling() 호출됨 : " + v + ", " + v1);
                return true;
            }
        });

        View view2 = findViewById(R.id.view2);
        view2.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View view, MotionEvent motionEvent) {
                detector.onTouchEvent(motionEvent);
                return true;
            }
        });


    }

    public void println(String data) {
        textView.append(data + "\n");
    }

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            Toast.makeText(this, "시스템 [BACK] 버튼이 눌렸습니다.", Toast.LENGTH_LONG).show();

            return true;
        }

        return false;
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

|메서드|이벤트 유형  |
|:--:|:--:|
|onDown() |  화면이 눌림 |
|onShowPress() |  화면이 눌렸다 떼어짐|
|onSingleTapUp() |  화면이 한 손가락으로 눌렸다 떼어짐 |
|onSingleTapConfirmed() |  화면이 한 손가락으로 눌림|
|onDoubleTap()|   화면이 두 손가락으로 눌림 |
|onScroll() |  화면이 눌린 채 일정 속도와 방향으로 움직임 |
|onFling() |   화면이 눌린 채 가속도를 붙여 손가락을 움직였다 뗌|
|onLongPress() |   화면이 오랫동안 눌림|


![1](https://user-images.githubusercontent.com/54762273/148380565-141ab838-195d-47ab-846d-1f5d3d0bd1c0.PNG)

