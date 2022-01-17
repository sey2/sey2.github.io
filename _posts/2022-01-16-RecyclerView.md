---
title: "Recycler View"
author: seyoung
date: '2022-01-16 22:47:00 +0900'
categories: Android User_Interface
tags: [android,layout]
math: true
mermaid: true
---

## Recycler View

리스트 모양으로 보여줄 수 있는 위젯으로 Recycler View가 있다.

Recycler View는 기본적으로 좌우 상하 스크롤을 만들 수 있고 각각의 아이템이 화면에 보일 때

메모리를 효율적으로 사용하도록 캐시(Cache) 메커니즘이 규현되어 있다.

<br><br>

## Recycler View를 이용해 리스트 모양으로 보여주기

Recycler View는 선택 위젯이기 때문에 어댑터가 데이터 관리와 뷰 객체 관리를 담당한다.

따라서 Recycler Vie는 껍데기 역할을 한다고 생각하면 되고, 어댑터를 만들기 전에 어댑터 안에 들어갈

각 아이템의 데이터를 담아둘 클래스를 하나 정의해야 한다.

여기서는 Person 클래스를 만들어 보자.

Getter와 Setter를 쉽게 만드는 방법은 클래스 중괄호 안에 커서를 두고 오른쪽 마우스 버튼을 눌러

**[Generate-> Getter and Setter]** 메뉴를 이용하면 더욱 쉽고 빠르게 만들 수 있다.

**Person.java**

```java
public class Person {
    String name;
    String mobile;
    
    public Person(String name, String mobile){
        this.name = name;
        this.mobile = mobile;
    }

    public String getName() {
        return name;
    }

    public String getMobile() {
        return mobile;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setMobile(String mobile) {
        this.mobile = mobile;
    }
}
```

<br>

어댑터 클래스도 새롭게 정의 해줍니다.

**PersonAdapter.java**
```java
public class PersonAdapter extends RecyclerView.Adapter<PersonAdapter.ViewHolder>{
    ArrayList<Person> items = new ArrayList<Person>();

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(viewGroup.getContext());
        // 인플레이션을 통해 뷰 객체를 만들기
        View itemView = inflater.inflate(R.layout.person_item, viewGroup, false);

        // 뷰홀더 객체를 생성하면서 뷰 객체를 전달하고 그 뷰홀더 객체를 반환하기
        return new ViewHolder(itemView);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder viewHolder, int position) {
        Person item = items.get(position);
        viewHolder.setItem(item);
    }

    @Override
    public int getItemCount() {
        return items.size();
    }

    public void addItem(Person item){
        items.add(item);
    }

    public void setItem(ArrayList<Person> items){
        this.items = items;
    }

    public Person getItem(int position){
        return items.get(position);
    }

    public void setItem(int position, Person item){
        items.set(position, item);
    }

    static class ViewHolder extends RecyclerView.ViewHolder{
        TextView textView;
        TextView textView2;

        public ViewHolder(View itemView){
            // 뷰홀더 생성자로 전달되는 뷰 객체 참조하기
            super(itemView);

            // 뷰 객체에 들어 있는 텍스트 뷰 참조하기
            textView = itemView.findViewById(R.id.textView);
            textView2 = itemView.findViewById(R.id.textView2);
        }

        public void setItem(Person item){
            textView.setText(item.getName());
            textView2.setText(item.getMobile());
        }
    }
}
```

리스트 형태로 보일 때 각각의 아이템은 뷰로 만들어지며 

각각의 아이템을 위한 뷰는 뷰홀더에 담아두게 된다.

뷰홀더 역할을 하는 클래스를 PersonAdapter 클래스 안에 넣어둔다고 생각하자.

**setItem 메서드는 뷰홀더에 들어있는 뷰 객체의 데이터를 다른 것으로 보이도록 하는 역할을 한다.**

이 어댑터에 규현되어야 하는 중요한 메서드는 3가지이다.

**getItemCount 메서드는 어댑터에서 관리하는 아이템의 개수를 반환한다.**

