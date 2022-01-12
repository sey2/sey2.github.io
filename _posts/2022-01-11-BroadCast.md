---
title: "Broad Cast Receiver"
author: seyoung
date: '2022-01-11 12:50:00 +0900'
categories: Android AppComponents
tags: [android,viewpager]
math: true
mermaid: true
---

## Broadcast

안드로이드에서 브로드캐스팅 (BroadCasting)이란 메시지를 여러 객체에 전달하는 것을 말한다.

예를 들어 카카오톡에서 여러 사람에게 메시지를 전달할 때 그룹 채팅방을 만들어

메시지를 전달하는것을 생각해보자. 마찬가지로 안드로이드도 여러 앱 구성 요소에

메시지를 전달할 때 브로드 캐스팅을 사용한다.

우리가 만든 앱에서 브로드캐스팅 메시지를 받고 싶다면 브로드캐스트 수신자를 

만들어 앱에 등록하면 된다. 

브로드캐스트도 앱의 구성 요소이기 때문에 수신자를 만들면 새로 등록해야 시스템이 알 수 있다

Mainfest 파일에 등록하는 Service와는 달리,

 Broadcast Recevier는 소스코드에서 registerReceiver 메서드를 사용해 시스템에 등록할 수 있다.

**브로드 캐스트 수신자 동작 방식**
![2](https://user-images.githubusercontent.com/54762273/148795587-1a80fe3d-622d-43c1-9bb5-2692c5341a90.png)
> 출처 : Do it 안드로이드 프로그래밍 


---

### 브로드 캐스트 수신자 등록 예시


브로드 캐스트 수신자에는 onReceive 메서드를 정의해야 한다.

이 메서드는 원하는 브로드캐스트 메시지가 도착하면 자동으로 호출된다.

시스템의 모든 메시지를 받을 수 없기 때문에 원하는 메시지만 받으려면 

인텐트 필터를 사용해 시스템에 등록하면 된다.

![1](https://user-images.githubusercontent.com/54762273/148781852-bc42c4f1-3189-4016-b52f-3ebf3e72105e.PNG)


위 사진같이 브로드캐스트 클래스를 만들어주자 Class Name은 SmsReceiver로 한다.

이렇게 하면 Manifest.xml 파일에 reciver태그가 자동으로 추가 되는데,

브로드캐스트 수신자를 등록하기 위해 아래 처럼 수정하자. <br><br>

**AndroidManifest.xml**

```xml
 <receiver
            android:name=".SmsReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.provider.Telephony.SMS_RECIVED"/>
            </intent-filter>
        </receiver>
```

위의 intent-filter 태그는 어떤 인텐트를 받을 것인지 지정해 주고,

action 태그의 android.provider.Telephony.SMS_RECIVED는 메시지가 들어간 인텐트를

구분하기 위한 액션 정보이다. 즉, 단말에서 SMS를 수신했을 때 이 action 정보가 들어간

인텐트를 전달 하므로 이 값을 넣어주면 SMS를 받아 볼 수 있다.

인제 아래와 같이 SmsReceiver.java 파일을 수정해주자 <br><br>

**SmsReceiver.java**

```java
public class SmsReceiver extends BroadcastReceiver {
    public static final String TAG = "SmsReceiver";
    
    @Override
    public void onReceive(Context context, Intent intent) {
        Log.i(TAG, "onReceive() 메서드 호출됨.");

        Bundle bundle = intent.getExtras(); // 인텐트에서 Bundle 객체 가져오기
        SmsMessage[] messages = parseSmsMessage(bundle);
        
        if(messages != null && messages.length > 0){
			// getOriginatingAddress() 발신자 번호 확인하는 메서드
            String sender = messages[0].getOriginatingAddress();
            Log.i(TAG, "SMS sender : " + sender);
            
            // 문자 내용을 확인하려면 getMessageBody().toString() 
            String contents = messages[0].getMessageBody();
            Log.i(TAG, "SMS contents : " + contents);
            
            Date receivedDate = new Date(messages[0].getTimestampMillis());
            Log.i(TAG, "SMS received date : " + receivedDate.toString());
        }
        throw new UnsupportedOperationException("Not yet implemented");
    }
    
    private SmsMessage[] parseSmsMessage(Bundle bundle){
        Object[] objs = (Object[]) bundle.get("pdus");
        SmsMessage[] messages = new SmsMessage[objs.length];
        
        int smsCount = objs.length;
        for(int i=0; i<smsCount; i++){
            if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
                String format = bundle.getString("format");
                messages[i] = SmsMessage.createFromPdu((byte[]) objs[i], format);
            }else
                messages[i] = SmsMessage.createFromPdu((byte[]) objs[i]); 
        }
        return messages;
    }
}
```

위의 parseSmsMessage 메서드는 한 번 만들어 놓으면 다른 앱을 만들 때도 재사용할 수 있다.

왜냐하면 SMS 데이터를 확인할 수 있도록 안드로이드 API에 정해둔 코드이기 때문이다.

 if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)에서,

 Build.VERSION_CODE에는 안드로이드 OS 버전별로 상수가 정의되어 있다.

M은 마시멜로 버전과 같거나 그 이후 버전일 때 중괄호 안의 코드를 실행하겠다는 뜻이다.

인제 SMS를 수신하려면 RECEIVE_SMS 권한이 있어야 한다. 

아래와 같이 코드를 수정하자. 


**AndroidManifest.xml** 

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.techtown.recevier">

    <uses-permission android:name="android.permission.RECEIVE_SMS"></uses-permission>>
```

**build.gradle(Module: SampleReceiver.app)**

```xml
dependencies {
	...
    implementation 'com.yanzhenjie:permission:2.0.3'
}
```

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        AndPermission.with(this)// 모든 위험 권한을 자동으로 부여하는 메서드 호출
                .runtime()
                .permission(Permission.RECEIVE_SMS)
                .onGranted(new Action<List<String>>() {
                    @Override
                    public void onAction(List<String> permissions) {
                        showToast("허용된 권한 갯수 : " + permissions.size());
                    }
                })
                .onDenied(new Action<List<String>>() {
                    @Override
                    public void onAction(List<String> permissions) {
                        showToast("거부된 권한 갯수 : " + permissions.size());
                    }
                })
                .start();

    }

    public void showToast(String message) {
        Toast.makeText(this, message, Toast.LENGTH_LONG).show();
    }

}
```
인제 앱을 실행했을 때 메인 액티비티가 화면에 보이면 권한을 요청하는 대화상자가 나타난다.

![2](https://user-images.githubusercontent.com/54762273/148781966-ed8988d8-26b3-4816-a5ca-d3bf08922d52.PNG)


Allow 버튼을 누르고 권한이 승인되고 SMS 받을 준비가 완료된다.

![3](https://user-images.githubusercontent.com/54762273/148782004-c27b9502-8aa5-4c41-938d-a4e702100fc7.PNG)

위 사진 처럼 메시지를 작성하고 SendMessage를 누르면 Logcat창에서,

onReceive 메서드가 호출되었다는 것을 알 수 있다.


### SMS 내용 액티비티에 나타내기


**app 폴더에서 New Activity -> Empty**

![4](https://user-images.githubusercontent.com/54762273/148792788-49bd7bc4-8de1-4773-a974-7b3e3ed47185.PNG)

만들어진 activity_main은 위 처럼 꾸미고 SmsActivity.java 파일을 아래와 같이 수정.

**SmsActivity.java**

```java
public class SmsActivity extends AppCompatActivity {
    EditText editText;
    EditText editText2;
    EditText editText3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_sms);

        editText = findViewById(R.id.editText);
        editText2 = findViewById(R.id.editText2);
        editText3 = findViewById(R.id.editText3);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                finish(); // 화면을 닫는다.
            }
        });
		
		// 전달 받은 인텐트를 처리 하는 processIntent 호출
        Intent passedIntent = getIntent();
        processIntent(passedIntent);
    }

    @Override
    protected void onNewIntent(Intent intent) {
        processIntent(intent);

        super.onNewIntent(intent);
    }

    private void processIntent(Intent intent) {
			// Intent가 null이 아니면 그 안에 있는 부가 데이터를 화면에 보여주기
        if (intent != null) {
            String sender = intent.getStringExtra("sender");
            String contents = intent.getStringExtra("contents");
            String receivedDate = intent.getStringExtra("receivedDate");

            editText.setText(sender);
            editText2.setText(contents);
            editText3.setText(receivedDate);
        }
    }

}
```

수신한 SMS를 보여줄 화면까지 만들었으므로 SmsReceiver.java  파일을 열어

SmsActivity 인텐트를 전달하는 코드를 추가한다. <br><br>

**SmsReceiver.java**
```java

public class SmsReceiver extends BroadcastReceiver {
    private static final String TAG = "SmsReceiver";

    public SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Override
    public void onReceive(Context context, Intent intent) {
        Log.i(TAG, "onReceive() 메서드 호출됨.");

        Bundle bundle = intent.getExtras();
        SmsMessage[] messages = parseSmsMessage(bundle);
        if (messages != null && messages.length > 0) {
            String sender = messages[0].getOriginatingAddress();
            Log.i(TAG, "SMS sender : " + sender);

            String contents = messages[0].getMessageBody();
            Log.i(TAG, "SMS contents : " + contents);

            Date receivedDate = new Date(messages[0].getTimestampMillis());
            Log.i(TAG, "SMS received date : " + receivedDate.toString());

            sendToActivity(context, sender, contents, receivedDate);
        }
    }

    private SmsMessage[] parseSmsMessage(Bundle bundle) {
        Object[] objs = (Object[]) bundle.get("pdus");

        SmsMessage[] messages = new SmsMessage[objs.length];
        int smsCount = objs.length;
        for (int i = 0; i < smsCount; i++) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                String format = bundle.getString("format");
                messages[i] = SmsMessage.createFromPdu((byte[]) objs[i], format);
            } else {
                messages[i] = SmsMessage.createFromPdu((byte[]) objs[i]);
            }
        }

        return messages;
    }

		/* SmsActivity로 인텐트를 보내기 위한 메서드 */
    private void sendToActivity(Context context, String sender, String contents, Date receivedDate) {
        Intent myIntent = new Intent(context, SmsActivity.class);
        myIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK|Intent.FLAG_ACTIVITY_SINGLE_TOP|Intent.FLAG_ACTIVITY_CLEAR_TOP);
        myIntent.putExtra("sender", sender);
        myIntent.putExtra("contents", contents);
        myIntent.putExtra("receivedDate", format.format(receivedDate));
        context.startActivity(myIntent);
    }

}
```