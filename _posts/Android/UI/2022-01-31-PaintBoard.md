---
title: "페인트 보드"
author: seyoung
date: '2022-01-31 15:40:00 +0900'
categories: Android User_Interface
tags: [android,layout,paintboard]
math: true
mermaid: true
---

## 페인트 보드 만들기

페인트 보드는 손가락으로 터치하는 방식의 터치 이벤트를 처리하여 빈 화면 위에 손가락으로 그림을 그릴 수 있는 가장 단순한 형태이다.

즉, onTouchEvent 메서드로 터치한 곳의 좌표 값을 이용하여 그리기 기능을 구현한 것이다.
터치 이벤트가 동작하는 방식은 크게 **(1)눌렀을 때와 (2) 누른 상태로 움직일 때 그리고 (3) 떼었을 때로 나눌 수 있다.** 

각각의 경우에 대하여 이벤트를 처리하면서 drawLine 메서드로 선을 그리면 된다.

이런 페인트 보드는실제 업무용으로 많이 사용되는 물류/택배 분에서 고객들에게 사인을 받을때 사용되기도 한다.

---

**예제** 

**PaintBoard.java**

```java
public class PaintBoard extends View {  
    Canvas mCanvas;  
    Bitmap mBitmap;  
    Paint mPaint;  
  
    int lastX;  
    int lastY;  
  
    public PaintBoard(Context context){  
        super(context);  
        init(context);  
    }  
  
    public PaintBoard(Context context, AttributeSet attrs){  
        super(context, attrs);  
        init(context);  
    }  
  
    private void init(Context context){  
        this.mPaint = new Paint();  
        this.mPaint.setColor(Color.BLACK);  
  
        this.lastX = -1;  
        this.lastY = -1;  
    }  
  
    protected  void onSizeChanged(int w, int h, int oldw, int oldh){  
        Bitmap img = Bitmap.createBitmap(w, h, Bitmap.Config.ARGB_8888);  
        Canvas canvas = new Canvas();  
        canvas.setBitmap(img);  
        canvas.drawColor(Color.WHITE);  
  
        mBitmap = img;  
        mCanvas = canvas;  
    }  
  
    protected  void onDraw(Canvas canvas){  
        if (mBitmap != null)  
            canvas.drawBitmap(mBitmap,0 ,0, null);  
    }  
  
    public boolean onTouchEvent(MotionEvent event){  
        int action = event.getAction();  
  
        int X = (int) event.getX();  
        int Y = (int) event.getY();  
  
        switch (action){  
            case MotionEvent.ACTION_UP:  
                lastX = -1;  
                lastY = -1;  
                break;  
  
            case MotionEvent.ACTION_DOWN:  
                if(lastX != -1){  
                    if(X != lastX || Y != lastY)  
                        mCanvas.drawLine(lastX, lastY, X, Y, mPaint);  
                    lastX = X;  
                    lastY = Y;  
                    break;  
                }  
  
            case MotionEvent.ACTION_MOVE:  
                if(lastX != -1)  
                    mCanvas.drawLine(lastX, lastY, X, Y, mPaint);  
                lastX = X;  
                lastY = Y;  
                break;  
        }  
  
        invalidate();  
  
        return true;  
  
    }  
  
}
```

손가락으로 누른 ACTION_DOWN 상태에서 좌표 값을 변수에 저장한 후 ACTION_MOVE 상태에서 이전 좌표 값과 현재의 좌표 값을 연결하여 선을 그리는 것이다.

<br>

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {  
  
  @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        PaintBoard view = new PaintBoard(this);  
        setContentView(view);  
    }  
}
```

---

**결과**

<img width="389" alt="스크린샷 2022-01-31 오후 2 11 52" src="https://user-images.githubusercontent.com/54762273/151742261-e0947fdd-67b8-43b5-8c21-9e8b69062494.png">

---

그런데 그림을 그리 때 손가락을 빨리빨리 움직여보면 선이 부드러운 곡선이 아니라 일부분이 직선으로 그려지는 것을 볼 수 있다. 그 이유는 터치 이벤트를 처리할 때 직선으로 각각의 좌표 값을 연결했기 때문이다.

**이 부분을 좀 더 부드럽게 처리하려면 어떻게 해야할까?**

여라가지 그리기 메서드 중에서 패스를 이용하면 연속적인 점들을 이용하여 직선 또는 부드러운 곡선을 그릴 수 있다.

흔히 **아크(Arc) 또는 커브(Curve)** 로도 표현되는 곡선 그리기 방법은 drawLine 메서드를 이용하여 직선을 그려 점들을 연결할 때 발생하는 격자형의 딱딱함을 없앨 수 있다.

<br>

**BestPaintBoard.java**


```java
public class BestPaintBoard extends View {  
  
