---
title: "View Listener 구현 방법"
date: '2022-01-05 21:58:00 +0900'
categories: Android Layout
tags: [anroid,view,event,listener]
mermaid: true
---



## View의 Event와 Listener

각각의 View들은 고유한 이벤트를 가지고 있다. 

이벤트 발생시 이를 처리하는 Listener에 대해 알아 보면, 

- Event : 뷰에서 다양한 동작이 발생하거나 상태가 변경될 때 발생하는 함수.

- Listener : 뷰의 이벤트 발생시, 이를 받아 처리할 수 있는 인터페이스.

처리 방식을 알아보기 위해

버튼을 클릭했을 때 발생하는 OnClick() 이벤트로 예를 들어 보자.

----

### Listener를 구현하는 방법

 1. XML에서 속성을 정의하고 메소드를 호출하는 방법
 2. 익명클래스를 이용하는 방법
 3. 람다식을 이용하는 방법
 4. 별도의 인터페이스 클래스를 이용하는 방법
 5. Activity에서 인터페이스를 정의하는 방법

---

###  XML에서 속성을 정의하고 메소드를 호출하는 방법

**XML 정의** 
```xml
<Button  android:layout_width="wrap_context"  
		 android:layout_height="wrap_context"  
		 android:text="버튼" 
		 android:onClick="button_OnClick" />  
``` 

**Java - Activity class**
```java
public void button_OnClick(View v) {
    TextView textView = findViewById(R.id.txtName);
    textView.setText("클릭되었습니다.");
}
```

----

###  익명클래스 Anonymous-class를 이용하는 방법

```java
Button btn = findViewById(R.id.btn);

btn.setOnClickListener(new View.onClickListener() { 
	@Override 
	public  void  onClick(View v) { 
		TextView textView = findViewById(R.id.txtName); 	
		textVew.setText("클릭되었습니다."); 
		} 
	});  

```
---

### 람다식을 이용하는 방법

```java
Button btn = findViewById(R.id.btn); 
btn.setOnClickListener((v)-> {
	TextView textView = findViewById(R.id.txtName); 
	textVew.setText("클릭되었습니다."); 
});  
```
---
### 별도의 인터페이스 이용 방법 
  
```java
class  MyListener  implements  View.OnClickListener {
	@Override 
	public  void  onClick(View v) {
		TextView textView = findViewById(R.id.txtName); 
		textVew.setText("클릭되었습니다.");
	 }
 }  

```

---

### Activity에서 인터페이스를 정의하는 방법 

```java 
public  class  MainActivity  extends  AppCompatActivity  implements  View.OnClickListener { 
	public  void  onCreate() { 
		Button btn = findViewById(R.id.btn); 	
		btn.setOnClickListener(this);
	} 
} 
@Override  
public  void  onClick(View v) { 
	TextView textView = findViewById(R.id.txtName); 	
	textVew.setText("클릭되었습니다."); '
}  
```