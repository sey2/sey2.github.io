---
title: "트윈 애니메이션 - 여러가지 액션 효과"
author: seyoung
date: '2022-01-19 01:31:00 +0900'
categories: Android Animation
tags: [android,animation,tweend]
math: true
mermaid: true
---

## 트윈 애니메이션 - 여러가지 액션 효과

### 위치이동

한 곳에서 다른 곳으로 부드럽게 움직이는 효과를 주기 위해서는 translate 태그를 사용하면 된다.

**/app/res/anim/text.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromXDelta="0%p"
    android:toXDelta="-100%p"
    android:duration="10000"
    android:repeatCount="-1"
    android:fillAfter="true"
    />
```

`fromXDelta`, `fromYDelta` 는 시작 위치이고 `toXDelta`, `toYDelta` 는 종료 위치이다. 

`fromXDelta`가 0%이므로 원래 위치의 X좌표에서 시작하고 `toXDelta`가 음수 값이므로 왼쪽으로

이동하게 된다. `duration`은 지속 시간이고 10초 동안 애니메이션이 작동한다.

`repeatCount`는 애니메이션 반복 할  횟 수이고 -1이므로 무한 반복된다. 

`fillAfter` 속성이 false이면 애니메이션이 끝나고 원래 위치로 돌아가고 true면 원래 위치로 돌아오지 않는다. 

---

**결과**
![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/149971518-bd105bf4-9f8d-4ad5-b08c-ee62adc93046.gif)

---

### 회전 효과 주기

회전 효과를 주고 싶으면 roate 태그를 이용하면 된다.

**/app/res/anim/text.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<rotate  xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromDegrees="0"
    android:toDegrees="360"
    android:pivotX="50%"
    android:pivotY="50%"
    android:duration="5000"
    />
```

`fromDegress`는 시작 각도, `toDegrees`는 종료 각도이고 위처럼 0부터 360으로 설정하면 한 바퀴 회전한다.

반대로 회전 시키고 싶으면 `fromDegress` 값을  0, `toDegress`값은 -360으로 설정하면 된다.

**여기서 pivotX, pivotY을 설정 하지 않으면 회전의 중심축이 (0,0)으로 설정된다.** 아래 예시를 보자.

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/149973445-cdbc7f31-117e-4f18-bc4e-2f893baeacf0.gif)
 
 회전하는 축을 중앙으로 잡고 싶으면 `pivotX`와 `pivotY`의 값을 50%로 설정해주면 된다.
 
---

**결과**
![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/149973760-4b22ecf9-8c85-462e-830d-7c0e4bf81b1f.gif)

---

### 투명도 액션 효과 주기

알파 값을 이용한 투명도 변환은 대상을 천천히 보이게 하거나 보이지 않게 하고 싶을 때 또는

하나의 뷰 위에 다른 뷰를 겹쳐 보이게 할 때 사용한다.

**/app/res/anim/text.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<alpha  xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromAlpha="0.0"
    android:toAlpha="1.0"
    android:duration="5000"
    />
```

알파 값의 범위는 0.0 ~ 1.0 이고 0.0은 완전히 투명 상태이다. (객체가 아예 보이지 않음) 

반대로 1은 완전히 보이는 상태이다. (투명 효과가 적용되지 않음)

---

**결과**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/149974674-31292673-eb57-4c70-8e80-858bf0bea1fa.gif)

---
### 애니메이션 확대/축소

<a href="https://sey2.github.io/posts/Tweened_Animation/#%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98-%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0">scale 예제</a>

---

### 애니메이션 속도 조절하기

**애니메이션 효과를 빠르거나 느리게 주고 싶으면 Interpolator를 사용하면 된다.**

|속성  |설명 |
|:---:|:---:|
|accelerate_interpolator  | 애니메이션 효과를 점점 빠르게 나타나도록 만든다.|
|decelerate_interpolator | 애니메이션 효과를 점점 느리게 나타나도록 만든다.|
|accelerate_decelerate_interpolator|애니메이션 효과를 점점 빠르다가 느리게 나타나도록 만든다.|
|anticipate_interpolator| 애니메이션 효과를 시작 위치에서 조금 뒤로 당겼다가 <br>시작하도록 만든다.|
|overshoot_interpolator|애니메이션 효과를 종료 위치에서 조금 지나쳤다가 종료되도록 만든다.|
|anticipate_overshoot_interpolator|애니메이션 효과를 시작 위치에서 조금 뒤로 당겼다가<br> 시작한 후 종료 위치에서 조금 지나쳤다가 종료되도록 만든다.|
|bounce_interpolator| 애니메이션 효과를 종료 위치에서 튀도록 만든다.|

예제

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/bounce_interpolator">
    <alpha
    android:fromAlpha="0.0"
    android:toAlpha="1.0"
    android:duration="5000"/>
</set>
```
---

아래 표에 나온 것 처럼 자바 코드에서 new 연산자로 직접 만들 수도 있다.

|기능  | 객체 |
|:--:|:--:|
|위치 이동  | **\<translate> -> TranslateAnimation**|
|회전|**\<rotate> -> RotateAnimation** |
|확대/축소| **\<scale> -> ScaleAnimation**|
|투명도|**\<alpha> -> AlphaAnimation**|
|애니메이션 집합| **\<set> -> AnimationSet**|


