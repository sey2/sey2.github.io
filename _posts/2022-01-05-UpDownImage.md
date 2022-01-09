---
title: "두개의 이미지 뷰에 이미지 번갈아 보여주기"
date: '2022-01-05 09:44:00 +0900'
categories: Android Layout_And_View
tags: [anroid,view,scroll,layout]
mermaid: true
---


## 두개의 이미지 뷰에 이미지 번갈아 보여주기 

```xml 
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  >  
  
 <HorizontalScrollView  
  android:id="@+id/scrollView01"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:layout_above="@+id/buttonLayout"  
  android:layout_alignParentTop="true"  
  >  
 <ScrollView  
  android:layout_width="match_parent"  
  android:layout_height="match_parent">  
  
 <ImageView  
  android:id="@+id/imageView01"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:scaleType="matrix"  
  />  
  
 </ScrollView>  
 </HorizontalScrollView>  
  
 <LinearLayout  
  android:id="@+id/buttonLayout"  
  android:orientation="vertical"  
  android:layout_width="match_parent"  
  android:layout_height="wrap_content"  
  android:layout_centerInParent="true"  
  >  
 <LinearLayout  
  android:orientation="horizontal"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_gravity="center_horizontal"  
  >  
 <Button  
  android:id="@+id/button01"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_margin="10dp"  
  android:text="    ▲    "  
  android:textSize="18sp"  
  />  
 <Button  
  android:id="@+id/button02"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:layout_margin="10dp"  
  android:text="    ▼    "  
  android:textSize="18sp"  
  />  
 </LinearLayout>  
 </LinearLayout>  
 <HorizontalScrollView  
  android:id="@+id/scrollView02"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:layout_below="@+id/buttonLayout"  
  android:layout_alignParentBottom="true"  
  >  
 <ScrollView  
  android:layout_width="match_parent"  
  android:layout_height="match_parent">  
  
 <ImageView  
  android:id="@+id/imageView02"  
  android:layout_width="wrap_content"  
  android:layout_height="wrap_content"  
  android:scaleType="matrix"  
  />  
  
 </ScrollView>  
 </HorizontalScrollView>  
  
</RelativeLayout>

```


```java
package org.techtown.myapplication;  
  
import android.os.Bundle;  
import android.view.View;  
import android.widget.Button;  
import android.widget.ImageView;  
  
import androidx.appcompat.app.AppCompatActivity;  
  
import org.techtown.myapplication.R;  
  
public class MainActivity extends AppCompatActivity {  
  ImageView imageView01;  
  ImageView imageView02;  
  
  @Override  
  protected void onCreate(Bundle savedInstanceState) {  
  super.onCreate(savedInstanceState);  
  setContentView(R.layout.activity_main);  
  
  imageView01 = findViewById(R.id.imageView01);  
  imageView02 = findViewById(R.id.imageView02);  
  
  Button button01 = findViewById(R.id.button01);  
  button01.setOnClickListener(new View.OnClickListener() {  
  public void onClick(View v) {  
  moveImageUp();  
 } });  
  Button button02 = findViewById(R.id.button02);  
  button02.setOnClickListener(new View.OnClickListener() {  
  public void onClick(View v) {  
  moveImageDown();  
 } });  
  moveImageUp();  
 }  
  private void moveImageDown() {  
  imageView01.setImageResource(0);  
  imageView02.setImageResource(R.drawable.one);  
  
  imageView01.invalidate();  
  imageView02.invalidate();  
 }  
  private void moveImageUp() {  
  imageView01.setImageResource(R.drawable.one);  
  imageView02.setImageResource(0);  
  
  imageView01.invalidate();  
  imageView02.invalidate();  
 }  
}

```

> **invalidate()는 draw를 다시 하도록 요청하고 requestLayout()은 measure를 통한 사이즈 체크부터 다시 시작한다.**

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148219043-152b4c82-4c20-4076-b848-c0a3fe1f308a.gif)

  

