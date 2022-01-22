---
title: "프래그먼트로 화면 만들기"
author: seyoung
date: '2022-01-09 19:41:00 +0900'
categories: Android Navigation
tags: [android,fragment ]
math: true
mermaid: true
---


## 프래그먼트로 화면 만들기

MainActivity에 버튼을 3개 추가 한 후 Fragment 파일을 추가 해준다.

**ListFragment.java**

```java

   public static interface ImageSelectionCallback{
       public void onImageSelected(int position);
   }

   public ImageSelectionCallback callback;

   @Override
    public void onAttach(Context context){
       super.onAttach(context);

       if(context instanceof ImageSelectionCallback){
           callback = (ImageSelectionCallback) context;
       }
   }

   @Nullable
   @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState){
       ViewGroup rootView = (ViewGroup) inflater.inflate(R.layout.fragment_list, container, false);
       Button button = rootView.findViewById(R.id.button);
       button.setOnClickListener((v)->{
           if(callback !=null)
               callback.onImageSelected(0);
       });

       Button button2 = rootView.findViewById(R.id.button2);
       button2.setOnClickListener((v)->{
           if(callback !=null)
               callback.onImageSelected(1);
       });

       Button button3 = rootView.findViewById(R.id.button3);
       button3.setOnClickListener((v)->{
           if(callback!=null)
               callback.onImageSelected(2);
       });
       return rootView;
   }
```

**ViewerFragment.java**

```java
public class ViewerFragment extends Fragment {
    ImageView imageView;

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState){
        ViewGroup rootView = (ViewGroup) inflater.inflate(R.layout.fragment_viewer,
                container, false);

        imageView = rootView.findViewById(R.id.imageView);
        return rootView;
    }

    public void setImage(int resId){
        imageView.setImageResource(resId);
    }
}
```

```java
public class MainActivity extends AppCompatActivity implements ListFragment.ImageSelectionCallback{

    ListFragment listFragment;
    ViewerFragment viewerFragment;

    int images[] = {R.drawable.dream01, R.drawable.dream02, R.drawable.dream03};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        FragmentManager manager= getSupportFragmentManager();
        listFragment = (ListFragment) manager.findFragmentById(R.id.listFragment);
        viewerFragment = (ViewerFragment) manager.findFragmentById(R.id.viewerFragment);
    }

    @Override
    public void onImageSelected(int position){
        viewerFragment.setImage(images[position]);
    }
}
```

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
  xmlns:app="http://schemas.android.com/apk/res-auto"  
  xmlns:tools="http://schemas.android.com/tools"  
  android:layout_width="match_parent"  
  android:layout_height="match_parent"  
  android:orientation="vertical"  
  tools:context=".MainActivity" >  
  
 <fragment  
  android:layout_width="match_parent"  
  android:layout_height="0dp"  
  android:layout_weight="1"  
  android:name="org.techtown.fragment.ListFragment"  
  android:id="@+id/listFragment"  
  />  
 <fragment  
  android:layout_width="match_parent"  
  android:layout_height="0dp"  
  android:layout_weight="1"  
  android:name="org.techtown.fragment.ViewerFragment"  
  android:id="@+id/viewerFragment"  
  />  
  
 </LinearLayout>
```


![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/148678524-5607f886-9e87-4f97-b290-3691da1510f9.gif)
