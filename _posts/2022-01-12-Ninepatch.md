---
title: "NinePatch Image"
author: seyoung
date: '2022-01-12 18:54:00 +0900'
categories: Android study
tags: [android,ninepatch image]
math: true
mermaid: true
---

## 나인 패치

모바일 앱 내에 이미지를 적용하는 경우가 적지 않게 있다.

그런데, 모바일 기기 환경 상 기기의 종류가 여러가지이고 해상도도 기기마다 다 다르다.

기기마다 해상도가 다 다르다 보니 적용되는 이미지 사이즈가 늘어나거나 깨져서 

의도한것과는 다르게 이미지가 이상하게 보이게된다.

나인 패치 이미지는 위의 이미지 왜곡 문제를 해결해주기 위한 해결책이다.
![1](https://user-images.githubusercontent.com/54762273/149113634-90d48a65-fd89-4c1c-afb9-837578e6e0be.PNG)

위의 세개의 버튼은 일반 이미지인데, 폭이 넓어질 수록 왜곡이 생긴다.

아래의 세개의 버튼은 나인 패치 이미지를 적용 시켰고, 버튼에 설정된 이미지가 

그대로 유지되는 효과를 확인할 수 있다.

--- 
### 나인 패치 이미지 만들기

**NinePatch 이미지를 생성하려는 PNG 이미지 마우스 우클릭 -> Create 9-patch file** 
<br>
![1](https://user-images.githubusercontent.com/54762273/149116402-768bb06d-0dc3-4e3e-ad34-e57cc9247b9d.jpg)

이미지 이름을 입력하고 OK 버튼을 누르면 .9.png인 이미지 파일이 생성된다.

새 나인 패치 파일을 더블 클릭하여 작업공간으로 이동해서 수정후 Save 


> ***reference***->  <a href="https://developer.android.com/studio/write/draw9patch?hl=ko">안드로이드 개발자</a>