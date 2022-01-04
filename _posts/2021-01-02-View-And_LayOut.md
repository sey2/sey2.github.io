---
title: "뷰와 레이아웃 "
author: seyoung
date: '2022-01-02 17:44:00 +0900'
categories: Android Layout
tags: [android,view,layout]
math: true
mermaid: true
---

## View 


|이름| 설명 |
|:--:|:--:|
|뷰(View)  |화면 구성 요소  |
|위젯  |사용자 눈에 보이면서 컨트롤 역할을 하는 뷰 |
|뷰 그룹| 뷰를 담고 있는 그릇|
|레이아웃| 뷰그룹 안에 뷰를 배치하는것|

## LayOut

레이아웃은 레이아웃이 담고 있는 위젯이 어디에 배치되어야 할지 정해줍니다. 
따라서 레이아웃 안에 레이아웃을 넣으면 각각의 레이아웃 안에서 버튼과 위젯의 위치를 잡을 수 있습니다.


<br>

　　　　　 　　　　　 　　　　　 &nbsp; &nbsp; &nbsp;   **Button과 LinearLayOut의 계층도**

<div class="mermaid"> 
  graph TD; View-->Object; TextView-->View; ViewGroup-->View; Button-->TextView; LinearLayOut-->ViewGroup; 
</div>
<br><br>

## 뷰의 크기 속성

뷰는 화면의 일정 영역을 차지하기 때문에 모든 뷰는 반드시 크기 속성을 가지고 있어야 합니다.

뷰의 가로, 세로 크기 속성이 없으면 안드로이는 XML 레이아웃이 잘못 되었다고 판단하고,<br>

오류를 출력 하게 됩니다.

<br><br>


### 화면에 글자를 보여주는 텍스트뷰 XML

```xml
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World"  />
```

> **텍스트 뷰는 끝에 /> 하나만 붙혀서 사용 가능하다.**


|이름| 설명 |
|:--:|:--:|
|wrap_content|내용물의 크기에 따라 뷰의 크기를 결정합니다.|
|mathch_parent|뷰를 담고 있는 뷰 그룹의 여유 공간을 꽉 채웁니다.|
|숫자로 크기 지정| 뷰의 크기를 고정된 값으로 만들고 싶을때 사용합니다.|

> 뷰를 담고 있는 레이아웃이 어떤 것인지에 따라서 뷰에 적용되는 방식이 달라집니다.<br>
  또한 각각의 레이아웃마다 필수 속성도 약간씩 다를 수 있습니다.



### 다른 뷰들을 담고 있는 레이아웃

```xml
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    ... <생략>
    
</androidx.constraintlayout.widget.ConstraintLayout>
```

ConstraintLayout는 안에 뷰를 위한 태그들이 들어가야 해서 시작 태그와 끝 태그를 분리해서 사용할 수 밖에 없습니다.

여기서 anroid:은 안드로이드의 기본 API에서 정의한 속성입니다.

직접 정의 하거나 외부 라이브러리를 사용할때 그 안에 정의된 속성이라면 다른 단어가 붙을 수도 있습니다.

### Layout 쉽게 바꾸는 방법

왼쪽 하단의 Component Tree -> Convert View -> 원하는 레이아웃 선택 -> Apply

![1](https://user-images.githubusercontent.com/54762273/147871657-0ac7ff7e-bae5-420a-bdc5-6cf52f113792.jpg)

----- 
## 뷰 영역 

**Margin: 테두리 바깥쪽 공간**

**Padding: 테두리 안쪽 공간**

<img width="609" alt="1" src="https://user-images.githubusercontent.com/54762273/148038652-dec3e69c-e977-44a6-945c-60c542ea3e6d.png">


## 뷰 정렬하기 

|정렬 속성| 설명 |
|:--:|:--:|
|layout_gravity|부모의 여유 공간에 뷰가 모두 채워지지 않아 여유 공간이 생겼을 때 <br> 여유 공간안에서 뷰를 정렬함|
|gravity|뷰 안에 표시하는 내용물을 정렬함|


### layout_gravity
layout_gravity는 뷰의 layout_width나 layout_height 속성을 wrap_content로 만든 후에 같이 사용할 수 있다. 

예를 들어, 세로 방향으로 설정된 리니어 레이아웃에 추가된 버튼들의 layout 속성을 warp_content로 하면

각각의 버튼들은 한 줄에 한 개씩 추가되면서 글자가 보이는 만큼만 가로 공간을 차지하므로

나머지 가로 공간은 여유 공간으로 남게 된다. 

이렇게 여유 공간이 있을 때는 안드로이드는 일반적으로 왼쪽 정렬을 하게 되는데 layout_gravity 속성을 직접

설정하면 왼쪽,중앙 또는 오른쪽 정렬 도 할 수 있다. 

![6](https://user-images.githubusercontent.com/54762273/148054940-ec6ae29e-bc35-47f3-9ce8-919cb41b2636.jpg)



### gravity

gravity 속성은 뷰의 위치가 아니라 뷰 안에 들어 있는 내용물의 위치를 결정하는 것이다. 

gravity는 ㅣ 연산자를 이용해 여러 개의 값을 같이 설정할 수 있다.

**주의할 점은 ㅣ 연산자 양쪽에 공백이 없어야 한다.**

![6](https://user-images.githubusercontent.com/54762273/148056532-3758f892-373f-470c-8e3b-f4af93e3811a.jpg)