    public boolean changed = false;  
  
    Canvas mCanvas;  
    Bitmap mBitmap;  
    Paint mPaint;  
  
    float lastX;  
    float lastY;  
  
    Path mPath = new Path();  
  
    float mCurveEndX;  
    float mCurveEndY;  
  
    int mInvaliadteExtraBorder = 10;  
  
    static final float TOUCH_TOLERANCE = 8;  
  
    public BestPaintBoard(Context context){  
        super(context);  
        init(context);  
    }  
  
    public BestPaintBoard(Context context, AttributeSet attrs){  
        super(context);  
        init(context);  
    }  
  
    private void init(Context context){  
        mPaint = new Paint();  
        mPaint.setAntiAlias(true);  
        mPaint.setColor(Color.BLACK);  
        mPaint.setStyle(Paint.Style.STROKE);  
        mPaint.setStrokeJoin(Paint.Join.ROUND);  
        mPaint.setStrokeCap(Paint.Cap.ROUND);  
        mPaint.setStrokeWidth(3.0F);  
  
        this.lastX = -1;  
        this.lastY = -1;  
    }  
  
    protected void onSizeChanged(int w, int h, int oldw, int oldh){  
        Bitmap img = Bitmap.createBitmap(w, h, Bitmap.Config.ARGB_8888);  
        Canvas canvas = new Canvas();  
        canvas.setBitmap(img);  
        canvas.drawColor(Color.WHITE);  
  
        mBitmap = img;  
        mCanvas = canvas;  
  
    }  
  
    protected  void onDraw(Canvas canvas){  
        if(mBitmap != null)  
            canvas.drawBitmap(mBitmap, 0, 0 ,null);  
    }  
  
    public boolean onTouchEvent(MotionEvent event){  
        int action = event.getAction();  
        switch (action){  
            case MotionEvent.ACTION_UP:  
                changed = true;  
  
                Rect rect = touchUp(event, false);  // Rect는 사각형을 만드는 클래스  
			    if(rect != null){  
                    invalidate(rect);  
                }  
  
                mPath.rewind(); // 라인 커브는 지우고 내부 정보는 다시 사용  
  
  return true;  
  
            case MotionEvent.ACTION_DOWN:  
                rect = touchDown(event);  
                if(rect != null){  
                    invalidate(rect);  
                }  
                return true;  
  
            case MotionEvent.ACTION_MOVE:  
                rect = touchMove(event);  
                if(rect != null){  
                    invalidate(rect);  
                }  
                return true;  
        }  
        return false;  
    }  
  
    private Rect touchDown(MotionEvent event){  
        float x = event.getX();  
        float y = event.getY();  
  
        lastX = x;  
        lastY = y;  
  
        Rect mInvalidRect = new Rect();  
        mPath.moveTo(x,y);  
  
        final int border = mInvaliadteExtraBorder;  
        mInvalidRect.set((int) x - border, (int) y - border, (int) x + border, (int) y + border);  
        mCurveEndX = x;  
        mCurveEndY = y;  
  
        mCanvas.drawPath(mPath, mPaint);  
  
        return mInvalidRect;  
  
    }  
  
    private Rect touchMove(MotionEvent event){  
        return processMove(event);  
    }  
  
    private Rect touchUp(MotionEvent event, boolean cancel){  
        return processMove(event);  
    }  
  
    private Rect processMove(MotionEvent event){  
        final float x = event.getX();  
        final float y = event.getY();  
  
        final float dx = Math.abs(x - lastX);  
        final float dy = Math.abs(y - lastY);  
  
        Rect mInvalidRect = new Rect();  
        if(dx >= TOUCH_TOLERANCE || dy >= TOUCH_TOLERANCE){ // 단순 터치인지 선을 그린 것인지 판단  
		    final int border = mInvaliadteExtraBorder;  
            mInvalidRect.set((int) mCurveEndX - border, (int) mCurveEndY - border,  
                    (int) mCurveEndX + border, (int) mCurveEndY + border);  
  
            float cX = mCurveEndX = (x + lastX) / 2;  
            float cY = mCurveEndY = (y + lastY) / 2;  
  
            mPath.quadTo(lastX, lastY, cX, cY);  
  
            mInvalidRect.union((int) lastX - border, (int) lastY - border,  
                    (int) cX + border, (int) cY + border);  
  
            lastX = x;  
            lastY = y;  
  
            mCanvas.drawPath(mPath, mPaint);  
  
        }  
        return mInvalidRect;  
    }  
  
  
}
```

---

**결과**

<img width="398" alt="스크린샷 2022-01-31 오후 3 22 04" src="https://user-images.githubusercontent.com/54762273/151749566-bde58690-7126-44a4-b5dc-6bf261fa617e.png">
