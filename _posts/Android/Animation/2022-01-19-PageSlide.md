---
title: "Page Sliding"
author: seyoung
date: '2022-01-19 20:21:00 +0900'
categories: Android Animation
tags: [android,animation,slid,page,pageslide]
math: true
mermaid: true
---

## 페이지 슬라이딩

페이지 슬라이딩은 버튼을 눌렀을 때 보이지 않던 뷰가 슬라이딩 방식으로 나타나는 기능이다.

여러 뷰를 중첩으로 해두었다가 하나 씩 전환하면서 보여주는 방식에서 애니메이션을 적용 시킨 것이다.

대표적인 예시로 바로가기 메뉴가 있다.

![1](https://user-images.githubusercontent.com/54762273/150105797-5a90e914-96d8-44a2-9ff9-147d5d5cc1d9.png)

> [출처 Do it! Android 프로그래밍]

---

### 예시

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#3F51B5"
        android:orientation="vertical">

        <TextView
            android:id="@+id/textView"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="TextView" />

    </LinearLayout>

    <LinearLayout
        android:id="@+id/page"
        android:layout_width="200dp"
        android:layout_height="match_parent"
        android:background="#FFEB3B"
        android:orientation="vertical"
        android:visibility="gone">

        <TextView
            android:id="@+id/textView2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Area 1" />

        <TextView
            android:id="@+id/textView3"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="Area 2" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right|center_vertical"
        android:orientation="vertical">

        <Button
            android:id="@+id/button"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Button" />
    </LinearLayout>

</FrameLayout>
```

<br>

**res/anim/translate_right**

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:fromXDelta="0%p"
        android:toXDelta="100%p"
        android:duration="500"
        android:repeatCount="0"
        android:fillAfter="true"
        />
</set>
```
<br>

**res/anim/translate_left**

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:fromXDelta="100%p"
        android:toXDelta="0%p"
        android:duration="500"
        android:repeatCount="0"
        android:fillAfter="true"
        />
</set>
```

<br>

**MainActivity.java**

```java

public class MainActivity extends AppCompatActivity {
    boolean isPageOpen = false;

    Animation translateLeftAnim;
    Animation translateRightAnim;

    LinearLayout page;
    Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        page = findViewById(R.id.page);

        // 애니메이션 xml 로딩 
        translateLeftAnim = AnimationUtils.loadAnimation(this,R.anim.translate_left);
        translateRightAnim = AnimationUtils.loadAnimation(this, R.anim.translate_right);

        // 애니메이션 리스너 등록 (End, Start, Repeat) 사용하기 위함
        SlidingPageAnimationListener animListener = new SlidingPageAnimationListener();
        translateLeftAnim.setAnimationListener(animListener);
        translateRightAnim.setAnimationListener(animListener);
    
        // 버튼 리스너 등록
        button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View view){
                if(isPageOpen)  // 서브 페이지가 켜져 있으면 지우기
                    page.startAnimation(translateRightAnim);
                else{       // 서브 페이지를 보이게함 
                    page.setVisibility(View.VISIBLE);
                    page.startAnimation(translateLeftAnim);
                }
            }
        });
    }

    private class SlidingPageAnimationListener implements Animation.AnimationListener{

        public void onAnimationEnd(Animation animation){
            if(isPageOpen){
                page.setVisibility(View.INVISIBLE);
                button.setText("OPEN");
                isPageOpen = false;
            }else{
                button.setText("Close");
                isPageOpen = true;
            }
        }

        @Override
        public void onAnimationStart(Animation animation) {}

        @Override
        public void onAnimationRepeat(Animation animation) {}
    }
```

---

**결과**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/150111731-524e92ad-dd2c-463a-9ad0-bc37afa0c3ab.gif)

서브 페이지를 켜면 나오는 Area1 Area 2는 실제 앱을 만들 때 이 각각의 영역에 필요한 뷰를

넣어 화면을 구성할 수 있다. 