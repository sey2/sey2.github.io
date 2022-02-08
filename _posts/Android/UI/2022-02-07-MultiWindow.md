---
title: "MultiWindow (다중 창)"
author: seyoung
date: '2022-02-07 16:17:00 +0900'
categories: Android User_Interface
tags: [android,multiwindow]
math: true
mermaid: true
---


## 다중 창 지원하기

다중 창(Multi Window)은 단말 화면에 여러 개의 액티비티가 보이도록 지원하는 기능이다.

이 기능은 API 24부터 지원되므로 예전 단말에서는 볼 수 없는 기능이지만 최근 단말에서는 필요에 따라 사용할 수 있다.

사용자는 다중 창을 만들어 각각의 영역에 액티비티를 보여줄 수 있다.

시스템 메뉴 버튼을 누르면 단말에서 실행된 액티비티들을 볼 수 있는 오버뷰(Overview) 화면이 뜨게 되는데

이 화면에서 액티비티 상단의 아이콘 부분을 클릭하면 다중 창으로 만들 수 있는 메뉴가 표시된다.

이 메뉴를 눌러 화면을 분할한 후 다른 액티비티를 다른 창에 보이도록 선택하면 된다.

이 기능은 사용자가 필요에 따라 만드는 기능이지만 앱에서 별도의 처리를 해주어야 하는 경우도 있다.

예를 들어, 다중 창 모드로 들어갔을 때는 액티비티가 보이는 영역이 줄어들기 때문에 화면 레이아웃을 변경해야 할 수도 있다. 

위와 같은 문제를 해결하기 위해 다음과 같은 메서드를 제공한다.

```java
public boolean isInMultiWindowMode()
public boolean isInPictureInPictureMode()
public void onMultiWindowModeChanged(boolean isInMultiWindowMode, Configuration newConfig)
```

`isInMultiWindowMode` 메서드를 호출하면 현재 다중 창 모드에 들어가 있는지 확인할 수 있다.

`isInPictureInPictureMode` 메서드를 호출하면 PIP (큰 화면에 작은 화면이 보이는 모드)에 들어가 있는지 확인할 수 있다.

하지만 위 메서드들은 호출할 때만 알려주기 때문에 다중 창 모드로 변경될 때 마다 즉시 알아야 할 수도 있다.

이때 사용하는 것이 `nMultiWindowModeChanged` 메서드이고 다중 창 모드로 변경될 때마다 자동으로 호출된다.

---
### 예제

```java
package org.techtown.multiwindow;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Build;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

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
            public void onClick(View view) {
                if (Build.VERSION.SDK_INT >= 24){
                    boolean isIn = isInMultiWindowMode();  // 다중 창 모드 여부 확인하기
                    println("다중 창 모드 여부: " + isIn);
                }
            }
        });

        Button button2 = findViewById(R.id.button2);
        button2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if(Build.VERSION.SDK_INT >= 24){
                    boolean isIn = isInPictureInPictureMode();
                    println("PIP 모드 여부: " + isIn);
                }
            }
        });
    }

    @Override
    public void onMultiWindowModeChanged(boolean isInMultiWindowMode){
        super.onMultiWindowModeChanged(isInMultiWindowMode);

        println("다중 창 모드 변경됨: " + isInMultiWindowMode);
    }

    public void println(String data){
        textView.append(data + "\n");
    }
}
```


---

## 결과

![1](https://user-images.githubusercontent.com/54762273/152697430-807a1889-be2d-4359-b61d-0065e0da20c5.PNG)


