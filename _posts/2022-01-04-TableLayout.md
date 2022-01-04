---
title: "테이블 레이아웃"
author: seyoung
date: '2022-01-04 18:23:00 +0900'
categories: Android Layout 
tags: [android,layout,tablelayout]
math: true
mermaid: true
---

## 테이블 레이아웃

테이블 레이아웃(Table Layout)은 그 안에 들어가는 뷰들을 격자 형태로 

만들어 주는 레이아웃이다. 

테이블 레이아웃은 HTML에서 사용하는 방식과 유사하여 실용적이지만 제약 레이아웃과

리니어 레이아웃만으로도 화면 배치를 할 수 있어 자주 사용하지는 않는다.

> 직관적으로 격자 형태를 만들고 싶을 때 사용하는 경우도 있으니 사용 방법을 알아두자.



![1](https://user-images.githubusercontent.com/54762273/148036848-0029c6a0-fd4e-4a4a-b299-f0afc635f694.png)


## 테이블 레이아웃 사용하기 

**Component Tree -> Convert view -> LinearLayout -> 팔레트창 -> TableLayout**

> Covert view 메뉴에는 TableLayout이 없다. 

![1](https://user-images.githubusercontent.com/54762273/148075217-01e6e311-26f7-48cf-a8cd-74cd14af3957.jpg)


이렇게 Table Row를 설정해주면 layout_height값은 **match_parent**이고 테이블 레이아웃에 포함된,

TableRow의 높이 값은 내부적으로 항상 wrap_content로 설정되어 있어서 화면을 꽉 채울 수 없다.

반대로 layout_width는 **wrap_content**로 설정되어 있어서 가로 공간을 꽉 채운다.


첫 번째, 두 번째, TableRow에 버튼을 세 개씩 추가하면 아래와 같은 상태가 된다.

![캡처](https://user-images.githubusercontent.com/54762273/148076153-36a0c092-402e-491a-be26-ce7354f0e649.PNG)


이 상태에서 오른쪽에 남는 공간이 없도록 만들고 싶을 경우에는, 

TableLayout의 stretchColumns 속성 값을 설정하면 된다. 이 속성은 가로 방향으로 여유 공간이 있다면,

그 여유 공간까지 모두 채워서 column을 설정 할 수 있다. 

예를 들어 stretch 값을 0이라고 적어주면 첫 번째 컬럼에 있는 버튼들이 0 컬럼의 남은 공간을 다 채우게 된다.

0,1,2를 입력하면 아래와 같은 상태가 된다. 


![캡처](https://user-images.githubusercontent.com/54762273/148076817-5d1b9d5a-2209-4eae-87d3-1fa0555c2558.PNG)

또한, 테이블 레이아웃의 대표적인 속성으로 **shrinkColumns, stretchColumns**가 있다.

shrinkColumns은 부모 컨테이너의 폭에 맞추도록 각 열의 폭을 강제로 축소한다.

stretchColumns는 부모 컨테이너의 여유 공간을 모두 채우기 위해 각 열의 폭을 강제로 늘린다. 

---


### layout_span

뷰가 여러 column에 걸쳐 있도록 만들기 위한 속성이며, 

뷰가 몇 개의 칼럼을 차지하게 할 것인지 숫자로 지정한다.

![캡처](https://user-images.githubusercontent.com/54762273/148078360-d58bf9ff-2e1c-4858-9f03-414fb31453c7.PNG)


**위와 같이 layout_span 값을 2로 지정하면 입력 상자가 두 개의 column 영역을 차지하게 된다.** 
