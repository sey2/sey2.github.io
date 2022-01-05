---
title: "상대 레이아웃"
author: seyoung
date: '2022-01-04 18:07:00 +0900'
categories: Android Layout_And_View
tags: [android,layout,imgae,button]
math: true
mermaid: true
---

## 이미지 뷰와 이미지 버튼

이미지 뷰와 이미지 버튼의 차이점은 이미지를 버튼처럼 사용 할 수 있는지의 여부이다.

이미지 뷰에 이미지를 보여주려면 먼저 /app/res/drawable 폴더에 이미지 파일을 넣은 후

app:srcCompat 속성 값을 다음과 같은 방법으로 지정하면 된다.

> **@drawable/이미지 파일명**

**여기서 주의할 점은 이미지 파일명은 확장자를 제외하고 작성 해야한다!**

이렇게 이미지 파일을 지정하는 방식을 '이미지 리소스 지정 방식'이라고 한다.

왜냐하면 이미지 파일은 res 폴더 안에 들어 있는 리소스 중의 하나이기 때문이다.

이 방식 이외에도 이미지 파일을 소스 코드에서 직접 로딩하여 비트맵으로 만든 후 설정하는 방법도 있다.

---

### drawable 폴더 제대로 사용하기 

|해상도  | 폴더 이름 |
|:--:|:--:|
|초고해상도  | /app/res/drawable-xhdpi<br>/app/res/drawable-xxhdpi<br>/app/res/drawable-xxxhdpi |
|고해상도 |/app/res/drawable-hdpi |
|중간 해상도 |/app/res/drawable-mdpi|
|저 해상도| /app/res/drawable-Idpi|

**drawable 폴더에 이미지를 그냥 넣으면 일반적으로 사용되는 이미지로 인식한다.**

하지만 위의 표에 있는대로 해상도에 맞게 넣으면 화면에 자동으로 적용된다.



---

### maxWidth, maxHeight

두 속성은 이미지가 표시되는 최대 폭, 높이를 설정한다.

이 속성을 설정하지 않으면 원본 이미지 크기 그대로 나타난다.


--- 


### tint

tint 속성은 이미지 뷰에 보이는 이미지의 색상을 설정할 수 있다.

색상은 #AARRGGBB 포맷으로 적용하면 된다.


----

### scaleType

scaleType 속성은 이미지 뷰의 크기에 맞게 원본 이미지의 크기를 자동으로 늘리거나 줄여서 

보여줄 때 사용한다.