---
title: "Activity Life Cycle"
author: seyoung
date: '2022-01-09 00:20:00 +0900'
categories: Android AppComponents
tags: [android,fragment ]
math: true
mermaid: true
---

## 프로세스와 테스크

프로세스는 독립적인 하나의 상자와 같아서 다른 프로세스와 정보를 공유할 수 없다.

따라서 하나의 프로세스에서 다른 프로세스의 화면을 띄우려면 시스템의 도움이 필요하다.

시스템에서 액티비티의 각종 정보를 저장 해두기 위해 태스크라는 것을 만든다.

시스템은 알아서 태스크를 관리하지만 우리가 직접 제어해야 하는 경우가 생긴다.

이를 위해 메니페스트 파일에 액티비티를 등록할 때 태스크도 함께 설정할 수 있다.

**예제를 통해 알아보자**

텍스트뷰와 버튼이 있는 액티비티의  MainActivity.java 파일에서 버튼을 눌렀을 때

인텐트를 사용해 MainActivity 화면을 띄울 수 있도록 코드를 입력해보자.


```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = findViewById(R.id.button);
        button.setOnClickListener((v) -> {
            Intent intent = new Intent(getApplicationContext(), MainActivity.class);
            startActivity(intent);
        });
    }
}
```

이렇게 하면 버튼을 누를 때마다 첫 화면이 반복해서 나오게 된다.

그 후 돌아가기 버튼을 클릭하면 동일한 화면이 여러 개 중첩이 되어 있다는 것을 확인할 수 있다.

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148647946-5574cc63-163f-422d-ae13-a0a40b49f4a7.gif)

---

AndroidManifest.xml 파일에서 MainActivity를 등록하는 <activity> 태그에 launchMode 속성을 추가하고

그 값을 standrad로 한 것과 같다.  다시 말하면, 태스크는 새로 뜨는 화면을 차례대로 스택에 넣어서 관리한다.

이번에는 Manifiest 파일에서 activity 태그 안에 launchMode 속성을 추가해 보자.

```xml
<activity  
  android:name=".MainActivity"  
  android:launchMode="singleTop"  
  android:exported="true">  
 <intent-filter>  
 <action android:name="android.intent.action.MAIN" />  
  
 <category android:name="android.intent.category.LAUNCHER" />  
 </intent-filter>  
</activity>
```

**이렇게 singleTop으로 설정하면 태스크의 가장 위쪽에 있는 액티비티는 더 이상 새로 만들지 않게 되고,**

**버튼을 여러 번 누른 후 Back 버튼을 누르면 이전과 달리 앱의 화면이 사라지게 된다.**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148647892-b42ccb9a-64a2-4d0b-a75b-bbf31f954d54.gif)


<br>

## 액티비티의 수명주기와 sharedPreferences

액티비티는 액티비티 스택에 보관하다가 현재 화면에 보이는 액티비티가 사라지면 다시 이전 액티비티가

동작하는 과정을 거친다. 이 때문에 하나의 액티비티가 화면에 보이거나 보이지 않게 되었을 때 다른 

액티비티의 상태에 영향을 미칠 수 있다. 

예를 들어, 우리가 만든 앱이 실행되는 도중에 전화가 오면 통화 앱이 화면에 나타나기 때문에 우리가 만든

앱 화면은 다른 화면 뒤로 들어가 중지될 수 있다.


이렇게 액티비티는 처음 실행될 때 메모리에 만들어지는 과정부터 시작해서 실행과 중지, 그리고 메모리에서 해

제되는 여러 과정의 상태 정보로 갖고 있으며, 이런 상태 정보는 시스템이 관리 하면서 각각의 상태에 해당하는 

메서드를 자동으로 호출하게 된다. 

**액티비티의 대표적인 상태 정보는 아래와 같다.**


|상태  | 설명 |
|:--:|:--:|
|실행(Running)  | 화면상에 액티비티가 보이면서 실행되어 있는 상태<br> 액티비티 스택의 최상위에 있으며 포커스를 가지고 있음 |
|일시 정지(Paused) |사용자에게 보이지만 다른 액티비티가 위에 있어 포커스를 받지 못하는 상태 <br>대화 상자가 위에 있어 일부가 가려진 경우에 해당함 |
|중지(Stopped)  |다른 액티비티에 의해 완전히 가려져 보이지 않는 상태 |


이렇게 액티비티의 상태 정보가 변화하는 것을 액티비티의 **'수명주기(Life Cycle)'** 또는 

생명주기라 하며 액티비티가 처음 만들어진 후 없어질 때 까지 상태가 변화 하면서 각각에 해당하는 

메서드가 자동으로 호출된다. 

다음은 액티비티 생명주기의 다이어 그램이다. 

