---
title: "더블 버퍼링 (Double Buffering)"
author: seyoung
date: '2022-01-30 11:00:00 +0900'
categories: Android User_Interface
tags: [android,layout,bitmap]
math: true
mermaid: true
---
## 더블 버퍼링 (Double Buffering)

비트맵 객체는 메모리에 만들어지는 이미지이다.

예를 들어,
사진을 찍었을 때 만들어지는 이미지 파일을 읽을 때 메모리에 비트맵 객체로 만들 수 있다. 

비트맵 객체를 이용하면 화면에 이미지를 그릴 수 있고 다양한 조작도 할 수 있다.
특히, 비트맵은 그래픽을 그릴 수 있는 메모리 공간을 제공한다.

흔히 **더블 버퍼링(Double Buffering)** 이라 불리는 방법인데, 별도의 메모리 공간에 미리 그래픽을 그린 후 뷰가 다시 그려져야 할 필요가 있을 때 미리 그려놓은 비트맵을 화면에 표시하는 방법이다.

별도의 메모리 공간에 미리 그래픽을 그린 후 뷰가 다시 그려져야 할 필요가 있을 때 미리 그려놓은 비트맵을 화면에 표시하는 방법이다.

### 더블 버퍼링 왜 사용하는가 ?

API를 시작하다보면 비트맵 이미지를 사용하게 된다. 그 때 이미지들이 전환되면서 영상처럼 부드럽게 움직일 거라 생각하지만 실제로 이미지들이 움직일 때마다 화면이 깜빡이는 현상이 눈에 들어온다. 쉽게 말하자면 아래와 같은 상황인 것이다.


▶ 게임 캐릭터이미지를 구현할 때 이미지를 움직이게 하고 싶다.

그러나 캐릭터가 띄엄띄엄 움직임과 동시에 깜빡거리는 화면 때문에 게임할 맛이 안난다.

그 이유는 컴퓨터가 이미지를 지웠다가 새 이미지를 다시 그리는 방식을 반복하기 때문이다.

이미지 그리는데 시간이 소요되므로 이미지의 출력이 잦을수록 깜빡거리는 현상이 심해진다.
  
이에 대한 해결방안으로 버퍼 역할을 해줄 메모리 장치 컨텍스트(보이지 않는 화면)를 하나 더 사용하여 그곳에 이미지를 그리고, 기존화면을 유지하다가 이미지가 완성되면 실제 화면 장치 컨텍스트로 한꺼번에 베껴 그리는 것이다.

  

**아래는 이를 그림으로 표현한 것이다.**

<img width="772" alt="스크린샷 2022-01-30 오후 11 04 37" src="https://user-images.githubusercontent.com/54762273/151703023-cfe46fcc-6489-4dbd-aacf-0f7c2714770c.png">


---

우선 /app/res/drawable 폴더에 이미지를 넣어준다.

그 후 **CustomViewImage.java** 클래스를 만들어 준 후 코드를 작성해 준다.
<br>

**CustomViewImage.java**

```java
public class CustomViewImage extends View { // View를 상속하여 새로운 View 정의  
  
  private Bitmap cacheBitmap; // 메모리에 만들어질 Bitmap 객체 선언  
  private Canvas cacheCanvas; // Bitmap 객체를 그리기 위한 Canvas 객체 선언  
  private Paint mPaint;  
  
    public CustomViewImage(Context context){  
        super(context);  
        init(context);  
    }  
  
    public CustomViewImage(Context context, AttributeSet attrs){  
        super(context);  
        init(context);  
    }  
  
    private void init(Context context){  
        mPaint = new Paint();  
    }  
  
    /* 뷰가 화면에 보이기 전에 Bitmap 객체 만들고 그 위에 그리기 */  
  protected void onSizeChanged(int w, int h, int oldw, int oldh){  
        createCacheBitmap(w, h);  
        testDrawing();  
    }  
  
    /* 메모리에 Bitmap 객체를 만들고 Canvas 객체 설정 */  
  private void createCacheBitmap(int w, int h){  
        cacheBitmap = Bitmap.createBitmap(w, h, Bitmap.Config.ARGB_8888);  
        cacheCanvas = new Canvas();  
        cacheCanvas.setBitmap(cacheBitmap);  
    }  
  
    /* 빨간 사각형 그리기 */  
  private void testDrawing(){  
        cacheCanvas.drawColor(Color.WHITE);  
        mPaint.setColor(Color.RED);  
        cacheCanvas.drawRect(100, 100, 200, 200, mPaint);  
    }  
  
    /* 메모리에 Bitmap을 이용해 화면에 그리기 */  
  protected  void onDraw(Canvas canvas){  
        if(cacheBitmap != null){  
            canvas.drawBitmap(cacheBitmap,0 ,0, null);  
        }  
    }  
  
}

```

인제 MainActivity에 CustomViewImage 클래스를 추가 해주자.

**CustomViewImage.java**


```java
public class MainActivity extends AppCompatActivity {  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        CustomViewImage view = new CustomViewImage(this);  
        setContentView(view);  
    }  
}
```

-- 

**결과**

<img width="385" alt="스크린샷 2022-01-30 오후 10 39 54" src="https://user-images.githubusercontent.com/54762273/151702151-cfd613dd-0818-4462-ac66-fecb181f476c.png">

---

뷰가 새로 그려질 때 호출되는 onDraw 메서드를 보면 단순히 메모리에 만들어 두었던 cacheBitmap을 그리는 코드만 들어가 있다. 실제 그래픽이 그려지는 시점은 testDrawing 메서드가 호출되었을 때 초기화된 후, testDrawing 메서드에 의해 그려지게 된다.

**더블 버퍼링을 이용한 그래픽 그리기 개념도**

![Untitled Diagram drawio](https://user-images.githubusercontent.com/54762273/151702706-983b85a6-742f-495e-abfa-6562e37cb650.png)

---

