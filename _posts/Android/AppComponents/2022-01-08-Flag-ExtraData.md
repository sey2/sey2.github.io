---
title: "액티비티 중복문제, 인텐트 객체 전달"
author: seyoung
date: '2022-01-07 22:14:00 +0900'
categories: Android AppComponents 
tags: [android,flag, extradata,parcelable ]
math: true
mermaid: true
---

## 액티비티 중복 문제 
액티비티로 만든 화면이 한 번 메모리에 만들어 졌는데도 계속 startActivity나 startActivityForResult 메서드를 

여러번 호출하면 메모리에 동일한 액티비티가 여러개 만들어진다. 

왜냐하면 시스템이 인텐트별로 새 액티비티를 띄워주기 때문이다. 

만약 같은 액티비티에 대해 인텐트를 두 번 보내면 중복된 액티비티가 뜨게 된다.

중복된 액티비티가 띄워지지 않게 할려면 어떻게 해야할까? 

이럴 때 사용하는게 바로 플래그이다.

## 플래그 

플래그를 이해하기 전에 액티비티가 처리되는 방식을 알아보자.

**액티비티는  액티비티 매니저(Activity Manager)라는 객체에 의해 액티비티 스택(Acticity Stack)이 관리된다.**

액티비티 스택은 액티비티를 차곡차곡 쌓아두었다가 가장 상위에 있던 액티비티가 없어지면 

이전의 액티비티가 다시 화면에 보이게 된다.


<img src="https://user-images.githubusercontent.com/54762273/148550562-99e6b8d2-453c-4ef6-ab8d-0ebca421a1ce.png" width="600" height=450/>

새로운 액티비티를 만들어 매니페스트 파일에 등록하면 그 액티비티 startActivity 또는 startActivityForReuslt

메서드를 사용해 실행된다.  이렇게 실행된 액티비티는 화면에 띄워지고 새로운 액티비티가 화면에 띄워지면

이전에 있던 액티비티는 스택에 저장되고 새로운 액티비티가 화면에 보이는 구조이다. 

이렇게 일반적인 스택 구조를 이용해 액티비티가 관리되는데 동일한 액티비티를 여러 번 실행하게 되면,

동일한 액티비티가 여러 개 스택에 들어가게 되고 동시에 데이터를 여러 번 접근하거나 리소스를 여러 번

사용하는 문제가 발생할 수 있다. 이러한 문제를 해결할 수 있는게 플래그이다. 

대표적인 플래그는 아래와 같다.

|플래그| 설명 |
|:--:|:--:|
| FLAG_ACTIVITY_SINGLE_TOP |이미 생성된 액티비티가 있으면, 그 액티비티를 그대로 사용하는 플래그|
| FLAG_ACTIVITY_NO_HISTORY  | 처음 이후에 실행되는 Activity는 스택에 들어가지 않음 (알람 같은거) |
| FLAG_ACTIVITY_CLEAR_TOP | 현재 Activity 이외 Activity는 모두 종료 |

<br>


