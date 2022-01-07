---
title: "안드로이드 버튼 추가하기 "
author: seyoung
date: '2022-01-01 00:19:00 +0900'
categories: Android Button
tags: [andorid,buuton]
math: true
mermaid: true
---

## 버튼 추가하기

![13](https://user-images.githubusercontent.com/54762273/147830076-e5294087-3585-4065-a5a2-de84a525e74c.PNG)

<br>
디자인 화면의 왼쪽에는 팔레트(Paletee)가 있습니다. <br><br>
팔레트안에는 (버튼, 메세지) 등이 들어 있는데 이 요소를 마우스로 끌어 화면에 추가할 수 있습니다. <br><br>
화면에 버튼을 끌어다 놓기 전에 디자인 화면 위쪽에는 있는 자석 모양 아이콘을 <br><br>
클릭하여 금지 모양을 없애 주어야 화면 요소를 쉽게 배치 할 수 있습니다.<br><br>

![13](https://user-images.githubusercontent.com/54762273/147830407-c6793fa9-410e-474d-ab76-64c1559d0e66.jpg)

그 후 위의 이미지대로 클릭을 하여 버튼을 추가해주면 버튼이 생기게 됩니다.<br><br>

## 버튼 이벤트 처리 

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
    
    public void OnButtonClicked(View v){
        Toast.makeText(this,"확인1 버튼이 눌렸어요.",Toast.LENGTH_LONG).show();
    }
}
```

** 자동으로 추가되는 context:와 text:는 안드로이드 스튜디오가 자동으로 보여주는 글자이므로 입력하지 않도록 주의하자 ! <br><br>

자 인제 소스 파일에 함수를 추가 하였으니 버튼이 클릭되면 함수가 호출 되게 설정을 바꿔주면 끝난다.<br>


![13](https://user-images.githubusercontent.com/54762273/147842805-caf3d316-15f0-49cd-834a-5e96dcf03066.jpg) <br>

**확인1 버튼을 선택한 상태로 속성 창에서 onClick 속성 값으로 onButtonClicked를 선택하면 된다.**


![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/147842855-6b961024-efa4-493f-b51a-e69006556850.gif) <br>

위 과정을 문제 없이 잘 했다면 영상처럼 버튼을 클릭하면 메세지가 나타났다 사라지게 된다.

# 여러개의 버튼 추가하기 

위에 버튼을 추가 했던것 처럼 버튼을 추가한다 -> 왼쪽, 오른쪽, 위, 아래 동그라미 버튼이 생기는데, <br> <br>

위에 있는 동그라미는 위의 버튼의 밑에 경계선과 연결하고, 밑에 있는 동그라미는 밑에 있는 바닥 경계선과 연결해 준다 <br><br>

![12](https://user-images.githubusercontent.com/54762273/147842965-0b057cf1-a7aa-4a9f-99e1-a80f84121491.PNG)


# 디자인 화면에서 단말 변경하기

![12](https://user-images.githubusercontent.com/54762273/147843024-c239d79b-b9ef-47d3-b8b4-e50fa0e4e296.jpg)

# 여러가지 버튼 이벤트 추가

```java

    public void onButton2Clicked(View v){
        Intent myIntent = new Intent(Intent.ACTION_VIEW, Uri.parse("http://m.naver.com"));
        startActivity(myIntent);
    }

    public void onButton3Clicked(View v){
        Intent myIntent = new Intent(Intent.ACTION_VIEW, Uri.parse("tel:010-9999-9999"));
        startActivity(myIntent);
    }

```

인텐트는 간단하게 안드로이드 플랫폼에게 원하는것을 말할 때 전달하는 우편물 같은것 이다. <br>
예를 들어 '웹 페이지를 띄우고 싶어'라고 편지를 쓰면 안드로이드 단말은 그 편지를 <br>
웹 브라우저에게 전달하면 웹 브라우저는 그 편지를 보고 내용에 맞게 웹 페이지를 띄어준다.



소스파일을 추가해준 뒤 속성 창에서 onClick 속성 값으로 버튼에 맞는 함수를 넣어준다. <br>
이렇게 하면 밑의 영상처럼 네이버로 연결, 전화 연결을 할 수 있게 된다.




![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/147843100-d9e0a6da-f3b5-4026-acd4-9b198cd62468.gif)



|android|기능|
|:---:|:---:|
|setContentView|화면에 무엇을 보여줄지 결정하는 메서드|
|R.layout.acticity_main|res/layout/activity_main_xml 파일|
|Intent|어떤 기능을 실행할 것인지 지정할 때 사용|
|Toast|화면에 잠깐 보였다 없어지는 메시지를 간단하게 보여주고 싶을때 사용 |


--- 


