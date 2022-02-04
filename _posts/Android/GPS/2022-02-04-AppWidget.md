---
title: "앱 위젯 만들기"
author: seyoung
date: '2022-02-04 15:07:00 +0900'
categories: Android GPS
tags: [android,location,gps,appwidget ]
math: true
mermaid: true
--- 

## 앱 위젯 만들기

**앱 위젯(App Widget)** 은 안드로이드 단말이 홈 화면에서 위젯을 바로 보여주고 싶을 때 사용할 수 있다. 일반적으로 위젯은 홈 화면을 길게 누르면 추가할 수 있는 화면이 표시된다.
또는 홈 화면 하단 중앙에 있는 앱 리스트 아이콘을 눌렀을 때 추가하는 화면이 표시되는 단말도 있다.

앱 위젯은 다음과 같이 두가지로 구성 된다.

|  |  |
|:--:|:--:|
|앱 위젯 호스트  |위젯이 담고 있는 그릇  |
|앱 위젯 제공자 | 위젯을 보여주는 제공자|

앱 위젯 제공자가 앱 위젯 호스트 안에서 위젯을 보여준다.

이런 앱 위젯을 구성할 때 필요한 요소는 아래와 같이  세 가지 있다.

|  |  |
|:--:|:--:|
|위젯의 초기 뷰 레이아웃  | 앱 위젯이 처음에 화면에 나타날 때 필요한 레이아웃을 정의함<br>XML로 정의한다.  |
|앱 위젯 제공자 정보| 앱 위젯을 위한 메타데이터(레이아웃, 업데이트 주기 등)를 가지고 있다. 앱 위젯 제공자 클래스에 대한 정보를 가지고 있다.<br>XML로 정의한다.|
|앱 위젯 제공자| 앱 위젯과 정보를 주고 받기 위한 기본 클래스이다. <br> 브로드캐스트 수신자로 만들며 앱 위젯의 상태 변화에 따른 기능을 구현한다.|

앱 위젯을 만든다는 것은 앱 위젯 제공자를 만드는 것이라고 할 수 있으며, 구성에 필요한 세가지 요소를 순서대로 만들면 앱 위젯을 구성할 수 있다.

