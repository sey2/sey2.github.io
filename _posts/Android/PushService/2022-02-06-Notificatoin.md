---
title: "상단 알림으로 알려주기"
author: seyoung
date: '2022-02-06 20:22:00 +0900'
categories: Android PushService
tags: [android,pushservice, alerm]
math: true
mermaid: true
---


## 상단 알림으로 알려주기

알림은 화면 상단에 정보를 표시하여 사용자가 알 수 있도록 한다.

이 알림 기능은 주로 다른 사람에게서 메시지를 받았을 때나 단말의 상태를 표시할 때 사용한다.

예를 들어 카카오톡 앱을 직접 만든다면 앱이 실행되지 않은 상태에서도 사용자에게 메시지가 왔다는 것을 알려줄 필요가 있다.

이때 백그라운드에서 동작하는 서비스에서 알림을 표시하면 사용자에게 알려줄 수 있다.

알림은 **NotificationManager** 시스템 서비스를 이용해 화면 상단에 띄울 수 있다.

알림을 띄우려면  **Notification** 객체를 만들어야 하는데 이 객체는  **NotificationCompat.Builder** 객체를 이용해서 만들 수 있다.

---
### 예제


**MainActivity.java**
```java
package org.techtown.noti;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.NotificationCompat;

import android.app.Notification;
import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {
    NotificationManager manager;

    private static String CHANNEL_ID = "channel1";
    private static String CHANNEL_NAME = "Channel1";

    private static String CHANNEL_ID2 = "channel2";
    private static String CHANNEL_NAME2 = "Channel2";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showNoti1();
            }
        });

        Button button2 = findViewById(R.id.button2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showNoti2();
            }
        });
    }

    public void showNoti1() {
        manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);

        NotificationCompat.Builder builder = null;

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            manager.createNotificationChannel(new NotificationChannel(
                    CHANNEL_ID, CHANNEL_NAME, NotificationManager.IMPORTANCE_DEFAULT)
            );

            builder = new NotificationCompat.Builder(this, CHANNEL_ID);
        } else {
            builder = new NotificationCompat.Builder(this);
        }

        builder.setContentTitle("간단 알림");
        builder.setContentText("알림 메시지입니다.");
        builder.setSmallIcon(android.R.drawable.ic_menu_view);
        Notification noti = builder.build();

        manager.notify(1, noti);
    }

    public void showNoti2() {
        manager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);

        NotificationCompat.Builder builder = null;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            manager.createNotificationChannel(new NotificationChannel(
                    CHANNEL_ID2, CHANNEL_NAME2, NotificationManager.IMPORTANCE_DEFAULT
            ));

            builder = new NotificationCompat.Builder(this, CHANNEL_ID2);
        } else {
            builder = new NotificationCompat.Builder(this);
        }

        Intent intent = new Intent(this, MainActivity.class);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 101, intent,
                                                                PendingIntent.FLAG_UPDATE_CURRENT);

        builder.setContentTitle("간단 알림");
        builder.setContentText("알림 메세지 입니다.");
        builder.setSmallIcon(android.R.drawable.ic_menu_view);
        builder.setAutoCancel(true);
        builder.setContentIntent(pendingIntent);

        Notification noti = builder.build();

        manager.notify(2, noti);
    }
}
```

`showNoti1` 메서드 안에 **NotificationManager** 객체를 참조한 후 **NotificationCompat.Builder** 객체를 생성한다.

안드로이드 오레오 버전 이전과 이후에 Builder 객체를 만드는 방법이 다르기 때문에 **Build.VERSION.SDK_INT** 상수의 값을 비교하여 단말의 OS 버전에 따라 다른 코드가 실행되도록 한다.

오레오 버전 이후 버전에서는 알림 채널이 지정되어야 하며, 채널은 `createNotificationChannel`메서드를 이용해 생성할 수 있다.

**Builder** 객체가 만들어지면 알림 정보를 설정할 수 있고 Builder 객체의 `build` 메서드를 호출하면 **Notification** 객체가 생성된다.

**NotificationManager**의 notify 메서드를 호출하면서 이 Notification 객체를 매개변수로 전달하면 알림을 띄우게 된다.

또한 `showNoti2` 메서드는 버튼을 눌렀을 때**PendingIntent**를 만들어 **Notification**을 만들 때 설정한다.
**PedingIntent**는 **Intent**와 비슷하지만 시스템에서 대기하는 역할을한다.

예를 들어 액티비티를 띄우는 역할을 하는 메서드가 `startActivity` 또는 `startActivityForResult`인데 이 메서드를 호출하면 시스템에서는 즉시 해석하고 처리한다.

하지만 **PendingIntent**는 지정된 상황이 될 때까지 보관하고 있게 된다.

`setAutoCancel` 메서드는 알림을 클릭했을 때 자동으로 알림 표시를 삭제하라는 설정이다.

`setContentIntent` 메서드에는 PendingIntent 객체가 매개 변수로 전달된다.
그리고 PendingIntent 객체에는 Intent 객체가 매개 변수로 전달되는데 이렇게 되면 알림을 클릭했을 때 이 Intent 객체를 이용해 액티비티를 띄워준다. 액티비티는 MainActivity가 뜨도록 MainActivity.class로 지정했다.

---

**결과**

첫 번째 버튼을 클릭하면 상단 바에 알람이 띄워지게 되고
두 번째 버튼을 클릭하면 상단 바에 알람이 띄워지고 알림을 클릭하면 MainActivity 화면으로 돌아온다.

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/152678325-bcaaf5c3-fdcd-4c66-832c-ae9cce341e6d.gif)

---

알름을 표시하는 방법은 이것 외에도 글자를 많이 표시하거나 이미지를 표시하거나 또는 목록을 표시하는 방법 등이 있다.

이것을 스타일 알림 **(Styled Notification)** 이라고 부른다.
