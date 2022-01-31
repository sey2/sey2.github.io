---
title: "멀티 터치 이미지 뷰어"
author: seyoung
date: '2022-01-31 17:14:00 +0900'
categories: Android User_Interface
tags: [android,layout,bitmap]
math: true
mermaid: true
---


## 멀티터치 이미지 뷰어 만들기

애플이 아이폰을 처음 시연할 때 사람들이 놀라워했던 기능 중의 하나가 멀터터치 기능이다.
즉, 두 손가락을 이용해 손가락 사이를 벌리면 이미지가 점차 확대되고 손가락 사이를 좁히면 이미지가 작아지도록 만들 때 사용하는 기능인데 지금은 아주 일반적인 기능이 되어 있다.

멀티터치는 실제 앱을 만들 때 그렇게 자주 활용되지는 않지만 생각보다 어렵지 않기 때문에 간단히 살펴보자.

일반적인 이벤트 처리 과정에서 알 수 있는, 한 손가락으로 터치 했을 때의 x,y 좌표 값 뿐만 아니라 두 번째 손가락으로 터치 했을 때의 x,y 좌표 값까지 알 수 있다면 여러 손가락을 이용하는 기능을 만들 수 있다.

물론 손가락으로 눌렀을 때, 드래그 할 때 그리고 떨어졌을 때를 구분할 수 있어야 한다.
하나의 손가락에 대한 좌표 값은 `getX`와 `getY` 메서드를 이용해 알 수 있고, 눌린 상태는 `getAction` 메서드를 이용해 구분 할 수 있다. 

이 정보를 두 개의 손가락마다 구분하기 위해 다음과 같은 메서드가 제공된다.

**API**
```java
public final int getPointerCount()
public final float getX(int pointerIndex)
public final float getY(int pointerIndex)
```

세 가지 메서드는 뷰의 onTouch 메서드로 전달되는 MotionEvent 객체에 정의 되어 있으며, 여러개의 손가락으로 터치된 정보를 알기 위해 필요한 기본적인 메서드들이다.

`getPointerCount` 메서드는 몇 개의 손가락이 터치되었는지를 알 수 있도록 해주는 것이고,
만약 반환된 값이 1이라면 한 개의 손가락, 2라면 두개의 손가락이 터치된 상태이다.


이벤트 처리에 자주 사용되는 getX와 getY 메서드는 손가락이 하나일 때 X와 Y의 좌표 값을 가져오지만 `getX(int pointerIndex)`와 `getY(int pointerIndex)` 메서드는 여러 개의 손가락이 터치되었을 때 각각의 손가락이 가지는 인덱스의 값을 이용해 좌표 값을 확인할 수 있다.

예를 들어, 두 개의 손가락이 터치되면 첫 번째 손가락은 인덱스 0 , 두번 째 손가락은 인덱스 1이 되고, getPointerCount 메서드를 호출했을 때 반환되는 값은 2가 된다. 

첫 번째 손가락으로 터치한 지점을 알고 싶으면 getX(0) 과 getY(0)를 호출하면 된다.

---

### 예제

**ImageDisplayView.java**

