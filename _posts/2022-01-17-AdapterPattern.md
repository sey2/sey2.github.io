---
title: "Adapter Pattern"
author: seyoung
date: '2022-01-17 01:10:00 +0900'
categories: Java Design_Pattern
tags: [android,layout]
math: true
mermaid: true
---

## Adapter 패턴

Android 공부를 계속하다 보니 Adpater Class 개념이 많이 나와서 정리해보고자 한다.

Adapter 패턴은 이름 그대로 어댑터 역할을 한다.

만약, 직류 12볼트로 작동하는 노트북을 교류 100볼트의 AC 전원에 연결한다고 가정하자.

이때 우리는 교류 100볼트를 직류 12볼트로 교환해줄 어댑터가 필요하다.

adapter는 개조시키는 것 이라는 의미를 가지고 있고,

어댑터의 역할은 직류 12볼트의 컴퓨터를 교류 100볼트의 환경에 맞게 바꾸는 것이다.

**이미 제공되어 있는 것과 필요한 것 사이의 차이를 없애주는 디자인 패턴이 Adapter 패턴이다.**

Adapter 패턴은 Wrapper 패턴으로 불리기도 하고 일반 상품을 예쁜 포장지로 싸서

선물용 상품으로 만드는 것처럼, 무엇인가를 한번 포장해서 다른 용도로 사용할 수 있게

교환해주는 것과 비유 할 수 있다.

**Adapter 패턴에는 다음과 같이 두가지 종류가 있다.**

   - **클래스에 의한 Adapter Pattern (상속을 사용한 Adapter 패턴)**
   - **인스턴스에 의한 Adapter Pattern (위임을 사용한 Adapter 패턴)**

<br><br>

**Adapter Pattern Structure**

![1](https://user-images.githubusercontent.com/54762273/149666191-3ed430c6-19c6-4305-893a-d618a6ab2dfc.PNG)

**Target(대상)의 역할**
    
  - 지금 필요한 메소드를 결정한다. 노트북을 작동 시키기 위한 직류 12볼트에 해당한다.
	 Print 인터페이스 (상속)나 Print 추상 클래스 (위임)가 이역할을 한다.


**Client(의뢰자)의 역할**
    
  - Target 역할의 메소드를 사용해서 일을 한다. 직류 12볼트로 움직이는 노트북에 해당한다.
     Main 클래스가 이 역할을 한다.

**Adaptee의 역할**
    
  - 이미 준비되어 있는 메소드를 가지는 역할을 한다. 교류 100볼트에 해당한다.
     Banner 클래스가 이 역할을 한다.

**Adapter의 역할**
    
  - Adaptee 역할의 메소드를 사용해서 어떻게든 Target 역할을 만족시키기 위한 것이 Adapter 패턴의 
    목적이며, **Adpter 역할에 해당하며, PrintBanner 클래스가 이 역할을 한다.**

---

### 왜 사용할까? 

이미 존재하고 있는 클래스를 이용하는 경우가 자주 있다.

특히 그 클래스가 충분한 테스트를 받아서 버그가 적으며 실제로 지금까지 사용된 실적이 있다면

어떻게든 그 클래스를 부품으로 재이용하고 싶을것이다.

Adapter 패턴은 기존의 클래스를 개조해서 필요한 클래스를 만든다.

이 패턴을 이용하여 메소드를 더욱 빠르게 만들 수 있다. 

만약 버그가 발생해도 기존의 클래스(Adapter의 역할)에는 버그가 없으므로 

Adapter 역할의 클래스를 중점적으로 조사하면 되고, 프로그램 검사도 상당히 쉬워진다.

----

### 상속을 이용한 Adapter Pattern

Banner class는 미리 벽 단자에 붙어 준비 되어 있는 교류 100V로 생각하면 된다.  (이미 제공되어 있는 것)

Print Banner class는 100볼트를 12볼트로 교환해 주는 어댑터 역할을 한다.

Print Interface는 직류 12볼트 역할이라고 생각하면 된다.

|전원의 비유  | 예제 프로그램 |
|:--:|:--:|
|제공되고 있는 것<br> (교류 100V)  | Banner Class<br> (showWithParen, showWithAster) |
|교환장치(어댑터)| PrintBanner 클래스|
|필요한 것<br>직류 (12V)| Print Interface<br> (printWeak,printStrong)|

구조는 아래와 같다.

![1](https://user-images.githubusercontent.com/54762273/149666952-8a9e5200-9c5c-4c05-9d4f-36fb260bd2ba.PNG)





**Banner.java**

```java
public class Banner {
	private String string;
	
	public Banner(String string) {
		this.string = string;
	}
	
	public void showWithParen() {
		System.out.println("(" + string + ")");
	}
	
	public void showWithAster() {
		System.out.println("*" + string + "*");
	}
}
```
<br>

**PrintBanner.java**

```java
public class PrintBanner extends Banner implements Print{
	
	public PrintBanner(String string) {
		super(string);
	}
	
	public void printWeak() {
		showWithParen();
	}
	
	public void printStrong() {
		showWithAster();
	}
}
```

<br>

**Print.java**

```java
public interface Print {
	public abstract void printWeak();
	public abstract void printStrong();
}
```

<br>

**Main.java**

```java
public class Main {
	public static void main(String[] args) {
		Print p = new PrintBanner("Hello");
		p.printWeak();
		p.printStrong();
	}

}
```
<br>

Main Class 에서 PrintBanner의 인스턴스를 Print 인터페이스형의 변수로 대입한 것에 주의해야 한다.

Banner 클래스나 showWithParen 메소드나 showWithAster 메소드는 Main 클래스의 소스 코드상에서

완전히 감추어져 있습니다.

마치 노트북이 직류 12볼트만 제공하면 제대로 작동하지만, 

제대로 작동할 수 있도록 어댑터를 통해서 교류 100볼트가 제공되고 있는것은 모르는 것과 같다.

즉 Main 클래스에서 PrintBanner 클래스를 보면 이 클래스가 어떻게 구현되어 있는지 모른다.

이 뜻은 Main 클래스를 전현 변경하지 않고도 PrintBanner 클래스의 구현을 바꿀 수 있다는 것이다.

---

### 위임을 사용한 Adapter Pattern

상속을 이용한 방법과 비슷한데 Adapter 역할을 하는 Print.java 파일이 

전에는 interface로 선언을 했지만 위임을 사용하는 방법에는 추상 클래스로 선언을 한다.

![1](https://user-images.githubusercontent.com/54762273/149667359-7d6e4cbe-6b67-45bf-a21e-4b58ff1f28f1.PNG)


**Main.class와 Banner.class는 위와 같다.**

**Print.class**

```java
public abstract class Print {
	public abstract void printWeak();
	public abstract void printStrong();
}
```
<br>

**PrintBanner.class**

```java
public class PrintBanner extends Print{
	private Banner banner;
	
	public PrintBanner(String string) {
		this.banner = new Banner(string);
	}
	
	public void printWeak() {
		banner.showWithParen();
	}
	
	public void printStrong() {
		banner.showWithAster();
	}
}
```
