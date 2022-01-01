---
title: Linear Layout"
author: seyoung
  name: seyoung
  link: https://github.com/sey2/sey2.github.io
date: '2021-12-29 19:52:00 +0800'
categories: Anroid
tags: [anroid,view,linear layout, layout]
math: true
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
| android: | 기능 |
| ------ | ------ |
| math_parent | 부모 컨테이너의 남아있는 모든 여유 공간을 채움|
| wrap_content |뷰 안에 들어있는 내용물의 크기에 맞게 뷰의 크기가 결정 |
| dp |임의로 원하는 길이를 지정한다. |
| id |뷰나 레이아웃의 아이디를 지정한다.|
| background | 뷰의 배경을 설정한다.|

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
