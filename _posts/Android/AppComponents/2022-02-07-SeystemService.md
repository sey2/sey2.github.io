---
title: "시스템 서비스 활용하기"
author: seyoung
date: '2022-02-07 03:18:00 +0900'
categories: Android AppComponents
tags: [android,service]
math: true
mermaid: true
---


## 시스템 서비스 활용하기

**시스템 서비스 (System Service)**는 단말이 켜졌을 때 자동으로 실행되어 백그라운드에서 동작한다.
이런 시스템 서비스 중에 인플레이션을 위한 시스템 서비스나 센서를 위한 시스템 서비스 등을 포스팅 했다.

안드로이드에서는 다양한 시스템 서비스가 제공되는데 그 중에서 **ActivityManager, PackageManager,** **AlarmManager** 에 대해 알아보자.

**ActivityManager**는 액티비티나 서비스를 관리하는 시스템 서비스로 앱의 실행 상태를 알 수 있도록 한다.

**PackagerManager**는 앱의 설치에 대한 정보를 알 수 있도록 하며 **AlarmManager**는 일정 시간에 알림을 받을 수 있도록 시스템에 등록해주는 역할을 한다.

예제를 통해 알아보자.

---

### 예제 

**MainActivity.java**

```java
package org.techtown.manager;

import androidx.appcompat.app.AppCompatActivity;

import android.app.ActivityManager;
import android.app.AlarmManager;
import android.app.PendingIntent;
import android.content.Context;
import android.content.Intent;
import android.content.pm.ApplicationInfo;
import android.content.pm.PackageManager;
import android.content.pm.ResolveInfo;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import java.util.List;

public class MainActivity extends AppCompatActivity {
    TextView textView;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                getServiceList();
            }
        });

        Button button2 = findViewById(R.id.button2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                getCurrentActivity();
            }
        });

        Button button3 = findViewById(R.id.button3);
        button3.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                getAppList();
            }
        });

        Button button4 = findViewById(R.id.button4);
        button4.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                findActivity();
            }
        });

        Button button5 = findViewById(R.id.button5);
        button5.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setAlarm();
            }
        });

    }

    public void getServiceList(){
        ActivityManager manager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);

        // 실행 중인 프로세스 확인을 위한 메서드 호출하기
        List<ActivityManager.RunningAppProcessInfo> processInfoList = manager.getRunningAppProcesses();

        for(int i=0; i < processInfoList.size(); i++){
            ActivityManager.RunningAppProcessInfo info = processInfoList.get(i);
            println("#" + i + " -> " + info.pid + ", " + info.processName);
        }
    }

    public void getCurrentActivity(){
        ActivityManager manager = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
        
        // 액티비티 스택에 들어있는 액티비티 정보 중에서 가장 최상위의 정보를 확인
        List<ActivityManager.RunningTaskInfo> taskList = manager.getRunningTasks(1);

        ActivityManager.RunningTaskInfo info = taskList.get(0);
        println("Running Task -> " + info.topActivity.toString());
     }

     public void getAppList(){
        PackageManager manager = getPackageManager();
        
        // 어떤 앱이 설치되어 있는지 확인하기 위한 메서드
        List<ApplicationInfo> appInfoList = manager.getInstalledApplications(PackageManager.GET_META_DATA);

        for(int i=0; i < appInfoList.size(); i++){
            ApplicationInfo info = appInfoList.get(i);
            println("#" + i + " -> " + info.loadLabel(manager).toString() + ", " + info.packageName);
        }
     }

     public void findActivity(){
        PackageManager manager = getPackageManager();

        Intent intent = new Intent(this, MainActivity.class);
        
        // 실행할 액티비티가 있는지 확인하기 위한 메서드 
        List<ResolveInfo> activityInfoList = manager.queryIntentActivities(intent, 0);

        for(int i=0; i<activityInfoList.size(); i++){
            ResolveInfo info = activityInfoList.get(i);
            println("#" + i + " -> " + info.activityInfo.applicationInfo.packageName);
        }
     }

     public void setAlarm(){
         AlarmManager manager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);

         Intent intent = new Intent(this,MainActivity.class);
         PendingIntent pendingIntent = PendingIntent.getActivity(this, 101, intent,
                                                                  PendingIntent.FLAG_CANCEL_CURRENT);

         // Alarm Manager에 알람 등록하기
         manager.set(AlarmManager.RTC, System.currentTimeMillis() + 60000, pendingIntent);
     }

     public void println(String data) { textView.append(data + "\n");}
}
```

---

### 결과

![1](https://user-images.githubusercontent.com/54762273/152695206-8e0347f9-bf1d-4d7b-8025-53a6df141e27.PNG)