```java
public class ImageDisplayView extends View implements View.OnTouchListener {  
    private static final String TAG = "ImageDisplayView";  
  
    Context mContext;  
    Canvas mCanvas;  
    Bitmap mBitmap;  
    Paint mPaint;  
  
    int lastX;  
    int lastY;  
  
    Bitmap sourceBitmap;  
  
    Matrix mMatrix;  
  
    float sourceWidth = 0.0F;  
    float sourceHeight = 0.0F;  
  
    float bitmapCenterX;  
    float bitmapCenterY;  
  
    float scaleRatio;  
    float totalScaleRatio;  
  
    float displayWidth = 0.0F;  
    float displayHeight = 0.0F;  
  
    int displayCenterX = 0;  
    int displayCenterY = 0;  
  
    public float startX;  
    public float startY;  
  
    public static float MAX_SCALE_RATIO = 5.0F;  
    public static float MIN_SCALE_RATIO = 0.1F;  
  
    float oldDistance = 0.0F;  
  
    int oldPointerCount = 0;  
    boolean isScrolling = false;  
    float distanceThreshold = 3.0F;  
  
    public ImageDisplayView(Context context) {  
        super(context);  
  
        mContext = context;  
  
        init();  
    }  
  
    public ImageDisplayView(Context context, AttributeSet attrs) {  
        super(context, attrs);  
  
        mContext = context;  
  
        init();  
    }  
  
    private void init() {  
        mPaint = new Paint();  
        mMatrix = new Matrix();  
  
        lastX = -1;  
        lastY = -1;  
  
        setOnTouchListener(this);  
    }  
  
    /* 뷰가 초기화 되고 나서 화면에 보이기 전 크기가 정해지면 호출되는 메서드 안에서 메모리 상에 새로운 비트맵 객체 생성 */  
	  protected void onSizeChanged(int w, int h, int oldw, int oldh) {  
        if (w > 0 && h > 0) {  
            newImage(w, h);  
  
            redraw();  
        }  
    }  
  
    public void newImage(int width, int height) {  
        Bitmap img = Bitmap.createBitmap(width, height, Bitmap.Config.ARGB_8888);  
        Canvas canvas = new Canvas();  
        canvas.setBitmap(img);  
  
        mBitmap = img;  
        mCanvas = canvas;  
  
        displayWidth = (float)width;  
        displayHeight = (float)height;  
  
        displayCenterX = width/2;  
        displayCenterY = height/2;  
    }  
  
    public void drawBackground(Canvas canvas) {  
        if (canvas != null) {  
            canvas.drawColor(Color.BLACK);  
        }  
    }  
  
    /* 뷰가 화면에 그려지는 메서드 안에서 메모리 상의 비트맵 객체 그리기 */  
  protected void onDraw(Canvas canvas) {  
        if (mBitmap != null) {  
            canvas.drawBitmap(mBitmap, 0, 0, null);  
        }  
    }  
  
    public void setImageData(Bitmap image) {  
        recycle();  
  
        sourceBitmap = image;  
  
        sourceWidth = sourceBitmap.getWidth();  
        sourceHeight = sourceBitmap.getHeight();  
  
        bitmapCenterX = sourceBitmap.getWidth()/2;  
        bitmapCenterY = sourceBitmap.getHeight()/2;  
  
        scaleRatio = 1.0F;  
        totalScaleRatio = 1.0F;  
    }  
  
    public void recycle() {  
        if (sourceBitmap != null) {  
            sourceBitmap.recycle();  
        }  
    }  
  
    public void redraw() {  
        if (sourceBitmap == null) {  
            Log.d(TAG, "sourceBitmap is null in redraw().");  
            return;  
        }  
  
        drawBackground(mCanvas);  
  
        float originX = (displayWidth - (float)sourceBitmap.getWidth()) / 2.0F;  
        float originY = (displayHeight - (float)sourceBitmap.getHeight()) / 2.0F;  
  
        mCanvas.translate(originX, originY);  
        mCanvas.drawBitmap(sourceBitmap, mMatrix, mPaint);  
        mCanvas.translate(-originX, -originY);  
  
        invalidate();  
    }  
  
    /* 뷰를 터치할 때 호출되는 메서드 다시 정의 */  
  public boolean onTouch(View v, MotionEvent ev) {  
        final int action = ev.getAction();  
  
        int pointerCount = ev.getPointerCount();    // 터치했을 때 몇개의 손가락으로 터치하는지 개수 확인  
	    Log.d(TAG, "Pointer Count : " + pointerCount);  
  
        switch (action) {  
            case MotionEvent.ACTION_DOWN:   // 손가락으로 눌렀을때의 기능 추가  
  
			    if (pointerCount == 1) {  
                    float curX = ev.getX();  
                    float curY = ev.getY();  
  
                    startX = curX;  
                    startY = curY;  
  
                } else if (pointerCount == 2) {  
                    oldDistance = 0.0F;  
  
                    isScrolling = true;  
                }  
  
                return true;  
            case MotionEvent.ACTION_MOVE:   // 손가락으로 움직일 때의 기능 추가  
  
			  if (pointerCount == 1) {  
  
                    if (isScrolling) {  
                        return true;  
                    }  
  
                    float curX = ev.getX();  
                    float curY = ev.getY();  
  
                    if (startX == 0.0F) {  
                        startX = curX;  
                        startY = curY;  
  
                        return true;  
                    }  
  
                    float offsetX = startX - curX;  
                    float offsetY = startY - curY;  
  
                    if (oldPointerCount == 2) {  
  
                    } else {  
                        Log.d(TAG, "ACTION_MOVE : " + offsetX + ", " + offsetY);  
  
                        if (totalScaleRatio > 1.0F) {  
                            moveImage(-offsetX, -offsetY);  // 한손가락으로 움직이고 있을 때는 moveImage 메서드 호출  
  }  
  
                        startX = curX;  
                        startY = curY;  
                    }  
  
                } else if (pointerCount == 2) {  
  
                    float x1 = ev.getX(0);  
                    float y1 = ev.getY(0);  
                    float x2 = ev.getX(1);  
                    float y2 = ev.getY(1);  
  
                    float dx = x1 - x2;  
                    float dy = y1 - y2;  
                    float distance = new Double(Math.sqrt(new Float(dx * dx + dy * dy).doubleValue())).floatValue();  
  
                    float outScaleRatio = 0.0F;  
                    if (oldDistance == 0.0F) {  
                        oldDistance = distance;  
  
                        break;  
                    }  
  
                    if (distance > oldDistance) {  
                        if ((distance-oldDistance) < distanceThreshold) {  
                            return true;  
                        }  
  
                        outScaleRatio = scaleRatio + (oldDistance / distance * 0.05F);  
                    } else if (distance < oldDistance) {  
                        if ((oldDistance-distance) < distanceThreshold) {  
                            return true;  
                        }  
  
                        outScaleRatio = scaleRatio - (distance / oldDistance * 0.05F);  
                    }  
  
                    if (outScaleRatio < MIN_SCALE_RATIO || outScaleRatio > MAX_SCALE_RATIO) {  
                        Log.d(TAG, "Invalid scaleRatio : " + outScaleRatio);  
                    } else {  
                        Log.d(TAG, "Distance : " + distance + ", ScaleRatio : " + outScaleRatio);  
                        scaleImage(outScaleRatio);  // 두 손가락으로 움직이고 있을 때는 scaleImage 호출  
  }  
  
                    oldDistance = distance;  
                }  
  
                oldPointerCount = pointerCount;  
  
                break;  
  
            case MotionEvent.ACTION_UP: // 손가락을 떄었을 때의 기능 추가  
  
				    if (pointerCount == 1) {  
  
                    float curX = ev.getX();  
                    float curY = ev.getY();  
  
                    float offsetX = startX - curX;  
                    float offsetY = startY - curY;  
  
                    if (oldPointerCount == 2) {  
  
                    } else {  
                        moveImage(-offsetX, -offsetY);  
                    }  
  
                } else {  
                    isScrolling = false;  
                }  
  
                return true;  
        }  
  
        return true;  
    }  
  
    /* 매트릭스 객체를 사용해 이미지 크기 변경 */  
	  private void scaleImage(float inScaleRatio) {  
        Log.d(TAG, "scaleImage() called : " + inScaleRatio);  
  
        mMatrix.postScale(inScaleRatio, inScaleRatio, bitmapCenterX, bitmapCenterY);  
        mMatrix.postRotate(0);  
  
        totalScaleRatio = totalScaleRatio * inScaleRatio;  
  
        redraw();  
    }  
  
    /* 매트릭스 객체를 사용해 이미지 이동 */  
	  private void moveImage(float offsetX, float offsetY) {  
        Log.d(TAG, "moveImage() called : " + offsetX + ", " + offsetY);  
  
        mMatrix.postTranslate(offsetX, offsetY);  
  
        redraw();  
    }  
  
}
```