![Untitled Diagram drawio](https://user-images.githubusercontent.com/54762273/152475876-9add4ac8-0d7c-4fd7-adc1-2da6172d76ca.png)


앱 위젯으로 만든 뷰는 주기적으로 업데이트될 수 있는데, 그럴 때마다 앱 위젯 제공자의 `onUpdate` 메서드가 호출된다. 

만약 앱 위젯 제공자가 위젯을 바꾸고 싶을 경우에는 앱 위젯 매니저를 통해 업데이트 할 수 있다.

---

### 예제

**앱 위젯 화면으로 보여줄 텍스트 뷰 정의**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent">  
  
   <LinearLayout  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:background="@drawable/background"  
  android:padding="10dp">  
  
      <TextView  
  android:id="@+id/txtInfo"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:gravity="center_horizontal|center_vertical"  
  android:text="내 위치 정보 수신중 ..."  
  android:textColor="#FFFFFFFF"  
  android:lineSpacingExtra="4dp" />  
  
   </LinearLayout>  
  
</LinearLayout>
```

앱 위젯에 들어갈 수 있는 뷰는 아래와 같다.

|유형  | 뷰 이름 |
|:--:|:--:|
|뷰 그룹  |FrameLayout, LinearLayout, RelativeLayout  |
|뷰| AnalogClock, Button, Chronometer ImageButton, ImageView, ProgressBar, TextView|

앱 위젯으로 표현되는 뷰들이 다른 프로세스에 들어가고 있기 때문에 다른 프로세스의 뷰를 접근하기 위해 RemoteViews 객체가 사용된다.

인제 앱 위젯 제공자 정보를 넣어주자.

**/app/res/xml/mylocationinfo.xml**

```xml

<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"  
  android:minWidth="294dp"  
  android:minHeight="72dp"  
  android:updatePeriodMillis="1800000"  
  android:initialLayout="@layout/mylocation">  
</appwidget-provider>
```

<br>

앱 위젯이 주기적으로 업데이트될 때 처리할 코드를 구현하자. 


```java
package org.techtown.location.widget;  
  
import android.app.PendingIntent;  
import android.app.Service;  
import android.appwidget.AppWidgetManager;  
import android.appwidget.AppWidgetProvider;  
import android.content.ComponentName;  
import android.content.Context;  
import android.content.Intent;  
import android.location.Address;  
import android.location.Criteria;  
import android.location.Geocoder;  
import android.location.Location;  
import android.location.LocationListener;  
import android.location.LocationManager;  
import android.net.Uri;  
import android.os.Bundle;  
import android.os.IBinder;  
  
import android.util.Log;  
import android.widget.RemoteViews;  
  
import java.util.List;  
  
public class MyLocationProvider extends AppWidgetProvider {  
  
   public static double ycoord = 0.0D;  
   public static double xcoord = 0.0D;  
  
  
   @Override  
  public void onDeleted(Context context, int[] appWidgetIds) {  
      super.onDeleted(context, appWidgetIds);  
   }  
  
   @Override  
  public void onDisabled(Context context) {  
      super.onDisabled(context);  
   }  
  
   @Override  
  public void onEnabled(Context context) {  
      super.onEnabled(context);  
   }  
  
   @Override  
  public void onReceive(Context context, Intent intent) {  
      super.onReceive(context, intent);  
   }  
  
   @Override  
  public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {  
      super.onUpdate(context, appWidgetManager, appWidgetIds);  
  
      Log.d("MyLocationProvider", "onUpdate() called : " + ycoord + ", " + xcoord);  
  
      final int size = appWidgetIds.length;  
  
        for (int i = 0; i < size; i++) {  
            int appWidgetId = appWidgetIds[i];  
  
            //String uri = "geo:"+ ycoord + "," + xcoord + "?z=10";  
 //Intent intent = new Intent(android.content.Intent.ACTION_VIEW, Uri.parse(uri));  
  
		    String uriBegin = "geo:" + ycoord + "," + xcoord;  
            String query = ycoord + "," + xcoord + "(" + "내위치" + ")";  
            String encodedQuery = Uri.encode(query);  
            String uriString = uriBegin + "?q=" + encodedQuery + "&z=15";  
            Uri uri = Uri.parse(uriString);  
              
            Intent intent = new Intent(Intent.ACTION_VIEW, uri);  
              
              
            PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);  
  
            RemoteViews views = new RemoteViews(context.getPackageName(), R.layout.mylocation);  
            views.setOnClickPendingIntent(R.id.txtInfo, pendingIntent);  
  
            appWidgetManager.updateAppWidget(appWidgetId, views);  
        }  
  
        // gps 위치 확인을 위한 서비스 시작  
       context.startService(new Intent(context,GPSLocationService.class));  
   }  
  
  
   public static class GPSLocationService extends Service {  
      public static final String TAG = "GPSLocationService";  
  
      private LocationManager manager = null;  
  
      private LocationListener listener = new LocationListener() {  
  
         public void onStatusChanged(String provider, int status, Bundle extras) {  
         }  
  
         public void onProviderEnabled(String provider) {  
         }  
  
         public void onProviderDisabled(String provider) {  
         }  
  
         public void onLocationChanged(Location location) {  
            Log.d(TAG, "onLocationChanged() called.");  
  
            // 위치 정보가 확인되면 updateCoordinates 메서드 호출  
  updateCoordinates(location.getLatitude(), location.getLongitude());  
  
            stopSelf();  
         }  
      };  
  
      public IBinder onBind(Intent intent) {  
         return null;  
      }  
  
      public void onCreate() {  
         super.onCreate();  
  
         Log.d(TAG, "onCreate() called.");  
  
         manager = (LocationManager) getSystemService(LOCATION_SERVICE);  
  
      }  
  
      public int onStartCommand(Intent intent, int flags, int startId) {  
         startListening();  
  
         return super.onStartCommand(intent, flags, startId);  
      }  
  
      public void onDestroy() {  
         stopListening();  
  
         Log.d(TAG, "onDestroy() called.");  
  
         super.onDestroy();  
      }  
  
      private void startListening() {  
         Log.d(TAG, "startListening() called.");  
  
         final Criteria criteria = new Criteria();  
         criteria.setAccuracy(Criteria.ACCURACY_COARSE);  
         criteria.setAltitudeRequired(false);  
         criteria.setBearingRequired(false);  
         criteria.setCostAllowed(true);  
         criteria.setPowerRequirement(Criteria.POWER_LOW);  
  
         final String bestProvider = manager.getBestProvider(criteria, true);  
  
         try {  
            if (bestProvider != null && bestProvider.length() > 0) {  
               manager.requestLocationUpdates(bestProvider, 500, 10, listener);  
            } else {  
               final List<String> providers = manager.getProviders(true);  
  
               for (final String provider : providers) {  
                  manager.requestLocationUpdates(provider, 500, 10, listener);  
               }  
            }  
         } catch(SecurityException e) {  
            e.printStackTrace();  
         }  
      }  
  
      private void stopListening() {  
         try {  
            if (manager != null && listener != null) {  
               manager.removeUpdates(listener);  
            }  
  
            manager = null;  
         } catch (final Exception ex) {  
  
         }  
      }  
  
      private void updateCoordinates(double latitude, double longitude) {  
         Geocoder coder = new Geocoder(this);  
         List<Address> addresses = null;  
         String info = "";  
  
         Log.d(TAG, "updateCoordinates() called.");  
  
         try {  
            addresses = coder.getFromLocation(latitude, longitude, 2);  
  
            if (null != addresses && addresses.size() > 0) {  
               int addressCount = addresses.get(0).getMaxAddressLineIndex();  
  
               if (-1 != addressCount) {  
                  for (int index = 0; index <= addressCount; ++index) {  
                     info += addresses.get(0).getAddressLine(index);  
  
                     if (index < addressCount)  
                        info += ", ";  
                  }  
               } else {  
                  info += addresses.get(0).getFeatureName() + ", "  
  + addresses.get(0).getSubAdminArea() + ", "  
  + addresses.get(0).getAdminArea();  
               }  
            }  
  
            Log.d(TAG, "Address : " + addresses.get(0).toString());  
         } catch (Exception e) {  
            e.printStackTrace();  
         }  
  
         coder = null;  
         addresses = null;  
  
         if (info.length() <= 0) {  
            info = "[내 위치] " + latitude + ", " + longitude  
  + "\n터치하면 지도로 볼 수 있습니다.";  
         } else {  
            info += ("\n" + "[내 위치] " + latitude + ", " + longitude + ")");  
            info += "\n터치하면 지도로 볼 수 있습니다.";  
         }  
  
         RemoteViews views = new RemoteViews(getPackageName(), R.layout.mylocation);  
  
         views.setTextViewText(R.id.txtInfo, info);  
  
         ComponentName thisWidget = new ComponentName(this, MyLocationProvider.class);  
         AppWidgetManager manager = AppWidgetManager.getInstance(this);  
         manager.updateAppWidget(thisWidget, views);  
  
         xcoord = longitude;  
         ycoord = latitude;  
         Log.d(TAG, "coordinates : " + latitude + ", " + longitude);  
  
      }  
   }  
  
}
```

텍스트 뷰를 눌렀을 때 내 위치를 이용해 지도를 보여줄 수 있는 가장 간단한 방법은 "geo:"로 시작하는 URI 객체를 만들어 인텐트로 지도를 띄워주는 것이다.

내 위치를 띄우는데 사용되는 URI 문자열 포맷은 다음과 같다.

**geo:\<latitude>, \<longitude>?z=\<zoomLevel>**

위도와 경도 뒤에 오는 "z" 파라미터 값은 지도가 나타날 때 사용되는 확대/축소 수준을 지정하는 것이다.

<br>

**IntroActivity.java**

```java

