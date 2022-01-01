---
title: "안드로이드 버튼 추가하기 "
author: seyoung
date: '2022-01-01 00:19:00 +0900'
categories: Android
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

## 버튼 클릭하면 메세지 창 나타나게 하기

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