이미지를 보여주고 터치했을 때의 이벤트를 이용해 이미지를 확대/축소하거나 이동하기 위해 새로 정의하는 ImageDisplayView 클래스는 일반 View 클래스를 상속한다.

또한 이 클래스는 터치 이벤트를 처리하므로 onTouchListener 인터페이스를 구현 하도록 한다. 이 인터페이스는 onTouch 메서드를 포함하고 있어서, 뷰에 설정할 경우 터치 이벤트가 발생할 때마다 onTouch 메서드를 호출한다.

우리가 원하는 대로 두 손가락을 이용해 이미지를 크게 만들려고 하면 이 메서드 안에서 터치할 때의 좌표 값을 받아 이미지의 크기를 바꾸어야 한다.

이미지의 크기를 확대 또는 축소하거나 이미지를 이동하기 위해 사용할 수 있는 방법은 Matrix 객체를 사용하는 것이다. 이 객체는 수학적인 연산을 통해 비트맵 이미지의 각 픽셀 값을 변경해주는데, 이 객체를 사용하기 쉽도록 다음과 같이 비트맵 이미지를 확대하거나 이동하는 메서드가 이미 정의되어 있으므로 단순히 이 메서드를 호출하는 것만으로도 원하는 기능을 만들 수 있다. 

