---
title: "지도에 아이콘 추가하기"
author: seyoung
date: '2022-02-04 13:55:00 +0900'
categories: Android GPS
tags: [android,location,gps ]
math: true
mermaid: true
--- 
 

## 오버레이란?

일반적으로 지구상의 현실 공간을 지도로 표현할 때는 **레이어(Layer)**로 분리하고 각각의 레이어에는 유사한 속성을 가진 객체들을 넣어두게 된다.
**구글맵에서는 이런 레이어들을 오버레이(Overlay)라고 부른다.**

![1](https://user-images.githubusercontent.com/54762273/152472032-3c2f8ef0-0ab1-4a5b-8527-6414d60b7f24.png)

(출처 : Google Map Layer - [https://seefurther.withgoogle.com/)](https://seefurther.withgoogle.com/)

---

### 내 현재 위치 표시를 위한 오버레이 추가하기

```java
@Override  
protected void onCreate(Bundle savedInstanceState) {  
    super.onCreate(savedInstanceState);  
    setContentView(R.layout.activity_main);  
  
    mapFragment = (SupportMapFragment) getSupportFragmentManager().findFragmentById(R.id.map);  
    mapFragment.getMapAsync(new OnMapReadyCallback() {  
        @Override  
		public void onMapReady(GoogleMap googleMap) {  
            Log.d("Map", "지도 준비됨.");  
            map = googleMap;  
  
        }  
    });  
  
    mapFragment.getMapAsync(new OnMapReadyCallback() {  
        @Override  
		 public void onMapReady(GoogleMap googleMap) {  
            Log.d("Map", "GoogleMap is ready");  
            map = googleMap;  
            map.setMyLocationEnabled(true);  
        }  
    });

		... 중략
	}
... 중략
	public void onResume(){  
	    super.onResume();  
  
	    if(map != null)  
	        map.setMyLocationEnabled(true); // 액티비티가 화면에 보일 때 내 위치 표시 활성화  
	}  
  
	public void onPause(){  
	    super.onPause();  
  
	    if(map != null)  
	        map.setMyLocationEnabled(false); // 액티비티가 중지될 때 내 위치 표시 비활성화  
	}

```

액티비티가 중지되거나 다시 시작할 때 현재 위치 표시하기 위해 `setMyLocationEnabeld(true)` 메서드를 호출한다.

이 메서드는 지도 초기화가 완료된 후에 호출되는 `onMapReady` 메서드 안에서 호출한다.
그리고 액티비티가 화면에 보이는 시점에서 다시 표시하고 화면이 사라지기 전에 없애고 싶다면 onResume과 onPause 메서드 안에서 각각 `setMyLocationEnabled(ture)`와 `setMyLocationEnabled(false)`를 호출한다.

---

### 마커를 사용해 내 위치나 카페 위치 표시하기

```java

... 중략

MarkerOptions myLocationMarker;

... 중략

private void showCurrentLocation(Location location){

... 중략

	showMyLocationMarker(curPoint);

}

private void showMyLocationMarker(LatLng curPoint) {  
    if (myLocationMarker == null) {  
        myLocationMarker = new MarkerOptions();  
        myLocationMarker.position(curPoint);  
        myLocationMarker.title("● 내 위치\n");  
        myLocationMarker.snippet("● GPS로 확인한 위치");  
        myLocationMarker.icon(BitmapDescriptorFactory.fromResource(R.drawable.mylocation));  
        map.addMarker(myLocationMarker);  
    } else {  
        myLocationMarker.position(curPoint);  
    }  
}

 ... 중략
```

**참고로 /app/res/drawable 폴더에 mylocation 이미지 파일이 있어야 한다.**

---

**결과**

<img width="403" alt="스크린샷 2022-02-04 오후 1 50 56" src="https://user-images.githubusercontent.com/54762273/152473784-45173827-a2df-453d-9b83-77580915b3b8.png">

---

### 전체 소스코드

```java
package org.techtown.location;  
  
import android.content.Context;  
import android.location.Location;  
import android.location.LocationListener;  
import android.location.LocationManager;  
import android.os.Bundle;  
import android.util.Log;  
import android.view.View;  
import android.widget.Button;  
import android.widget.Toast;  
  
import androidx.appcompat.app.AppCompatActivity;  
  
import com.google.android.gms.maps.CameraUpdateFactory;  
import com.google.android.gms.maps.GoogleMap;  
import com.google.android.gms.maps.MapsInitializer;  
import com.google.android.gms.maps.OnMapReadyCallback;  
import com.google.android.gms.maps.SupportMapFragment;  
import com.google.android.gms.maps.model.BitmapDescriptorFactory;  
import com.google.android.gms.maps.model.LatLng;  
import com.google.android.gms.maps.model.MarkerOptions;  
import com.yanzhenjie.permission.Action;  
import com.yanzhenjie.permission.AndPermission;  
import com.yanzhenjie.permission.runtime.Permission;  
  
import java.util.List;  
  
public class MainActivity extends AppCompatActivity {  
  
    SupportMapFragment mapFragment;  
    GoogleMap map;  
  
    MarkerOptions myLocationMarker;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        mapFragment = (SupportMapFragment) getSupportFragmentManager().findFragmentById(R.id.map);  
        mapFragment.getMapAsync(new OnMapReadyCallback() {  
            @Override  
		    public void onMapReady(GoogleMap googleMap) {  
                Log.d("Map", "지도 준비됨.");  
                map = googleMap;  
  
            }  
        });  
  
        mapFragment.getMapAsync(new OnMapReadyCallback() {  
            @Override  
		    public void onMapReady(GoogleMap googleMap) {  
                Log.d("Map", "GoogleMap is ready");  
                map = googleMap;  
                map.setMyLocationEnabled(true);  
            }  
        });  
  
        try {  
            MapsInitializer.initialize(this);  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
  
        Button button = findViewById(R.id.button);  
        button.setOnClickListener(new View.OnClickListener() {  
            @Override  
  public void onClick(View v) {  
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
  
    public void showToast(String message) {  
        Toast.makeText(this, message, Toast.LENGTH_LONG).show();  
    }  
  
    public void onResume(){  
        super.onResume();  
  
        if(map != null)  
            map.setMyLocationEnabled(true); // 액티비티가 화면에 보일 때 내 위치 표시 활성화  
  }  
  
    public void onPause(){  
        super.onPause();  
  
        if(map != null)  
            map.setMyLocationEnabled(false); // 액티비티가 중지될 때 내 위치 표시 비활성화  
  }  
  
  
    public void startLocationService() {  
        LocationManager manager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);  
  
        try {  
            Location location = manager.getLastKnownLocation(LocationManager.GPS_PROVIDER);  
            if (location != null) {  
                double latitude = location.getLatitude();  
                double longitude = location.getLongitude();  
                String message = "최근 위치 -> Latitude : " + latitude + "\nLongitude:" + longitude;  
  
                Log.d("Map", message);  
            }  
  
            GPSListener gpsListener = new GPSListener();  
            long minTime = 10000;  
            float minDistance = 0;  
  
            manager.requestLocationUpdates(  
                    LocationManager.GPS_PROVIDER,  
                    minTime, minDistance, gpsListener);  
  
            Toast.makeText(getApplicationContext(), "내 위치확인 요청함",  
                    Toast.LENGTH_SHORT).show();  
  
        } catch(SecurityException e) {  
            e.printStackTrace();  
        }  
    }  
  
    class GPSListener implements LocationListener {  
        public void onLocationChanged(Location location) {  
            Double latitude = location.getLatitude();  
            Double longitude = location.getLongitude();  
  
            String message = "내 위치 -> Latitude : "+ latitude + "\nLongitude:"+ longitude;  
            Log.d("Map", message);  
  
            showCurrentLocation(latitude, longitude);  
        }  
  
        public void onProviderDisabled(String provider) { }  
  
        public void onProviderEnabled(String provider) { }  
  
        public void onStatusChanged(String provider, int status, Bundle extras) { }  
    }  
  
    private void showCurrentLocation(Double latitude, Double longitude) {  
        LatLng curPoint = new LatLng(latitude, longitude);  
        map.animateCamera(CameraUpdateFactory.newLatLngZoom(curPoint, 15));  
  
        showMyLocationMarker(curPoint);  
    }  
  
    private void showMyLocationMarker(LatLng curPoint) {  
        if (myLocationMarker == null) {  
            myLocationMarker = new MarkerOptions();  
            myLocationMarker.position(curPoint);  
            myLocationMarker.title("● 내 위치\n");  
            myLocationMarker.snippet("● GPS로 확인한 위치");  
            myLocationMarker.icon(BitmapDescriptorFactory.fromResource(R.drawable.mylocation));  
            map.addMarker(myLocationMarker);  
        } else {  
            myLocationMarker.position(curPoint);  
        }  
    }  
  
}

```