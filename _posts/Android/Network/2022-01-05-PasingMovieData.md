---
title: "일별 오피스 데이터 파싱"
author: seyoung
date: '2022-01-25 01:25:00 +0900'
categories: Android Network
tags: [android,network,volley]
math: true
mermaid: true
---

## 영화 정보 가져와 보여주기

<a href = "https://www.kobis.or.kr/kobisopenapi/homepg/apiservice/searchServiceInfo.do">오픈 API  </a>

영화 포스터까지 받아 오고 싶으면 <a href = "https://www.culture.go.kr/data/openapi/openapiView.do?id=385&category=C&gubun=A"> 문화 진흥회 오픈 API</a> 를 이용하면 되지만 밑의 데이터 파싱 방법과는 다른 것 같음.

포스터 이미지 받아와서 이미지 뷰에 설정하려면 <a href ="https://square.github.io/picasso/"> 피카소</a> 외부 라이브러리 이용 

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/editText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPersonName"
        android:text="http://kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?key=f5eef3421c602c6cb7ea224104795888&#38;targetDt=20120101"/>

    <Button
        android:id="@+id/button"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>
```

<br>

**movie_item.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:elevation="5dp"
    android:orientation="vertical">

    <androidx.cardview.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="1dp"
        android:layout_marginLeft="8dp"
        android:layout_marginTop="4dp"
        android:layout_marginRight="8dp"
        android:layout_marginBottom="4dp"
        app:cardCornerRadius="10dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <ImageView
                android:id="@+id/imageView"
                android:layout_width="80dp"
                android:layout_height="80dp"
                android:padding="5dp"
                app:srcCompat="@drawable/movie" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_margin="5dp"
                android:layout_weight="1"
                android:orientation="vertical">

                <TextView
                    android:id="@+id/textView"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_gravity="right"
                    android:maxLines="1"
                    android:text="제목"
                    android:textSize="22sp" />

                <TextView
                    android:id="@+id/textView2"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginTop="10dp"
                    android:gravity="right"
                    android:paddingRight="10dp"
                    android:text="관객수"
                    android:textSize="20sp" />


            </LinearLayout>

        </LinearLayout>

    </androidx.cardview.widget.CardView>
</LinearLayout>
```

<br>


**MovieAdapter.java**

```java
public class MovieAdapter extends RecyclerView.Adapter<MovieAdapter.ViewHolder>{
    ArrayList<Movie>items = new ArrayList<Movie>();

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int viewType){
        LayoutInflater inflater = LayoutInflater.from(viewGroup.getContext());
        View itemView = inflater.inflate(R.layout.movie_item, viewGroup, false);

        return new ViewHolder(itemView);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder viewHolder, int position){
        // 현재 인덱스에 맞는 Movie 객체를 찾아 뷰 홀더에 객체를 설정
        Movie item = items.get(position);
        viewHolder.setItem(item);
    }

    @Override
    public int getItemCount(){
        return items.size();
    }

    public void addItem(Movie item){
        items.add(item);
    }

    public void setItems(ArrayList<Movie> items){
        this.items = items;
    }

    public Movie getItem(int position){
        return items.get(position);
    }

    public static class ViewHolder extends RecyclerView.ViewHolder{
        TextView textView;
        TextView textView2;

        public  ViewHolder(View itemView){
            super(itemView);

            this.textView = itemView.findViewById(R.id.textView);
            this.textView2 = itemView.findViewById(R.id.textView2);
        }

        public void setItem(Movie item){
            textView.setText(item.movieNm);
            textView2.setText(item.audiCnt + " 명");
        }
    }
}

```

<br>

**MovieList.java**

```java
public class MovieList {
    MovieListResult boxOfficeResult;
}
```

<br>

**MovieResult.java**

```java
public class MovieListResult {  
  
  String boxofficeType;  
  String showRange;  
  
  ArrayList<Movie> dailyBoxOfficeList = new ArrayList<Movie>();  
  
}
```
<br>

**Movie**

```java
package org.techtown.movie;

public class Movie {

    String rnum;
    String rank;
    String rankInten;
    String rankOldAndNew;
    String movieCd;
    String movieNm;
    String openDt;
    String salesAmt;
    String salesShare;
    String salesInten;
    String salesChange;
    String salesAcc;
    String audiCnt;
    String audiInten;
    String audiChange;
    String audiAcc;
    String scrnCnt;
    String showCnt;

}
```
<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {
    EditText editText;
    TextView textView;

    static RequestQueue requestQueue;

    RecyclerView recyclerView;
    MovieAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        editText = findViewById(R.id.editText);
        textView = findViewById(R.id.textView);

        Button button = findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                makeRequest();
            }
        });

        if (requestQueue == null) {
            requestQueue = Volley.newRequestQueue(getApplicationContext());
        }

        recyclerView = findViewById(R.id.recyclerView);

        LinearLayoutManager layoutManager = new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false);
        recyclerView.setLayoutManager(layoutManager);

        adapter = new MovieAdapter();
        recyclerView.setAdapter(adapter);

    }


    public void makeRequest() {
        String url = editText.getText().toString();

        StringRequest request = new StringRequest(
                Request.Method.GET,
                url,
                new Response.Listener<String>() {
                    @Override
                    public void onResponse(String response) {
                        println("응답 -> " + response);

                        processResponse(response);
                    }
                },
                new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        println("에러 -> " + error.getMessage());
                    }
                }
        ) {
            @Override
            protected Map<String, String> getParams() throws AuthFailureError {
                Map<String,String> params = new HashMap<String,String>();

                return params;
            }
        };

        request.setShouldCache(false);
        requestQueue.add(request);
        println("요청 보냄.");
    }

    public void println(String data) {
        Log.d("MainActivity", data);
    }

    public void processResponse(String response) {
        Gson gson = new Gson();
        MovieList movieList = gson.fromJson(response, MovieList.class);

        println("영화정보의 수 : " + movieList.boxOfficeResult.dailyBoxOfficeList.size());

        for (int i = 0; i < movieList.boxOfficeResult.dailyBoxOfficeList.size(); i++) {
            Movie movie = movieList.boxOfficeResult.dailyBoxOfficeList.get(i);

            adapter.addItem(movie);
        }

        adapter.notifyDataSetChanged();
    }

}
```

인터넷 권한 추가 

**AndroidManifest.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.techtown.movie">

    <uses-permission android:name="android.permission.INTERNET"/>

    <application
        android:usesCleartextTraffic="true"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.SampleMovie">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

<br>

**외부 라이브러리 추가**

**build.gradle(Module : Name)**

```xml

dependencies {  
  implementation 'com.squareup.picasso:picasso:2.71828'  
  implementation 'com.android.volley:volley:1.2.0'
```

---

**결과**

![1](https://user-images.githubusercontent.com/54762273/150937919-906e0991-c7a4-469d-8ee4-01114339d319.PNG)
