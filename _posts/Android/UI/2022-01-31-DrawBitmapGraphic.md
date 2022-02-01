---
title: "비트맵 객체로 그래픽 그리기"
author: seyoung
date: '2022-01-31 00:08:00 +0900'
categories: Android User_Interface
tags: [android,layout,bitmap]
math: true
mermaid: true
---

## 비트맵 객체로 그래픽 그리기

`Bitmap`을 다루기 위해 주로 사용되는 녀석은 Bitmap안에 있는 메소드가 아니라`BitmapFactory`안에 있는 메소드를 주로 사용한다. 

`BitmapFactory` 클래스는 파일(file), 스트림(stream), 바이트-배열(byte-array) 등의 다양한 소스(source)로부터 이미지 데이터를 읽어들여 `Bitmap`객체를 생성하는 클래스이다.

![Untitled Diagram drawio](https://user-images.githubusercontent.com/54762273/151703628-c05f7b3b-c966-4fe0-bcdc-827d9eec763e.png)

BitmapFactory 클래스에 정의된 대표적인 메서드들은 다음과 같다.

|  |  |
|:--:|:--:|
| 파일에서 읽기 | 파일 path를 지정하면 해당 위치의 이미지 파일을 읽어온다.<br> -> public static Bitmap decodeFile(String pathName) |
| 리소스에서 읽기| 리소스에 저장한 이미지 파일을 읽어온다. <br> -> public static Bitmap decodeResource(Resources res, int id)|
| 바이트 배열에서 읽기| 바이트 배열로 되어 있는 이미지 파일을 읽어 온다. <br> -> public static Bitmap decodeByteArray(byte [] data, int offset, int length)
| 스트림에서 읽기| 입력 스트림에서 이미지 파일을 읽어 온다. <br> -> public static Bitmap decodeStream(InputStream is)|

바이트 배열이나 스트림에서 읽을 수 있다는 것은 원격지에 있는 이미지 파일도 손쉽게 읽어 들일 수 있다는 것을 의미한다.

 원격지의 소켓 서버나 웹 서버에 있는 이미지 파일은 서버에 연결한 후 입력 스트림으로 이미지를 읽어 들이거나 바이트 배열의 형태로 읽어 들인 데이터를 decodeBtyeArray 메서드를 이용해서 이미지로 변환 할 수 있다.


---

**예제**

**CustomViewImg.java**

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
  
        /* 리소스의 이미지 파일을 읽어 들여 화면에 그리기) */  
  Bitmap srcImg = BitmapFactory.decodeResource(getResources(), R.drawable.water);  
        cacheCanvas.drawBitmap(srcImg, 30, 30, mPaint);  
  
        /* Matrix 객체를 이용해 좌우 대칭이 되는 비트맵 이미지 만들어 그리기 */  
  Matrix horInverseMatrix = new Matrix();  
        horInverseMatrix.setScale(-1,1);  
        Bitmap horInverseImg = Bitmap.createBitmap(srcImg, 0, 0, srcImg.getWidth(),  
                                                     srcImg.getHeight(),horInverseMatrix, false);  
        cacheCanvas.drawBitmap(horInverseImg,30,130,mPaint);  
  
        /* Matrix 객체를 이용해 상하 대칭이 되는 비트맵 이미지 만들어 그리기 */  
  Matrix verInverseMatrix = new Matrix();  
        verInverseMatrix.setScale(1,-1);  
        Bitmap verInverseImg = Bitmap.createBitmap(srcImg, 0,0, srcImg.getWidth(),  
                                                     srcImg.getHeight(), verInverseMatrix, false);  
        cacheCanvas.drawBitmap(verInverseImg,30,230,mPaint);  
  
        mPaint.setMaskFilter(new BlurMaskFilter(10, BlurMaskFilter.Blur.NORMAL));  
        Bitmap scaledImg = Bitmap.createScaledBitmap(srcImg, srcImg.getWidth() *3,  
                                                    srcImg.getHeight() * 3, false);  
    }  
  
    /* 메모리에 Bitmap을 이용해 화면에 그리기 */  
  protected  void onDraw(Canvas canvas){  
        if(cacheBitmap != null){  
            canvas.drawBitmap(cacheBitmap,0 ,0, null);  
        }  
    }  
  
}
```

**이미지를 다양 한 효과를 주기 위해 마스크(Mask)를 사용하기도 한다.**

여러 마스크 필터 중에서 **BlurMaskFilter**는 번짐 효과를 낼 수 있는 마스크 필터로 페인트 객체에 설정하여 사용할 수 있다. 

비트맵 이미지를 확대할 때는 **createScaledBitmap** 메서드를 사용할 수 있다.

---
**결과**

<img width="408" alt="스크린샷 2022-01-30 오후 11 52 07" src="https://user-images.githubusercontent.com/54762273/151704781-8357669d-009b-4d07-9c33-18526135926b.png">