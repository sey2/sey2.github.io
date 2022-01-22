---
title: "쓰레드로 여러 이미지 번갈아 보여주기"
author: seyoung
date: '2022-01-22 22:19:00 +0900'
categories: Android Thread Android Animation
tags: [android,thread,handler,animation]
math: true
mermaid: true
---

## 스레드로 애니메이션 만들기

여러 이미지를 연속해서 바꿔가며 애니메이션 효과를 만들고 싶을 때 스레드를 사용하는 경우가 많다.

아래 예제를 통해 우리 귀여운 꿀이를 보는 동시에 애니메이션 효과를 알아보자

--- 
**MainActivity.java**
```java
public class MainActivity extends AppCompatActivity {
    ImageView imageView;

    ArrayList<Drawable> drawableList = new ArrayList<Drawable>();
    Handler handler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Resources res = getResources();
        drawableList.add(res.getDrawable(R.drawable.m1));
        drawableList.add(res.getDrawable(R.drawable.m2));
        drawableList.add(res.getDrawable(R.drawable.m3));
        drawableList.add(res.getDrawable(R.drawable.m4));

        imageView = findViewById(R.id.imageView);
        Button button = findViewById(R.id.button);
        button.setOnClickListener((v)->{
            AnimThread thread = new AnimThread();
            thread.start();
        });
    }

    class AnimThread extends Thread{
        public void run(){
            int idx =0;
            for(int i=0; i<100; i++){
                final Drawable drawable = drawableList.get(idx);
                idx+=1;

                if(idx>3)
                    idx=0;

                handler.post(new Runnable() {
                    @Override
                    public void run() {
                        imageView.setImageDrawable(drawable);
                    }
                });

                try{
                    Thread.sleep(1000);
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }
    }

```

### 예제

![ezgif com-gif-maker](https://user-images.githubusercontent.com/54762273/150639915-2b03e57d-4c43-47d8-b846-39d6c7f7a1b3.gif)
