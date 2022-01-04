---
title: "상대 레이아웃"
author: seyoung
date: '2022-01-04 18:07:00 +0900'
categories: Android Layout 
tags: [android,layout,relativelayout]
math: true
mermaid: true
---


## 상대 레이아웃 

**상대 레이아웃(RelativeLayout)** 은 제약 레이아웃이 안드로이드 스튜디오에서
사용  되기 전까지 디폴트 레이아웃으로 사용되었다. 

하지만 지금은 **상대 레이아웃보다 제약 레이아웃을 더 권장한다.** 

**이유는 제약 레이아웃은 상대 레이아웃의 특성은 그대로 가지고 있으면서
더 많은 기능을 제공하기 때문이다.**

위의 글만 보면 상대 레이아웃은 많이 사용하지 않을거라고 오해 할 수 있는데,

실제 앱 개발할 때 단순하고 이해하기 쉬운 레이아웃이라 아직도 사용하는 
경우가 있다고 한다. 

![1](https://user-images.githubusercontent.com/54762273/148033284-28e4ac8f-077c-4a43-a65b-cce92a72152d.jpg)

상대 레이아웃은 부모 컨테이너, 즉 부모 레이아웃과의 상대적 위치 또는

같은 레이아웃 안에 들어 있는 다른뷰와의 상대적 위치를 이용해 화면을

배치하는 레이아웃이다.

> **왼쪽 그림에서 버튼이 부모 컨테이너의 위쪽에 붙어 있다.**
> **이건 버튼의 상대 레이아웃 속성으로 부모 컨테이너 위쪽에 붙힌거다.**

> **오른쪽 그림은 상대 레이아웃의 속성(Right)으로** 
>  **첫 번째 버튼 오른쪽에 두 번째 버튼을 붙인거다.**


<br>

## 속성

 **부모 컨테이너와의 상대적 위치를 이용해 뷰를 배치할 수 있는 속성**

| 속성 | 설명 |
|:--:|:--:|
|layout_alignParentTop  | 부모 컨테이너의 위쪽과 뷰를 맞춤 |
|layout_alignParentBottom  | 부모 컨테이너의 아래쪽과 뷰의 아래쪽을 맞춤 |
|layout_alignParentLeft | 부모 컨테이너의 왼쪽 끝과 뷰의 왼쪽 끝을 맞춤 |
|layout_alignParentRight  |부모 컨테이너의 오른쪽 끝과 뷰의 오른쪽 끝을 맞춤 |
|layout_centerHorizontal |부모 컨테이너의 수평 방향 중앙에 배치함  |
|layout_centerVertical  |부모 컨테이너의 수직 방향 중앙에 배치함  |
|layout_centerlnParent |부모 컨테이너의 수평과 수직 방향 중앙에 배치함 |


---

**다른 뷰와의 상대적 위치를 이용해 뷰를 배치할 수 있는 속성**

| 속성 | 설명|
|:--:|:--:|
|layout_above  | 지정한 뷰의 위쪽에 배치함 |
|layout_below | 지정한 뷰의 아래쪽에 배치함 |
|layout_toLeftOf  |지정한 뷰의 왼쪽에 배치함  |
|layout_toRightOf  |지정한 뷰의 오른쪽에 배치함  |
|layout_alignTop  |지정한 뷰의 위쪽과 맞춤  |
|layout_alignBottom  |지정한 뷰의 아래쪽에 맞춤  |
|layout_alignLeft  |지정한 뷰의 왼쪽과 맞춤  |
|layout_alignRight |지정한 뷰의 오른쪽과 맞춤 |
|layout_alignBaseline |지정한 뷰와 내용물의 아래쪽 기준선을 맞춤 |




