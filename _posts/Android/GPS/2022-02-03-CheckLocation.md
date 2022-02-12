---
title: "GPS로 내 위치 확인하기 "
author: seyoung
date: '2022-02-03 15:58:00 +0900'
categories: Android GPS
tags: [android,location,gps ]
math: true
mermaid: true
--- 
 
## GPS로 나의 위치 확인하기


앱에서 내 위치를 확인하고 싶다면 위치 관리자 (LocationManager)를 사용한다.

나의 현재 위치를 확인하는 가장 기본적인 방법은 위치 관리자에게 위치 정보를 요청하는 것으로 다음과 같은 단계를 거쳐야 한다.

 **1. 위치 관리자 객체 참조하기**
	
 - 위치 관리자는 시스템 서비스로 제공되므로, getSystemService 메서드를 이용해 <br>위치 관리자 객체를 참조한다.

**2. 위치 리스너 구현하기**

- 위치 관리자가 알려주는 현재 위치는 위치 리스너 (LocationListener)를 통해 받게 되므로 새로운 리스너를 구현하여 전달 받은 위치 정보를 처리한다.

**3.위치 정보 업데이트 요청하기**

- 위치 관리자에게 위치 정보가 변결될 때 마다 알려달라고 요청하기 위해 `requestLocationUpdates` 메서드를 호출한다. 이 메서드의 매개 변수로 2단계에서<br> 구현한 위치 리스너 객체를 전달한다.

**4. 매니페스트에 권한 추가하기**

- GPS를 사용할 수 있도록 매니페스트 파일에 ACCESS_FINE_LOCATION 권한ㅇ를 추가하고 위험 권한을 위한 설정과 코드를 추가한다.


![Untitled Diagram drawio](https://user-images.githubusercontent.com/54762273/152289022-6c163b32-dcdf-435c-a024-59809daeeb59.png)

	       
---

### 예제 

```java
package org.techtown.location;  
  
import androidx.appcompat.app.AppCompatActivity;  
import android.content.Context;  
import android.location.Location;  
import android.location.LocationListener;  
import android.location.LocationManager;  
import android.os.Bundle;  
import android.view.View;  
import android.widget.Button;  
import android.widget.TextView;  
import android.widget.Toast;  
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
		    public void onClick(View view) {  
                startLocationService();  
            }  
        });  
  
        AndPermission.with(this)  
                .runtime()  
                .permission(  
                        Permission.ACCESS_FINE_LOCATION,  
                        Permission.ACCESS_COARSE_LOCATION)  
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
  
    public void startLocationService(){  
        // LocationManager 객체 참조하기  
	    LocationManager manager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);  
  
        try{  
            // 이전에 확인했던 위치 가져오기  
			Location location = manager.getLastKnownLocation(LocationManager.GPS_PROVIDER);  
            if(location != null){  
                double latitude = location.getLatitude();  
                double longitude = location.getLongitude();  
                String message = "최근 위치 -> Latitude : " + latitude + "\nLongitude" + longitude;  
  
                textView.setText(message);  
            }  
  
            GPSListener gpsListener = new GPSListener();  
            long minTime = 10000;  
            float minDistance = 0;  
  
            // 위치 요청하기  
		    manager.requestLocationUpdates(LocationManager.GPS_PROVIDER, minTime, minDistance,gpsListener);  
            Toast.makeText(getApplicationContext(), "내 위치확인 요청함", Toast.LENGTH_SHORT).show();  
        }catch(SecurityException e){  
            e.printStackTrace();  
        }  
     }  
  
     class GPSListener implements LocationListener{  
	     // 위치가 확인되었을 때 자동으로 호출되는 onLocatioChanged 메서드
        @Override  
	    public void onLocationChanged(Location location){  
            Double latitude = location.getLatitude();  
            Double longitude = location.getLongitude();  
            String message = "내 위치 -> Latitude : " + latitude + "\nLongitude:" + longitude;  
            textView.setText(message);  
        }  
  
         @Override  
	    public void onProviderDisabled(String provider){}  
  
         @Override  
	    public void onProviderEnabled(String provider){}  
  
         @Override  
	    public void onStatusChanged(String provider, int status, Bundle extras){}  
  
     }  
}
```

최근 위치 정보를 확인하기 위해 사용되는 `getLastKnownLocation` 메서드에는 위치 정보를 제공하는 위치 제공자 정보를 매개 변수로 전달한다.

안드로이드 위치 제공자는 크게 GPS_PROVIDER와 NERWORK_PROVIDER로 구분하고 있으며, 이 두 개의 값 중 하나를 매개 변수로 전달하면 Location 객체가 반환된다.

**Location** 객체는 위도와 경도 값을 가지고 있으며,	 `getLatitude`와 `getLongitude` 메서드로 그 값을 확인할 수 있다.

**위치 리스너**는 위치 관리자에서 전달하는 정보를 받기 위해 정의된 인터페이스이다.

위치 관리자가 위치 정보를 전달할 때 호출되므로 위치 정보를 받아 처리하려면 위치 리스너의 `onLocationChanged` 메서드를 구현해야 한다.

위치 관리자는 일정한 시간 간격으로 위치 정보를 확인하거나 일정 거리 이상을 이동 했을 때 위치 정보를 전달하는 기능을 제공한다.

위치 관리자에게 현재 위치를 요청하기 위해서는 `requestLocationUpdated` 메서드를 호출해야하는데 매개 변수로는 최소 시간, 최소 거리, 위치 리스너객체가 전달 되어야 한다.

`getLastKnwonLocation` 메서드는 권한이 필요하기 때문에 Manifest파일에 권한을 추가한다.

**AndroidManifest.xml**

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>  
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

그리고 외부 권한을 추가해주기 위해 의존성을 추가해준다.

```xml
dependencies {  
	...
  implementation 'com.yanzhenjie:permission:2.0.3'  
}
```

---
### 결과

<img width="405" alt="스크린샷 2022-02-03 오후 3 52 00" src="https://user-images.githubusercontent.com/54762273/152294970-2e01d447-7f05-4b06-b2ee-f60b80a2a141.png">


---

### 애뮬레이터로 가상 위치 정보 전송하기

에뮬레이터에서는 GPS 모듈을 사용할 수 없다.

따라서 위치 정보를 가상으로 전달하는 방법을 사용해야 한다.

에뮬레이터 오른쪽에 ... 을 클릭한 후  location 탭에서 위치를 전송하면 된다.