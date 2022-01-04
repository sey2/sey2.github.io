---
title: "Linear Layout"
date: '2021-12-29 19:52:00 +0900'
categories: Android Layout
tags: [anroid,view,linear layout, layout]
mermaid: true
---


## LinerLayout
Liner Layout은 view를 수평 또는 수직 방향으로 배치할 수 있는 레이아웃이다. 
- android:orientation = "vertical" (하위 뷰들을 수직 방향으로 배치)
- android:orientation = "horizontal" (하위 뷰들을 수평방향으로 배치)
```sh
android:orientation = "vertical"
android:orientation = "horizontal"
```
![12](https://user-images.githubusercontent.com/54762273/127168417-5e6e4306-0595-4b14-9641-c4c8245ba4be.png)

## Features


|android|기능|
|:---:|:---:|
|math_parent|부모 컨테이너의 남아있는 모든 여유 공간을 채움|
|wrap_content|뷰 안에 들어있는 내용물의 크기에 맞게 뷰의 크기가 결정|
|dp|임의로 원하는 길이를 지정한다.|
|id|뷰나 레이아웃의 아이디를 지정한다.|
|background| 뷰의 배경을 설정한다.|


```sh
<LinearLayout
       android:layout_width = "match_parent"
       android:layout_height = "wrap_content"
    </LinearLayout>
```

![2](https://user-images.githubusercontent.com/54762273/127171520-2e83eaa9-3aa5-44b4-8e36-59558c6fb605.png)

```sh
<LinearLayout
         android:layout_width = "wrap_content"
        android:layout_height = "match_parent"
    </LinearLayout>
```
![3](https://user-images.githubusercontent.com/54762273/127172228-3a40679b-a2ba-4653-9a1a-f8c4555225d7.png)

## backgoround
```sh
android:background = "#FF0000"
```
## dp
```sh
android:layout_width = "200dp"
android:layout_height = "100dp"
```
## id
```sh
android:id = "@+id/linearlayout"
android:id = "@+id/textview"
```


## 리니어 레이아웃 사용하기

**ComponetTree-> 현재 레이아웃 우 클릭 -> Convert View**

![1](https://user-images.githubusercontent.com/54762273/148041680-086e8b17-86ec-49d3-b05d-600c3de3e459.png)

![2](https://user-images.githubusercontent.com/54762273/148041774-a2aa3b6e-81ce-4331-9c4a-7e79044351e5.PNG)


Common Attributes에서 oriantation 지정 

![3](https://user-images.githubusercontent.com/54762273/148041965-8a04e558-15d6-4255-847c-a5759e2a5ed4.PNG)


**Vertical로 하면 아래 이미지 처럼 세로로 쌓인다.**

![4](https://user-images.githubusercontent.com/54762273/148041999-c5b013d6-bfdf-4f3b-8815-25fc22bbd499.PNG)



**Horizontal**로 바꾸게 되면 버튼 두개가 사라지게 되는데,

그 이유는 layout_wdith 속성 값이 match_parent로 되어서 그런다.

![5](https://user-images.githubusercontent.com/54762273/148042029-c3eea9b7-6519-43c4-8367-78319c4569b2.PNG)


**wrap_content로 바꾸게 되면 버튼 세개가 다시 생기게 된다.** 

![6](https://user-images.githubusercontent.com/54762273/148042313-f0e131d2-60ba-45d0-8ee3-5fd329415876.PNG)
