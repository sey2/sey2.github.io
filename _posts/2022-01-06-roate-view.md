---
title: "Rotate View Event"
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

![1](https://user-images.githubusercontent.com/54762273/148387647-a18f13d5-9378-414e-898e-83c1f014bd61.PNG)


![2](https://user-images.githubusercontent.com/54762273/148386837-bda87379-c66d-4a9d-8770-76a58f7d3773.PNG)

폴더 이름은 layotu-land로 설정 후 land 폴더의 activity.xml 파일을 복사 후 layout-land 폴더로 붙혀 넣기

![4](https://user-images.githubusercontent.com/54762273/148386973-5c773e10-271b-4d21-8f78-5707b7fda4ff.PNG)

그러면 이렇게 가로 모드일 때 화면이 적용된다.

단말기를 돌렸는데 변화가 없다면 Auto - rotate가 켜져 있는지 확인하자.

![3](https://user-images.githubusercontent.com/54762273/148387011-9e551cd8-1116-4cf3-9e9f-d71a061b8bc9.PNG)

---

## onSaveInstanceState

이렇게 하면 단말의 방향을 바꿨을 때 다른 화면이 보이게 할 수 있다.

근데 이러면 한가지 문제점이 있다.

**단말의 방향이 바뀔 때 액티비티가 메모리에서 없어졌다가 새로 만들어진다는 점이다.**

이 경우 액티비티 안에 선언 해 두었던 변수 값이 사라지므로 변수의 값을 저장 했다가 복원하는 방법이 있어야 한다.

이런 문제를 해결하는 방법이 onSaveInstanceState 콜백 메서드를 이용하는 것이다. 

이 메서드는 액티비티가 종료되기 전의 상태를 저장하고, 저장한 상태는 onCreate 메서드가 호출될 때 전달되는 번들 객체로 복원 할 수 있다.

---

### onSaveInstanceState 예시 

```java
public class MainActivity extends AppCompatActivity {
    String name;

    EditText editText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        editText = findViewById(R.id.editText);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                name = editText.getText().toString();   // 버튼을 클릭했을 때 사용자가 입력한 값을 name 변수에 할당
                showToast("Save Value" + name);
            }
        });

        if(savedInstanceState != null) {    // 이 화면이 초기화 될 때 name 변수의 값 복원
            name = savedInstanceState.getString(name);
            showToast("Return Value" + name);
        }
    }


    public void showToast(String data){
        Toast.makeText(this,data,Toast.LENGTH_LONG).show();
    }

    @Override
    protected void onSaveInstanceState(@NonNull Bundle outState) {
        super.onSaveInstanceState(outState);
        outState.putString("name",name);    // name 변수의 값 저장
    }   
}

```

***에디트 텍스트에 표시되는 값은 복원하지 않아도 그대로 유지 된다.**