![1](https://user-images.githubusercontent.com/54762273/148553000-ac952b79-382d-4ec5-a32b-05bed18b431f.png)

![2](https://user-images.githubusercontent.com/54762273/148553108-28adcb23-9ab8-4538-bd99-a3c1e9ecabb9.png)

![3](https://user-images.githubusercontent.com/54762273/148553037-c055bd68-c768-4472-93cb-2918345ec7d0.png)

---

**액티비티가 새로 만들어지지 않고 재사용된다면 액티비티의 onCreate 메서드가 호출되지 않는다.**

이 경우에는 새로 띄워지는 액티비티에서 인텐트를 전달 받아 처리하는 방법이 따로 있어야 한다.

그것이 바로 **onNewIntent** 메서드이다. 

이 메서드를 재정의하면 액티비티가 새로 만들어지지 않았을 때 인텐트 객체만 전달 받을 수 있다. 

<br>

## 부가데이터 

한 액티비티에서 다른 액티비티를 띄울 때 데이터를 전달해야 하는 경우가 있다.

예를 들어, 로그인 화면에서 로그인 버튼을 눌러 로그인을 성공하면 메뉴 화면으로 아이디를 

전달하여 표시해야 할 수도 있다. 

어떻게 하면 로그인 화면에서 메뉴 화면으로 아이디를 전달할 수 있을까?

가장 간단한 방법은 별도의 클래스를 만든 다음 그 안에 클래스 변수를 만들어 두 개의 화면에서

모두 그 변수를 참조하게 하는 방법이다.

즉, 로그인 화면에서 값을 설정하고 메뉴 화면에서 로그인 화면의 변수 값을 참조하면 된다.

하지만 이 경우 다른 앱에서 우리가 만든 화면을 띄울 수도 있기 떄문에 변수를 공유하는 방식으로

데이터를 전달하는 것이 불가능할 수도 있다.

따라서 **기본적으로 액티비티를 띄울 때 전달되는 인텐트 안에**

**부가 데이터(Extra data)를 넣어 전달하는 방법을 권장한다.**

인텐트 안에는 번들(Bundle) 객체가 들어 있는데, 번들 객체는 해시테이블과 유사해서 

putExtra와 get-기본 자료형 이름Extra 메서드로 데이터를 넣거나 빼낼 수 있다.

예를 들어 문자열을 넣고 싶을 때는 putExtra 메서드를 호출하고,

문자열을 다시 빼내고 싶을때는 getStringExtra 메서드를 이용하면된다. 

또한, getOOO() 형태를 가진 메서드는 데이터 값이 없으면 디폴트로 설정한 defaultValue 값이 반환된다.

전달하고 싶은 데이터가 객체인 경우에는 객체 자체를 전달 할 순 없고, 객체 데이터를 바이트 배열로 

변환하거나 Serializable 인터페이스를 구현하는 객체를 만들어 직렬화한 다음 전달 해야한다.

하지만 안드로이드에서는 Serializable 인터페이스와 유사한 Parcelable 인터페이스를 권장한다.

Parcelable 인터페이스는 직렬화 했을 때 크기가 작아 안드로이드 내부의 데이터 전달에 자주 사용된다. 

이 인터페이스를 사용하려면 아래의 두 가지 메서드를 모두 구현해야 한다.

```java
public abstract int describleContents()		// 객체의 유형을 구분할 때 사용

/* 객체가 가지고 있는 데이터를 Parcel 객체로 만들어주는 역할 */
public abstract void writeToParcel(Parcel dest, int flags)		
```

위 두가지 메소드를 다 구현한 다음에는 CREATOR라는 상수를 만들어야 한다.

CREATOR 상수는 Parcel 객체로부터 데이터를 읽어 들여 객체를 생성하는 역할을 한다. 

---

### Parcelable 예제

java 파일 프로젝트에 폴더에 class 하나를 아래와 같이 추가해준다.

```java
public class SimpleData implements Parcelable {
    int number;
    String message;

    public SimpleData(int num, String msg) {
        number = num;
        message = msg;
    }

    /* Parcel 객체에서 읽기 */
    public SimpleData(Parcel src) {
        number = src.readInt();
        message = src.readString();
    }

    /* Creator 상수 정의 */
    public static final Creator CREATOR = new Creator() {

        public SimpleData createFromParcel(Parcel in) {
            return new SimpleData(in);
        }

        public SimpleData[] newArray(int size) {
            return new SimpleData[size];
        }

    };
    public int describeContents(){
        return 0;
    }
    
    /* Parcel 객체로 쓰기 */
    /* Simple Data 객체 안에 있는 데이터를 Parcel 객체로 만드는 역할을 한다*/
    public void writeToParcel(Parcel dest, int flags){
        dest.writeInt(number);
        dest.writeString(message);
    }
}
```

**MainActivity.java 코드를 수정한다.**

```java
public class MainActivity extends AppCompatActivity {
    public static final int REQUEST_CODE_MENU = 101;
    public static final String KEY_SIMPLE_DATA = "data";
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = findViewById(R.id.button);
        button.setOnClickListener((v)->{
            Intent intent = new Intent(getApplicationContext(),MenuActivity.class);
            SimpleData data = new SimpleData(100, "Hello Android");
            intent.putExtra(KEY_SIMPLE_DATA,data);
            startActivityForResult(intent,REQUEST_CODE_MENU);
        });
    }
}
```

**MenuActivity.java 코드를 수정한다.**

```java
public class MenuActivity extends AppCompatActivity {
    TextView textView;

    public static final String KEY_SIMPLE_DATA = "data";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_menu);

        textView = findViewById(R.id.textView);
        Button button = findViewById(R.id.button2);
        button.setOnClickListener((v)->{
            Intent intent = new Intent();
            intent.putExtra("name","mike");
            setResult(RESULT_OK,intent);

            finish();
        });

        // 메인 액티비티로부터 전달 받은 인텐트 참조
        Intent intent = getIntent();
        processIntent(intent);
    }

    private void processIntent(Intent intent){
        if(intent != null){
            // getExtras() -> 번들 객체 반환
            Bundle bundle = intent.getExtras();
            
            /* 번들 객체 안에 SimpleData 객체가 들어 있으므로  getParcelable 메서드로
                객체를 참조한다. */
            SimpleData data = bundle.getParcelable(KEY_SIMPLE_DATA);
            if(intent != null){
                textView.setText("전달 받은 데이터\n Number : " + data.number +
                        " \nMessage : " + data.message);
            }
        }
    }
}
```
---


**결과**


![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148561904-5cc19e5f-cba5-4995-81f6-7e1b61950a97.gif)


이렇게 Parcelable 인터페이스를 사용하면 객체를 정의해 데이터를 전달할 수 있으므로 코드가

좀 더 단순해지고 재사용성이 높아지는 장점이 생기게 된다. 

하지만 데이터를 담아둘 새로운 객체를 일일이 정의하는 것이 번거롭다는 단점이 있다. 