---
title: "Swipe Layout"
author: seyoung
date: '2022-02-21 02:12:00 +0900'
categories: Android User_Interface
tags: [android,ui]
math: true
mermaid: true
---

## SwipeLayout 

https://github.com/chthai64/SwipeRevealLayout


![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/154854124-cca3c4af-a6bc-4d32-bd12-58340e92677b.gif)

<br>

**의존성 추가**
```xml
implementation 'com.chauthai.swipereveallayout:swipe-reveal-layout:1.4.1'
```

**Adapter 등록 및 리스너 추가**

```java

public class NoteAdapter extends RecyclerView.Adapter<NoteAdapter.ViewHolder>
        implements OnNoteItemClickListener {

    static ArrayList<Note> items = new ArrayList<Note>();
    private final ViewBinderHelper binderHelper = new ViewBinderHelper();
    

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(viewGroup.getContext());
        View itemView = inflater.inflate(R.layout.note_item, viewGroup, false);


        return new ViewHolder(itemView, this, layoutType);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder viewHolder, int position) {
            Note item = items.get(position);

            binderHelper.setOpenOnlyOne(true);
            binderHelper.bind(viewHolder.swipelayout,Integer.toString(item.get_id()));
            viewHolder.bind(item);
            viewHolder.setItem(item);
            viewHolder.setLayoutType(layoutType);
    }

   public class ViewHolder extends RecyclerView.ViewHolder {
        SwipeRevealLayout swipelayout;
        private View deleteLayout;
        private View editLayout;

        LinearLayout layout1;
        LinearLayout layout2;
        
			...  (변수 선언 생략)

        NoteDatabase database;

       public ViewHolder(View itemView, final OnNoteItemClickListener listener, int layoutType) {
            super(itemView);

            swipelayout = (SwipeRevealLayout) itemView.findViewById(R.id.swipe_layout);
            deleteLayout = itemView.findViewById(R.id.txtDelete);
					
					... 생략 

           // 데이터 베이스 객체 얻어오기
           Fragment1 fragment1 = new Fragment1();
           database = NoteDatabase.getInstance(fragment1.context);

            setLayoutType(layoutType);
        }

        // Swipe Layout (삭제, 수정) 리스너 설정
        public void bind(final Note item){
            deleteLayout.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
					
					// 클릭한 Swipe Layout의 일기id (DB)
                    int position = item._id;

                    // 리싸이클러 뷰 아이템 목록에서 안보이게 하기
                    items.remove(getAdapterPosition());
                    notifyItemRemoved(getAdapterPosition());

                    // 일기 삭제 (DB 테이블 삭제)
                    deleteNote(position);
                }
            });
            
        }

        /* swipe 삭제 버튼 누르면 DB에서 해당 일기를 찾아 삭제 */
        public void deleteNote(int position){

           String sql = "delete from " + NoteDatabase.TABLE_NOTE +
                    " where " +
                    "   _id = " + position;

            database.execSQL(sql);
        }

    }
```

**Swipe 레이아웃과 관련된 코드가 아닌건 전부 생략하였음**

<br>


**Swipe Layout.xml**
```xml
<?xml version="1.0" encoding="utf-8"?>
<com.chauthai.swipereveallayout.SwipeRevealLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/swipelayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:dragEdge="right"
    app:mode="same_level">

    <!--Swipe Layout-->
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:orientation="horizontal">

    </LinearLayout>

    <!--Main Layout-->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:padding="20dp">

    </LinearLayout>

</com.chauthai.swipereveallayout.SwipeRevealLayout>
```

**현재 만들고 있는 다이어리 앱 xml 코드는 길어서 예제 파일의 xml을 첨부하였다.**

**Swipe Layout 부분이 스와이프 했을 때 보이는 부분**

**Main Layout 부분은 기본으로 보이는 부분**

<br>

<a href ="https://github.com/sey2/S_Diary"> 내가 작성한 다이어리 앱 코드 바로가기 </a>