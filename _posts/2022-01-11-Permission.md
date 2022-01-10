---
title: "Dangerous Permission"
author: seyoung
date: '2022-01-11 02:27:00 +0900'
categories: Android AppComponets
tags: [android,viewpager]
math: true
mermaid: true
---

## 위험 권한 부여하기

브로드캐스트 수신자를 만들어 SMS 문자를 받으려면 RECEIVE_SMS 권한이 필요하다.

Manifest에 넣어준 권한은 앱을 설치할 때 사용자가 허용하면 한꺼번에 권한이 부여되는데

마시멜로(API32) 부터는 중요한 권한들을 분류하여 설치 시점이 아니라 앱을 실행 했을 때,

사용자로부터 권한을 부여 받도록 변경 되었다.

--- 

### 일반 권한과 위험 권한의 차이점 

마시멜로 버전부터는 권한을 일반 권한(Normal Permission)과 위험 권한(Dangerous Permission)

으로 나뉘었다. 인터넷을 사용할 때 부여하는 INTERNET 권한 같은 일반 권한은 앱을  설치할 때

사용자에게 권한이 부여되어야 함을 알려주고 설치 할 것인지 물어본다.

그러나 위험 권한으로 분류되는 RECEIVE_SMS의 경우에는 설치 시에 부여한 권한은

의미가 없고 실행 시에 사용자에게 권한을 부여할 것인지 물어보게 된다.

즉, 권한을 부여하지 않으면 해당 기능은 동작하지 않는다.

위치, 카메라, 마이크, 연락처, 전화, 문자, 일정, 센서로 대표되는 위험 권한은 다음과 같은

세부 권한으로 나뉜다.

|위험 권한 그룹의 분류  | 세부 권한 |
|:--:|:--:|
|LOCATION  |ACESS_FINE_LOCATION<br>ACESS_COARSE_LOCATION  |
|CAMERA | CAMERA|
|MICROPHONE| RECORD_AUDIO|
|CONTACTS| READ_CONTACTS<br>WRITE_CONTACTS<br>GET_ACCOUNTS|
|PHONE|READ_PHONE_STATE<br>CALL_PHONE<br>READ_CALL_LOG<br>WRITE_CALL_LOG<br>ADD_VOICEMAIL<br>USE_SIP<br>PROCESS_OUTGOING_CASLLS|
|SMS|SEND_SMS<br>RECEVIE_SMS<br>READ_SMS<br>RECEIVE_WAP_PUSH<br>RECEIVCE_MMS|
|CALENDAR|READ_CALENDAR<br>WRITE_CALENDAR|
|SENSORS|BODY_SENSORS|
|STORAGE|READ_EXTERNAL_STORAGE<br>WRTIE_EXTERNAL_STORAGE|

---

### 기본 방법으로 위험 권한 부여하기

SD 카드를 접근할 때 사용되는 두가지 위험 권한을 부여하기 위해,

먼저 AndroidManifest.xml 파일을 다음과 같이 권한을 추가한다.

**AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.techtown.permission">

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/> 
```

기본 권한을 부여할 때는 <uses-permission> 태그를 사용한ㅂ다.

이렇게 부여한 기본 권한 중에서 SD 카드를 접근하는 권한은 위험 권한이므로

아래와 같이 MainActivity.java 파일에 코드를 추가해 주어야 한다.<br><br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 위험 권한을 부여할 권한 지정하기
        String [] permissions = {
                Manifest.permission.READ_EXTERNAL_STORAGE,
                Manifest.permission.WRITE_EXTERNAL_STORAGE
        };

        checkPermissions(permissions);
    }

    public void checkPermissions(String[] permissions){
        ArrayList<String> targetList = new ArrayList<String>();

        for(int i=0; i<permissions.length; i++){
            String curPermission = permissions[i];

            // 권한이 부여 되어 있는지 확인
            int permissionCheck = ContextCompat.checkSelfPermission(this,curPermission);

            if(permissionCheck == PackageManager.PERMISSION_GRANTED)
                Toast.makeText(this,curPermission + " 권한 있음.", Toast.LENGTH_LONG).show();
            else{
                Toast.makeText(this,curPermission +" 권한 없음.", Toast.LENGTH_LONG).show();
                
                if(ActivityCompat.shouldShowRequestPermissionRationale(this,curPermission))
                    Toast.makeText(this,curPermission + " 권한 설명 필요함.", Toast.LENGTH_LONG).show();
                else
                    targetList.add(curPermission);
            }

        }

        String[] targets = new String[targetList.size()];
        targetList.toArray(targets);
        // 위험 권한 부여 요청
        ActivityCompat.requestPermissions(this,targets,101);
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String permissions[], int [] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        switch (requestCode) { // 요청 코드가 맞는지 확인함
            case 101:
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
                    Toast.makeText(this, "첫 번째 권한을 사용자가 승인함.", Toast.LENGTH_LONG).show();
                else
                    Toast.makeText(this, "첫 번째 권한 거부됨. ", Toast.LENGTH_LONG).show();
                break;
        }
    }
}
```
<br>

이렇게 설정을 해주고 나서 앱을 실행하면 다음과 같이 권한을 요청하는 대화 상자가 나타난다.

![1](https://user-images.githubusercontent.com/54762273/148802880-ddb610a2-090f-484d-b3b5-a1fac5b9f168.PNG)

이렇게 실행했을 때 부여되는 위험 권한이라고 하더라도 사용자가 한 번 수락하면 

앱에 부여된 권한 정보를 단말에서 알고 있기 때문에 앱을 다시 실행해도 대화상자는 더 이상

나타나지 않는다. 

---


### 외부 라이브러리를 이용한 위험 권한 자동 부여 방법 

SamplePermission2 이름을 가진 프로젝트에서 build.gradle(Module:SamplePermission2.app)

파일을 열어서 다음과 같이 추가한다.

**build.gradle(Module:SamplePermission2.app)**

```xml
dependencies {  
  ...
  implementation 'com.yanzhenjie:permission:2.0.3'
}
```
<br>

manifest 파일을 아래와 같이 수정하자. 

**AndroidManifest.xml**

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
  package="org.techtown.permission2">  
 <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>  
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
<br>

그 다음 MainActivity.java 파일에 다음 코드를 추가한다. 

**MainActivity.java**
```java

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        AndPermission.with(this)
                .runtime()
                .permission(Permission.READ_EXTERNAL_STORAGE,Permission.WRITE_EXTERNAL_STORAGE)
                .onGranted(new Action<List<String>>() {
                    @Override
                    public void onAction(List<String> permissions) {
                        showToast("허용된 권한 개수: " + permissions.size());
                    }
                })
                .onDenied(new Action<List<String>>() {
                    @Override
                    public void onAction(List<String> permissions) {
                        showToast("거부된 권한 개수: " + permissions.size());
                    }
                })
                .start();
    }

    public void showToast(String message){
        Toast.makeText(this,message,Toast.LENGTH_LONG).show();
    }
}
```