**Matrix API**
```java
public boolean postScale(float sx, float sy, float px, float py)
public boolean postTranslate(float dx, float dy)
public boolean postRotate(float degress)
```

`postScale` 메서드는 비트맵 이미지를 확대 또는 축소 할 수 있다.

 - **첫 번째 매개 변수**는 X축을 기준으로 확대하는 비율
 - **두 번째 매개 변수**는 Y축을 기준으로 확대하는 비율
 - **세 번째, 네 번째 매개 변수**는 확대 또는 축소할 때 기준이 되는 위치가 되는데 일반적으로 비트맵 이미지의 중심점을 지정한다.

<br>

`postTranslate` 메서드는 비트맵 이미지를 이동시킬 때 사용한다.

 - **첫 번째, 두 번째 매개 변수**는 이동할 만큼의 X와 Y 좌표 값을 의미

<br>

`postRotate` 메서드는 비트맵 이미지를 회전시킬 때 사용한다.

 - **첫 번째 매개 변수**는 회전 각도를 의미한다.
<br>

이렇게 세가지 메서드를 통해 비트맵 이미지를 확대/축소 할 수 있지만 터치 이벤트를 처리해 기능을 구현하기 전에 먼저 뷰에 사진 이미지를 보여줄 수 있어야 한다.

또한 터치 이벤트를 지속적으로 처리해 다시 그려주는 과정이 필요하므로 메모리에 비트맵 이미지를 만들어 두고 `onDraw` 메서드 안에서 메모리에 만들어둔 비트맵 이미지를 화면에 보여주는 **더블 버퍼링 방식**을 사용해야한다.

비트맵 이미지를 초기화하는 부분은 `init` 메서드가 아닌 `onSizeChanged` 메서드에 들어 있는데 그 이유는 뷰가 화면에 보이기 전에 `onSizeChanged`메서드가 호출되므로 이 메서드 안에서 비트맵 이미지를 만드는 것이 효율적이기 때문이다.

**MainActivity.java**

```java
public class MainActivity extends AppCompatActivity {  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        LinearLayout container = findViewById(R.id.container);  
        Resources res = getResources();  
        Bitmap bitmap = BitmapFactory.decodeResource(res, R.drawable.beach);  
  
        ImageDisplayView view = new ImageDisplayView(this);  
        view.setImageData(bitmap);  
  
        LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(  
                LinearLayout.LayoutParams.MATCH_PARENT,  
                LinearLayout.LayoutParams.MATCH_PARENT);  
        container.addView(view, params);  
    }  
}
```

---

**결과**

<img width="390" alt="스크린샷 2022-01-31 오후 5 08 44" src="https://user-images.githubusercontent.com/54762273/151758850-c9436153-b788-4e8f-8717-9156b3620bdb.png">