![1](https://user-images.githubusercontent.com/54762273/148648233-6ae4d491-825a-4a68-b46e-b31500e5945d.jpg)

--- 
***액티비티의 상태 메서드***


|상태메서드|설명|
|:---:|:---:|
|onCreate()  |액티비티가 처음 만들어졌을 때 호출된다.<br>화면에 보이는 뷰들의 일반적인 상태를 설정하는 부분이다.<br>이전 상태가 저장되어 있는 경우엔 번들 객체를 참조하여 이전 상태 복원이 가능하다. |
|onStart() |액티비티가 화면에 보이기 바로 전에 호출된다.<br>액티비티가 화면 상에 보이면 이 메소드 다음에 onResume() 메소드가 호출된다.<br>액티비티가 화면에서 가려지게 되면 이 메소드 다음에 onStop() 메소드가 호출된다.|
|onResume() |액티비티가 사용자와 상호작용하기 바로 전에 호출된다.<br>onPause()에서 저장한 데이터를 다시 복원하는 기능을 한다.|
|onRestart() |액티비티가 중지된 이후에 호출되는 메소드로 다시 시작되기 바로 전에 호출된다. <br>이 메소드 다음에는 항상 onStart() 메소드가 호출된다.|
|onPause()|또 다른 액티비티를 시작하려고 할 때 호출된다.<br>저장되지 않은 데이터를 저장소에 저장하거나,<br> 애니메이션 중인 작업을 중지하는 등의 기능을 수행한다.<br>이 메소드가 리턴하기 전에는 다음 액티비티가 시작될 수 없으므로 매우 빨리 수행되어야 한다.<br>액티비티가 이 상태에 들어가면 시스템은 액티비티를 강제 종료할 수 있다.|
|onStop() |액티비티가 사용자에게 더 이상 보이지 않을 때 호출된다.<br>액티비티가 소멸되거나 또 다른 액티비티가 화면을 가릴 때 호출된다.<br>액티비티가 이 상태에 들어가면 시스템은 액티비티를 강제 종료할 수 있다.|
|onDestroy() |액티비티가 소멸되어 없어지기 전에 호출된다.<br>액티비티가 앱에 의해 종료되거나(finish() 메소드 호출) 시스템이 강제로 종료시키는 경우에 호출될 수 있다.|


onPause 메서드 안에서 데이터를 저장하고 onResum 메서드안에서 복원할 수 있지만,

SharedPreferences를 사용하여 앱 안에서 간단한 데이터를 저장하거나 복원할 수 있다.

### 상태 주기 확인 및 SharedPreferenecs 예제

MainActivity.java
```java
public class MainActivity extends AppCompatActivity {
    EditText nameInput;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = findViewById(R.id.button);
        button.setOnClickListener((v) -> {
            Intent intent = new Intent(getApplicationContext(), MenuActivity.class);
            startActivity(intent);
        });
        Toast.makeText(this, "onCreate 호출됨", Toast.LENGTH_LONG).show();
        println("onCreate 호출됨");
    }
    @Override
    protected void onStart() {
        super.onStart();

        Toast.makeText(this, "onStart 호출됨", Toast.LENGTH_LONG).show();
        println("onStart 호출됨");
    }

    @Override
    protected void onStop() {
        super.onStop();

        Toast.makeText(this, "onStop 호출됨", Toast.LENGTH_LONG).show();
        println("onStop 호출됨");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();

        Toast.makeText(this, "onDestroy 호출됨", Toast.LENGTH_LONG).show();
        println("onDestroy 호출됨");
    }

    @Override
    protected void onPause() {
        super.onPause();

        Toast.makeText(this, "onPause 호출됨", Toast.LENGTH_LONG).show();
        println("onPause 호출됨");

        saveState();
    }

    @Override
    protected void onResume() {
        super.onResume();

        Toast.makeText(this, "onResume 호출됨", Toast.LENGTH_LONG).show();
        println("onResume 호출됨");

        restoreState();
    }

    public void println(String data) {
        Toast.makeText(this, data, Toast.LENGTH_LONG).show();
        Log.d("Main", data);
    }

    protected void restoreState() {
		// 저장소 이름을 pref로 설정
		// SharedPreferences를 사용하려면 getSharedPreferences로 참조 해야함
        SharedPreferences pref = getSharedPreferences("pref", Activity.MODE_PRIVATE);
        if ((pref != null) && (pref.contains("name"))) {
            String name = pref.getString("name", "");
            nameInput.setText(name);
        }
    }

    protected void saveState() {
        SharedPreferences pref = getSharedPreferences("pref", Activity.MODE_PRIVATE);
        SharedPreferences.Editor editor = pref.edit();
        editor.putString("name", nameInput.getText().toString());
        editor.commit();
    }

    protected void clearState() {
        SharedPreferences pref = getSharedPreferences("pref", Activity.MODE_PRIVATE);
        SharedPreferences.Editor editor = pref.edit();
        editor.clear();
        editor.commit();
    }
```

MenuActivity 
```java
public class MenuActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_menu);

        Button button = findViewById(R.id.button2);
        button.setOnClickListener((v)->{
            finish();
        });
    }
```