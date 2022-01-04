---
title: "안드로이드 레이아웃 종류 "
author: seyoung
date: '2022-01-04 17:48:00 +0900'
categories: Android Layout 
tags: [android,layout]
math: true
mermaid: true
---


##  레이아웃의 종류 


|레이아웃 이름| 설명 |
|:--:|:--:|
|**제약 레이아웃<br> (ConstarintLayout)**  |**제약 조건 기반 모델**<br> 연결선을 제약 조건으로 하여 화면을 구성하는 방법<br>안드로이드 스튜디오에서 자동으로 설정하는<br> **디폴트 레이아웃**  |
|**리니어 레이아웃<br> (LinearLayout)**  |**박스 모델(BOX)**<br> 한 쪽 방향으로 차례대로 뷰를 추가하며 화면을<br> 구성하는 방법<br> 뷰가 차지할 수 있는 사각형 영역을 할당 |
|**상대 레이아웃<br> (RelativeLayout)**  |**규칙(Rule) 기반 모델**<br>부모 컨테이너나 다른 뷰와의 <br>상대적 위치로 화면을 구성하는 방법<br> 권장하지 않음   |
|**프레임 레이아웃 <br> (ConstarintLayout)**  |**싱글(Single)모델**<br> 가장 상위에 있는 하나의 뷰 또는 <br>뷰 그룹만 보여주는 방법<br>여러개의 뷰가 중첩하여 쌓게 됨 가장 단순하지만 <br> 여러개의 뷰를 중첩한 후 <br>각 뷰를 전환하여 보여주는 방식 |


## 대표적인 레이아웃 

### 제약 레이아웃 
> <a  href="https://sey2.github.io/posts/Constraint-Layout/">제약 레이아웃 </a>

### 리니어 레이아웃 

> <a  href="https://sey2.github.io/posts/Android_day_1/">리니어 레이아웃  </a>

### 상대 레이아웃 
> <a  href="https://sey2.github.io/posts/RelativeLayout/">상대 레이아웃 </a>


### 프레임 레이아웃 

> <a  href="https://sey2.github.io/posts/FrameLayout/">프레임 레이아웃 </a>

### 테이블 레이아웃

> <a  href="https://sey2.github.io/posts/TabeleLayout/">테이블 레이아웃 </a>


## 스크롤 뷰란?
 하나의 뷰나 뷰 그룹을 넣을 수 있고 어떤 뷰의 내용물이 넘치면 스크롤을
 만들 수 있게 도와준다.

예를 들어 스크롤뷰에 넣은 뷰 안에 글자를 넣었을 때 그 글자가 많아서 

화면을 벗어나면 자동으로 스크롤이 만들어 진다. 