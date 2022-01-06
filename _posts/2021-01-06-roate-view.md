---
title: "Roate View Event"
author: seyoung
date: '2022-01-06 22:05:00 +0900'
categories: Android Event View
tags: [android,event,key]
math: true
mermaid: true
---

## 단말 방향을 전환 했을 때 이벤트 처리

**res 폴더 밑에 새 디렉토리 만들기 -> layout-land 만든다.**

layout-land 폴더는 layout 폴더와 같은 역할을 하지만 단말이 가로 방향으로 보일 때는 layout-land 폴더 안에 

들어 있는 XML 레이아웃 파일이 사용된다.

즉, 세로 방향일 때는 layout 폴더의 activity_main.xml 파일이 사용되고 

**가로 방향일 때는 layout-land 폴더의 activity_main.xml 파일이 사용된다.**

![1](https://user-images.githubusercontent.com/54762273/148386561-fec21025-174b-463d-acf5-4d64b44d9bcf.PNG)



![2](https://user-images.githubusercontent.com/54762273/148386837-bda87379-c66d-4a9d-8770-76a58f7d3773.PNG)

폴더 이름은 layotu-land로 설정 후 land 폴더의 activity.xml 파일을 복사 후 layout-land 폴더로 붙혀 넣기

![4](https://user-images.githubusercontent.com/54762273/148386973-5c773e10-271b-4d21-8f78-5707b7fda4ff.PNG)

그러면 이렇게 가로 모드일 때 화면이 적용된다.

단말기를 돌렸는데 변화가 없다면 

![3](https://user-images.githubusercontent.com/54762273/148387011-9e551cd8-1116-4cf3-9e9f-d71a061b8bc9.PNG)


Auto - rotate가 켜져 있는지 확인하자.