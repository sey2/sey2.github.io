---
title: "상단 탭 하단 탭 만들기"
author: seyoung
date: '2022-01-09 22:51:00 +0900'
categories: Android Layout_And_View
tags: [android,view]
math: true
mermaid: true
---


## 상단 탭 하단 탭 만들기

### 상단 탭 만들기 

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.coordinatorlayout.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <com.google.android.material.appbar.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="?colorPrimaryDark"
                android:elevation="1dp"
                android:theme="@style/ThemeOverlay.AppCompat.Dark">

                <TextView
                    android:id="@+id/titleText"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="타이틀"
                    android:textAppearance="@style/Base.TextAppearance.Widget.AppCompat.Toolbar.Title" />

            </androidx.appcompat.widget.Toolbar>

            <com.google.android.material.tabs.TabLayout
                android:id="@+id/tabs"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@android:color/background_light"
                android:elevation="1dp"
                app:tabGravity="fill"
                app:tabMode="fixed"
                app:tabSelectedTextColor="?colorAccent"
                app:tabTextColor="?colorPrimary" />
        </com.google.android.material.appbar.AppBarLayout>

        <FrameLayout
            android:id="@+id/container"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior">

        </FrameLayout>

    </androidx.coordinatorlayout.widget.CoordinatorLayout>

</RelativeLayout>
```

위의 XML 구조를 정리하면 다음과 같다.

```
<CoordinatorLayout>
 <AppBarLayout>
   <Toolbar>
   </Toolbar>
   <TabLayout>
   </TabLayout>
   </AppBarLayout>
   <FrameLayout>
   </FrameLayout>
   </CoordinatorLayout>
```

CoordinatorLayout은 액션 바 영역을 포함한 전체 화면의 위치를 잡아주는 역할을 한다.

CoordinatorLayout 안에 AppBarLayout과 함께 다른 레이아웃을 넣으면 그 둘 간의 간격이나

위치가 자동으로 결정된다. 

AppBarLayout은 액션바를 가르킨다.

인제 res/layout 폴더 안에 fragment 파일들을 추가하자.

Fragmnet1.java와 fragment1.xml 파일을 인플레이션 해주자.


**Fragment1.java**
```java
public class Fragment1 extends Fragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState){
        return inflater.inflate(R.layout.fragment1,container,false);
    }
}
```


**Fragment1.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:background="#00BCD4">  
  
 <Button  
  android:id="@+id/button"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_weight="1"  
  android:text="첫 번째" />  
</LinearLayout>
```

위와 같이 Fragment2,3 파일도 만들어주자.

그리고 MainActivity.java 파일을 아래와 같이 수정

```java
public class MainActivity extends AppCompatActivity {

    Toolbar toolbar;

    Fragment1 fragment1;
    Fragment2 fragment2;
    Fragment3 fragment3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        toolbar = findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);

        ActionBar actionBar = getSupportActionBar();
        actionBar.setDisplayShowTitleEnabled(false);

        fragment1 = new Fragment1();
        fragment2 = new Fragment2();
        fragment3 = new Fragment3();

        getSupportFragmentManager().beginTransaction().replace(R.id.container,fragment1).commit();
    }
}
```

위의 자바 파일에서 setSupportActionBar 메서드는 액티비티에 디폴트로 만들어진 액션바가 없을

경우에만 동작한다.

우리가 테마를 액션바가 들어있는 테마로 설정 했기 때문에

 프로젝트가 만들어질 때 메인 액티비티에서 자동으로 액션바가 만들어진다 .

액티비티에 설정된 테마를 변경하기 위해 /app/res/values 폴더 안에 있는 themes.xml 파일을 연다. 

그리고 아래와 같이 수정한다.

```xml
   <style name="Theme.SampleTab" parent="Theme.MaterialComponents.DayNight.NoActionBar">

```

이렇게 하면 이 스타일을 적용한 액티비티에는 액션바가 만들어지지 않는다.

액션바를 따로 만들고 싶으면 직접 setSupportActionBar 메서드를 호출하여야 한다.

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148683213-5fb203ba-2cda-45ef-9827-2423f3f08cca.gif)


---

### 하단 탭 만들기

하단 탭은 BottomNavigationView 위젯으로 만들 수 있다.

**/app/res 폴더에 menu 폴더를 만든 후 menu_bottom.xml 파일을 만든다.** <br><br>

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@+id/container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_navigation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginEnd="0dp"
        android:layout_marginStart="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:itemBackground="?colorPrimary"
        app:itemIconTint="@drawable/item_color"
        app:itemTextColor="@drawable/item_color"
        app:menu="@menu/menu_bottom" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
<br><br>


**Activity_main.java**
```java
public class MainActivity extends AppCompatActivity {

    Fragment1 fragment1;
    Fragment2 fragment2;
    Fragment3 fragment3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        fragment1 = new Fragment1();
        fragment2 = new Fragment2();
        fragment3 = new Fragment3();

        getSupportFragmentManager().beginTransaction().replace(R.id.container, fragment1).commit();

        BottomNavigationView bottomNavigation = findViewById(R.id.bottom_navigation);
        bottomNavigation.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                switch (item.getItemId()) {
                    case R.id.tab1:
                        Toast.makeText(getApplicationContext(), "첫 번째 탭 선택됨", Toast.LENGTH_LONG).show();
                        getSupportFragmentManager().beginTransaction()
                                .replace(R.id.container, fragment1).commit();

                        return true;
                    case R.id.tab2:
                        Toast.makeText(getApplicationContext(), "두 번째 탭 선택됨", Toast.LENGTH_LONG).show();
                        getSupportFragmentManager().beginTransaction()
                                .replace(R.id.container, fragment2).commit();

                        return true;
                    case R.id.tab3:
                        Toast.makeText(getApplicationContext(), "세 번째 탭 선택됨", Toast.LENGTH_LONG).show();
                        getSupportFragmentManager().beginTransaction()
                                .replace(R.id.container, fragment3).commit();

                        return true;
                }

                return false;
            }
        });
    }
}
```

<br><br>

**menu_bottom.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto" >

    <item
        android:id = "@+id/tab1"
        app:showAsAction="ifRoom"
        android:enabled="true"
        android:icon="@android:drawable/ic_dialog_email"
        android:title="이메일"/>

    <item
        android:id="@+id/tab2"
        app:showAsAction="ifRoom"
        android:icon="@android:drawable/ic_dialog_info"
        android:title="정보"/>

    <item
        android:id="@+id/tab3"
        app:showAsAction="ifRoom"
        android:enabled="true"
        android:icon="@android:drawable/ic_dialog_map"
        android:title="위치"/>

</menu>
```

---


**결과**


![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148684900-6087b938-16ca-4f33-ac71-3bab06405ef1.gif)
