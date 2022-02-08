---
title: "네트워크 상태 확인"
author: seyoung
date: '2022-02-07 03:40:00 +0900'
categories: Android Network
tags: [android,network]
math: true
mermaid: true
---

## 네트워크 상태 확인

단말의 인터넷 연결 상태는 어떻게 알 수 있을까?

인터넷에 연결되어 있다면 일반망에 연결되어 있는지, 무선랜(WiFi)에 연결 되었는지 어떻게 확인할까?

인터넷의 연결 상태에 따라 사용자에게 보여줄 결과물을 다르게 만들고 싶은 경우가 있다.

예제를 통해 알아보자.

---

### 예제 

**MainActivity.java**

```java
package org.techtown.network;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.net.ConnectivityManager;
import android.net.NetworkInfo;
import android.net.wifi.WifiManager;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {
    TextView textView;

    WiFiReceiver wifiReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                checkConnectivity();
            }
        });

        wifiReceiver = new WiFiReceiver();
    }

    public void checkConnectivity() {
        ConnectivityManager manager = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo info = manager.getActiveNetworkInfo();
        if (info != null) {
            if (info.getType() == ConnectivityManager.TYPE_WIFI) {
                println("WiFi로 설정됨");
            } else if (info.getType() == ConnectivityManager.TYPE_MOBILE) {
                println("일반망으로 설정됨");
            }

            println("연결 여부 : " + info.isConnected());
        } else {
            println("데이터통신 불가");
        }

    }

    public void println(String data) {
        textView.append(data + "\n");
    }

    @Override
    protected void onPause() {
        super.onPause();

        unregisterReceiver(wifiReceiver);
    }

    @Override
    protected void onResume() {
        super.onResume();

        IntentFilter filter = new IntentFilter();   // 코드에서 수신자 등록하기
        filter.addAction(WifiManager.WIFI_STATE_CHANGED_ACTION);
        filter.addAction(WifiManager.NETWORK_STATE_CHANGED_ACTION);
        registerReceiver(wifiReceiver, filter);

    }

    class WiFiReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            if (action.equals(WifiManager.WIFI_STATE_CHANGED_ACTION)) {     // WiFi 상태 체크하기
                int state = intent.getIntExtra(WifiManager.EXTRA_WIFI_STATE, -1);
                if (state == WifiManager.WIFI_STATE_ENABLED) {
                    println("WiFi enabled");
                } else if (state == WifiManager.WIFI_STATE_DISABLED) {
                    println("WiFi disabled");
                }
            } else if (action.equals(WifiManager.NETWORK_STATE_CHANGED_ACTION)) {
                NetworkInfo info = intent.getParcelableExtra(WifiManager.EXTRA_NETWORK_INFO);
                WifiManager manager = (WifiManager) getApplicationContext().getSystemService(Context.WIFI_SERVICE);
                String ssid = manager.getConnectionInfo().getSSID();

                if (info.getState() == NetworkInfo.State.CONNECTED) {
                    println("Connected : " + ssid);
                } else if (info.getState() == NetworkInfo.State.DISCONNECTED) {
                    println("Disconnected : " + ssid);
                }
            }
        }
    }

}
https://www.acmicpc.net/problem/17215
```

권한 추가

**AndroidManifest.xml**

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>  
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>  
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
```

---
### 결과

![1](https://user-images.githubusercontent.com/54762273/152696156-58c19eec-6243-4dfc-ae56-1e6f30b70624.PNG)