**onCreateViewHolder와 onBindViewHolder 메서드는 뷰홀더 객체가 만들어질 때와 재사용될 때 자동 호출된다.**

Recycler View에 보이는 여러 개의 아이템은 내부에서 캐시되기 때문에 아이템 개수만큼 

객체로 만들어 지지 않는다 예를 들어, 아이템이 만 개라고  하더라도 이 아이템을 위해 

만 개의 뷰 객체가 만들어 지지 않는다. 

뷰홀더가 새로 만들어지는 시점에는 onCreateViewHolder 메서드가 호출되고 

그 안에서는 각 아이템을 위해 정의한 XML 레이아웃을 이용해 뷰 객체를 만들어 준다.

onBindViewHolder 메서드는 뷰홀더가 재사용될 때 호출되므로 뷰 객체는 기존 것을 그대로 사용하고

데이터만 바꿔준다. 이제 어댑터 안에 Person 객체들을 만들어 넣어야 하므로 아래와 같이 파일을 수정한다.

<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        RecyclerView recyclerView = findViewById(R.id.recyclerView);

        LinearLayoutManager layoutManager =
                new LinearLayoutManager(this,LinearLayoutManager.VERTICAL, false);
        recyclerView.setLayoutManager(layoutManager);
        PersonAdapter adapter = new PersonAdapter();

        adapter.addItem(new Person("김민수","010-1000-1000"));
        adapter.addItem(new Person("김하늘","010-2000-2000"));
        adapter.addItem(new Person("홍길동","010-3000-3000"));

        // 리싸이클러뷰에 어댑터 설정하기
        recyclerView.setAdapter(adapter);
    }
}
```

리싸이클러뷰에는 레이아웃 매니저를 설정할 수 있다.

레이아웃 매니저는 리싸이클러뷰가 보일 기본적인 형태를 설정할 때 자주 사용하는 형태로

세로 방향, 가로 방향, 격자 모양이다.

LinearLayoutManager 객체를 통해 방향을 VERTICAL로 설정하여 세로 방향 스크롤로 보이게 된다.

> 가로 방향은 HORIZONTAL, 격자 모양은 GridLayoutManger 객체를 사용해 column 수를 지정하면 된다.


인제 리스트 안에 들어갈 person_item.xml 파일을 만들어준다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical">

    <androidx.cardview.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:cardBackgroundColor="#FFFFFFFF"
        app:cardCornerRadius="10dp"
        app:cardElevation="5dp"
        app:cardUseCompatPadding="true" >

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <ImageView
                android:id="@+id/imageView"
                android:layout_width="80dp"
                android:layout_height="80dp"
                android:padding="5dp"
                app:srcCompat="@mipmap/ic_launcher" />

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
                    android:text="이름"
                    android:textSize="30sp" />

                <TextView
                    android:id="@+id/textView2"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="전화번호"
                    android:textColor="#FF0000FF"
                    android:textSize="25sp" />
            </LinearLayout>
        </LinearLayout>
    </androidx.cardview.widget.CardView>
</LinearLayout>
``` 
---

### 결과

![1](https://user-images.githubusercontent.com/54762273/149662702-ba3042a6-cda1-4b11-ae8d-c81781f838c8.PNG)

---

## 격자모양 RecyclerView

위에서 만든 List모양 RecyclerView에서 조금만 바꾸어 주면 된다.

RecyclerView가 보이는 모양은 레이아웃 매니저를 통해 결정이 되므로 MainActivity.java 파일을  수정한다.

**MainActivity.java**

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        RecyclerView recyclerView = findViewById(R.id.recyclerView);

        GridLayoutManager layoutManager =
                new GridLayoutManager(this,2);
                ...
        }
```

이렇게 수정해 주고 Person 객체를 추가 해주면 아래와 같이 격자모양  RecyclerView가 생성된다.

![1](https://user-images.githubusercontent.com/54762273/149759379-b0e2025d-a1a9-4dac-b949-d229622b386c.PNG)


