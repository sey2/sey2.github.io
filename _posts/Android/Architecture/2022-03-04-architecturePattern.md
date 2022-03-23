---
title: "MVC, MVP, MVVM"
author: seyoung
date: '2022-03-04 01:15:00 +0900'
categories: Android Architecture
tags: [android]
math: true
mermaid: true
---

## 아키텍처 패턴 - MVC, MVP, MVVM

### MVC 



**Model, View, Controller로 구성**

  - **Model(모델): 어플리케이션의 데이터를 저장하는 역할을 담당.**
	  - **SQLite, File, cotent provider가 이 영역에 속함**
<br>

  - **View(뷰): 화면 구성을 담당하는 영역이다.**
     - **이 영역은 View 클래스를 상속하는 클래스를 사용해서 구성할 수 있다.**

- **Controller(컨트롤러)**
    - **View와 Model을 서로 연결하고 제어하는 영역이다.**
    - **activity, service, broadcast receiver, fragment 가 포함된다.**
    
   
<img width="1259" alt="1" src="https://user-images.githubusercontent.com/54762273/156602017-13f1f950-63e9-4b15-bbcd-9ffe381c82c1.png">


**장점: 구현이 쉽다.**

**단점: 하나의 클래스에 코드양이 증가, View와 Model간의 의존성이 높아져서 기능 추가 및 유지보수가 어려움**

간단한 프로젝트를 진행하는 경우에 적합, 프로젝트가 커지고 메소드 개수가 많아지면 코드 파악이 어려워지기
때문에 메소드와 클래스를 적절히 분리하면서 복잡도를 관리 해야함

---

### MVP

**Model, View, Presenter로 구성되어 있음**

  - **Model(모델): 데이터와 관련된 처리를 담당**

  - **View(뷰): 이벤트가 발생하면 Presenter로 알린 후 반환 받은 것에 따라 UI를 갱신**

 - **Presenter(프레젠터): View에서 전달 받은 이벤트를 처리하여 다시 View로 반환한다.**
	**ㄴModel에 데이터 요청이 필요한 이벤트가 들어오는 경우 Model에 요청한 후 받은 데이터를 View에 전달**

![1](https://user-images.githubusercontent.com/54762273/156604693-54826a58-bc34-46fa-b230-de27e2c78d68.png)


**장점: View와 Model 사이의 의존성을 낮출 수 있다.**

**단점: View와 Presenter 사이의 의존성이 높아진다. (View와 Presenter는 1:1로 매칭)**

---

### MVVM

  -   **Model(모델): 데이터와 관련된 처리를 담당한다. 데이터가 변경되면 ViewModel을 거쳐서 View로 전달**
	
		- **안드로이드에서는 LiveData나 RxJava 등을 통해 구현할 수 있다.**
		
		<br>
		
-   **View(뷰): 화면에 표현되는 레이아웃을 관리한다.**
	
	- **View는 Model은 알지 못하기 때문에 ViewModel을 옵저빙하고 있다가 상태 변화가 전달되면 화면을 갱신해야 한다.**

-   **ViewModel(뷰모델): View에 연결할 데이터와 명령으로 구성되어 있으며 변경 알림을 통해 View에게 상태 변화를 전달한다. ViewModel은 Model은 알지만 View는 알지 못한다.**

**장점: UI 컨트롤러의 책임을 분담하여 클래스가 거대해지는 것을 방지하고 유지 보수, 재사용성, 테스트 등을 용이하게 만들어준다.**

**단점: ViewModel의 설계가 어렵다. DataBinding 등 추가적인 공부가 필요하다.**

MVVM 모델을 구현하는 경우 데이터 바인딩을 함께 써야 의존성을 낮출 수 있다. 구글이 제공하는 AAC(Android Architecture Component)를 사용해 MVVM 패턴을 쉽게 구성할 수 있다.