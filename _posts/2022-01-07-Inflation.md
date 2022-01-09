---
title: "Infaltion, Infalator"
author: seyoung
date: '2022-01-07 19:05:00 +0900'
categories: Android Layout_And_View 
tags: [android,progressbar,bar,snakbar,toast,dialog]
math: true
mermaid: true
---
## 레이아웃 인프레이션

XML 레이아웃만 만들고 화면을 띄우고 앱을 실행 할 수는 없다. 

화면의 기능을 담당하는 소스 코드 파일이 필요하기 때문이다. 

XML 레이아웃 파일과 하나의 소스 코드 파일이 어디서 연결되는지 어떻게 알까 ?

**MainActivity** 파일에서,

MainActivity 클래스는 AppCompatActivity 클래스를 상속하고 이 클래스는 화면에 필요한 메서드들이 있다는데,

 AppCompatActivity 클래스의  **setContentView** 메서드가 XML 레이아웃 파일을 연결해 준다.

**setContentView 메서드로 전달하는 XML 레이아웃 파일 이름은 확장자 없이 지정해 주어야 한다.**

> **R.layout.레이아웃 파일명**

```java 
public void setContentView (int layoutResID)
public void setContentView (View view, [, ViewGroup.LayoutParams params])
```

위에서 R은 프로젝트 창에 보이는 res 폴더를 의미하고, layout은 res 폴더의 layout 폴더를 의미한다.

즉 앱이 실행될 때, XML 레이아웃의 내용이 메모리에 객체화되고 객체화된 XML 레이아웃을 소스파일에서

사용한다. 이렇게 XML 레이아웃의 내용이 메모리에 객체화되는 과정을 **인플레이션(Inflation)** 이라고 한다.

![1](https://user-images.githubusercontent.com/54762273/148519699-732cfdfd-423a-403d-bc7d-b5f91272dc1a.jpg)


XML 레이아웃은 앱이 실행되는 시점에 메모리에 객체화 되고, 

XML 레이아웃 파일에 Button 태그를 정의해도 앱은 자신이 실행되기 전까지 버튼이 있는지 모른다. 

---

### 예시 

이 과정을 확인하기 위해 버튼을 하나 추가하고 setContentView 메서드 위에 버튼을 찾아 변수를 할당하고

setOnClickListener 메서드를 호출하는 코드를 입력해 보았다.


![1](https://user-images.githubusercontent.com/54762273/148520301-37497101-be4c-4ef7-8880-efd494a874bc.PNG)


이렇게 하면 위와 같이 앱이 실행되지 않고 오류를 발생한다. 

이렇게 오류가 발생하는 이유는 메모리에 객체화되지 않은 버튼 객체를 참조하려 해서이다.

<br>

## 부분 화면을 보여주는 인플레이터

setContentView 메서드는 액티비티의 화면 전체(메인 레이아웃)를 설정하는 역할만 수행한다.

즉, setContentView메서드로는 부분 화면(부분 레이아웃)을 메모리에 객체화 할 수 없다.

부분 화면을 메모리에 객체화 하려면 인플레이터를 사용해야 한다.

안드로이드는 LayoutInlater라는 클레스를 제공하고 이 클래스는 시스템 서비스를 제공하는 클래스이므로, 

다음 getSystemService 메서드를 이용하여 LayoutInflater 객체를 참조한 후 사용해야 한다.

```java
getSystemService(Context.LAYOUT_INFLATER_SERVICE)
```

![1](https://user-images.githubusercontent.com/54762273/148522052-2201536e-90d2-4b4f-98b6-4017c2a7d0e3.jpg)

메인 레이아웃은 소스 코드에서 setContentView(R.layout.activity_main)와 같은 방법으로 객체화 하여

화면에 나타낸다. 그 중 일부 화면을 분리한 부분화면은 LayoutInflater 객체를 사용해 뷰 그룹 객체로 

객체화 (인플레이션)한 후 메인 레이아웃에 추가 해야한다. 

### 실습

**app -> New -> Activity -> Empty Activity**

![1](https://user-images.githubusercontent.com/54762273/148522730-e4dddb9e-46d4-4e7a-a934-d525d8db35e4.jpg)

**MenuActivity 생성**

![1](https://user-images.githubusercontent.com/54762273/148522896-05bb11b4-f9ec-40cc-9477-840fb60a5c39.PNG)


이렇게 하면 activity_menu 클래스와 xml 파일이 만들어진다.  


그 후 menu xml 을 아래와 같이 수정한다.

``` xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  xmlns:app="http://schemas.android.com/apk/res-auto"  
  xmlns:tools="http://schemas.android.com/tools"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:orientation="vertical"  
  tools:context=".MenuActivity">  
  
 <TextView  
  android:id="@+id/textView"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:text="TextView" />  
  
 <Button  
  android:id="@+id/button2"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:text="Button" />  
  
 <LinearLayout  
  android:id="@+id/container"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:orientation="vertical"></LinearLayout>  
</LinearLayout>
```

인제 버튼을  클릭 했을 때 새로운 XML 레이아웃이 안쪽에 있는 Linear Layout에 나타나게 해볼거다.

그러기 위해서는 부분 화면으로 추가할 XML 레이아웃도 만들어 줘야하는데, 

/app/res/layout 폴더 선택 -> 마우스 오른쪽 클릭 -> new ->Layout resource file 선택

이름은 sub1.xml, Root element는 리니어 레이아웃으로 설정해 봅시다. 

그 후 sub1.xml 파일은 

```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  android:orientation="vertical"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent">  
  
 <TextView  
  android:id="@+id/textView"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:text="부분 화면 1"  android:textSize="30sp"/>  
  
 <CheckBox  
  android:id="@+id/checkBox"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:text="동의합니다"/>  
  
  
</LinearLayout>
```
이렇게 하면 부분 화면은 아래와 같이 나온다. 
![1](https://user-images.githubusercontent.com/54762273/148524308-7ec352cd-6aa5-490c-b2eb-b0c4f77c2528.PNG)

이제 전체 레이아웃에 부분 레이아웃을 추가하기 위해 activity_menu.xml 파일의 짝인 MenuActivity.java 파일을

수정해 보자.

```java
public class MenuActivity extends AppCompatActivity {

    LinearLayout container;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_menu);

        container = findViewById(R.id.container);

        Button button = findViewById(R.id.button2);
        button.setOnClickListener((v)->{
            LayoutInflater inflater = (LayoutInflater)
                    getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            // container에 sub1 xml 화면을 띄워라.
            inflater.inflate(R.layout.sub1,container,true);
            CheckBox checkBox = container.findViewById(R.id.checkBox);
            checkBox.setText("로딩이 되었어요.");
        });
    }
}
```

그 후 부분화면이 나올 수 있게Manifest.xml 을 아래와 같이 수정하자.

```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
  package="org.techtown.myapplication">  
  
 <application  
  android:allowBackup="true"  
  android:icon="@mipmap/ic_launcher"  
  android:label="@string/app_name"  
  android:roundIcon="@mipmap/ic_launcher_round"  
  android:supportsRtl="true"  
  android:theme="@style/Theme.MyApplication">  
 <activity android:name=".MainActivity"></activity>  
 <activity android:name=".MenuActivity"  
  android:exported="true">  
 <intent-filter>  
 <action android:name="android.intent.action.MAIN" />  
  
 <category android:name="android.intent.category.LAUNCHER" />  
 </intent-filter>  
 </activity>  
 </application>  
  
</manifest>
```

**결과**
  

![1](https://user-images.githubusercontent.com/54762273/148527598-e006ac25-f360-498f-ae05-37bbe2865bbd.PNG)