package org.techtown.location.widget;  
  
import android.os.Bundle;  
import android.util.Log;  
  
import androidx.appcompat.app.AppCompatActivity;  
  
import com.yanzhenjie.permission.Action;  
import com.yanzhenjie.permission.AndPermission;  
import com.yanzhenjie.permission.runtime.Permission;  
  
import java.util.List;  
  
public class IntroActivity extends AppCompatActivity {  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.intro);  
  
        AndPermission.with(this)  
                .runtime()  
                .permission(  
                        Permission.ACCESS_FINE_LOCATION,  
                        Permission.ACCESS_COARSE_LOCATION)  
                .onGranted(new Action<List<String>>() {  
                    @Override  
				   public void onAction(List<String> permissions) {  
                        Log.d("Intro", "허용된 권한 갯수 : " + permissions.size());  
                    }  
                })  
                .onDenied(new Action<List<String>>() {  
                    @Override  
				    public void onAction(List<String> permissions) {  
                        Log.d("Intro", "거부된 권한 갯수 : " + permissions.size());  
                    }  
                })  
                .start();  
  
    }  
  
}
```

<br>

마지막으로 매니페스트 파일에 앱 위젯과 관련된 태그들을 정의해주자.

**AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"  
  package="org.techtown.location.widget">  
  
    <uses-permission android:name="android.permission.INTERNET"/>  
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>  
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>  
  
    <application  
  android:allowBackup="true"  
  android:icon="@mipmap/ic_launcher"  
  android:label="@string/app_name"  
  android:roundIcon="@mipmap/ic_launcher_round"  
  android:supportsRtl="true"  
  android:theme="@style/Theme.SampleMyLocationWidget">  
  
        <receiver android:name=".MyLocationProvider">  
            <intent-filter>  
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />  
            </intent-filter>  
            <meta-data android:name="android.appwidget.provider"  
					   android:resource="@xml/mylocationinfo" />  
        </receiver>  
  
        <service android:name=".MyLocationProvider$GPSLocationService"></service>  
  
    </application>  
  
</manifest>

```