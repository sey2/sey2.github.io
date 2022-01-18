---
title: "트윈 애니메이션"
author: seyoung
date: '2022-01-18 01:13:00 +0900'
categories: Android Animation
tags: [android,animation,tweend]
math: true
mermaid: true
---
## Tweened Animation

안드로이드는 애니메이션을 간편하게 적용할 수 있는 여러가지 방법을 제공한다.

그중에서 트윈 애니메이션(Tweened Animation)이 가장 간단하면서 일반적인 방법으로 사용된다.

이는 이동, 확대/축소, 회전과 같이 일정한 패턴으로 움직이는 애니메이션을 구현할 때 사용한다.

애니메이션이 어떻게 동작할지 정의한 정보는 XML로 만든다.

이렇게 만든 XML 정보는 자바 소스에서 애니메이션 객체로 로딩한 후 뷰 객체의 

`startAnimation` 메서드를 사용해서 애니메이션을 동작하게 만들 수 있다.

순서를 정리하자면 아래와 같다.

**1. 애니메이션이 어떻게 동작하는지를 XML로 정의한다.**

**2. XML을 자바 파일로 로딩하여 애니메이션 객체로 만든다.**

**3. 뷰에 애니메이션을 적용하여 동작 시킨다.**

동작시킬 수 있는 트윈 애니메이션의 대상과 애니메이션 효과는 아래와 같다.

<br>

|구분  | 이름 | 설명|
|:---:|:---:|:---:|
| 대상 | 뷰 | View는 위젯이나 레이아웃을 모두 포함한다. <br> 예를 들어, 텍스트 뷰나 리니어 레이아웃에 애니메이션을 적용 시킬 수 있다.  |
| 대상   | 그리기 객체|다양한 Drawable에 애니메이션을 적용시킬 수 있다.<br> ShapeDrawable은 캔버스에 그릴 도형을 지정할 수 있으며,<br> BitmapDrawable은 비트맵 이미지를 지정할 수 있다.  |
| 효과  | 위치 이동  |translate로 정의한 액션은 대상의 위치를 이동시키는 데 사용하는 효과이다. |
| 효과   |확대/축소 | Scale로 정의한 액션은 대상의 크기를 키우거나 줄이는데 사용되는 효과이다.|
| 효과  | 회전| Rotate로 정의한 액션은 대상을 회전 시키는데 사용되는 효과이다.
|효과| 투명도 | Alpha로 정의한 액션은 대상의 투명도를 조절하는데 사용되는 효과이다. |

<br>

트윈 애니메이션의 액션(Action) 정보는 XML 리소스로 정의하거나 소스 코드에서 직접 객체로 만들 수 있다.

**XML 리소스로 정의할 때는 /app/res/anim 폴더의 밑에 확장자를 xml로 해야한다.**

이렇게 하면 애니메이션 액션 정의는 빌드할 때 컴파일 되어 설치 파일에 포함된다. 

---
### 애니메이션 적용해보기 

애니메이션 액션 정보를 만들기 위해 /app/res 폴더안에 admin 폴더를 생성한다.


![1](https://user-images.githubusercontent.com/54762273/149960066-5d836a0a-f9f8-446d-b98c-fe08df6893e1.PNG)

폴더안에 scale.xml 파일을 만든 후 아래와 같이 대상을 두배로 확대하는 스케일 액션을 정의하자.


**scale.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <scale
        android:duration="2500"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fromXScale="1.0"
        android:fromYScale="1.0"
        android:toXScale="2.0"
        android:toYScale="2.0"
        />
    
    <scale
        android:startOffset="2500"
        android:duration="2500"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fromXScale="1.0"
        android:fromYScale="1.0"
        android:toXScale="0.5"
        android:toYScale="0.5"

        />
</set>
```
시작 시간은 `startOffset`이고 지정하지 않으면 애니메이션은 바로 시작한다.

`duration`은 애니메이션이 지속되는 시간이고 단위는 ms이다.

scale 태그는 대상을 확대하거나 축소할 때 사용되는데, 크기를 변경하려는 축의 정보는

X축과 Y축에 대해 `pivotX`와`pivotY`로 지정한다.

`fromXScale`과`fromYScale`은 시작할 때의 확대/축소 비율이고, `toXScale`과`toYScale`은 

끝날 때의 확대/축소 비율이다. 여기서는 1.0으로 시작하여 2.0으로 끝나므로 원래 크기에서 2배

크기로 확대되는 애니메이션이 작동한다.

그 후 버튼을 추가 한 후 아래와 같이 애니메이션을 적용 시키는 코드를 작성하자.
<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {

                // 라소스에 정의한 애니메이션 액션 로딩
                Animation anim =
                        AnimationUtils.loadAnimation(getApplicationContext(), R.anim.scale);
                // 뷰의 애니메이션 시작
                view.startAnimation(anim);
            }
        });
    }
}
```

XML 리소스에 정의된 애니메이션 액션 정보를 로딩하기 위해 `AnimationUtils` 클래스의 `loadAnimation` 
메서드를 사용한다.

`loadAnimation` 메서드는 아래와 같은 형태이다.

```java
public static Animation loadAnimation(Context context, int id)
```

두번째 매개 변수 id는 XML 리소스에 정의된 애니메이션 액션의 id 값이다.

---

**결과**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/149967750-98bae45c-395d-4549-994b-6f9f7f0b58ea.gif)

---

## 화면이 사용자에게 보이는 시점에 애니메이션 시작하는 방법

화면이 사용자에게 보이자마자 애니메이션을 시작하고 싶다면 애니메이션 시작점은

`onWindowFocusChanged` 메서드가 호출되는 시점, 즉 윈도우가 포커스를 받는 시점이어야 한다.

`onWindowFocusChanged` 메서드 내에서 파라미터로 전달되는 `hasFocus` 변수의 값이 true일 경우에

각각의 애니메이션 객체에 대해 `start` 메서드를 호출함으로써 애니메이션이 시작되도록 하면 된다.

윈도우가 다른 윈도우에 의해 가려지거나 할 때는 hasFocus 값이 false가 되므로 애니메이션 객체의

rest 메서드를 호출하여 초기 상태로 되돌릴 수 있다.

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    public void onWindowFocusChanged(boolean hasFocus){
        super.onWindowFocusChanged(hasFocus);
        Button button = findViewById(R.id.button);

        Animation anim =
                AnimationUtils.loadAnimation(getApplicationContext(), R.anim.scale);

        if(hasFocus == true) 
            button.startAnimation(anim);
        else if(hasFocus == false)
            anim.reset();

    }
}
```

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/149981950-25906eca-14de-4383-a1c4-d49452915d74.gif)


위 처럼 어플을 키자마자 애니메이션이 작동한다.

|메서드 |설명  |
|:--:|:--:|
|public void onAnimationStart(Animation animation) | 애니메이션이 시작되기 전에 호출된다. |
|public void onAnimationEnd(Animation animation)| 애니메이션이 끝났을 때 호출된다.|
|public void onAnimationRepeat(Animation animation)| 애니메이션이 반복될 때 호출된다.| 

