---
title: "로어블 객체로 만들어 그리기"
author: seyoung
date: '2022-01-30 21:54:00 +0900'
categories: Android User_Interface
tags: [android,layout]
math: true
mermaid: true
---

## 드로어블 객체로 만들어 그리기

그래픽 그리기가 가능한 요소들은 드로어블(Drawable) 객체로 만들어 그릴 수 있다.

드로어블 객체는 이미 XML 파일로 정의할 수 있다는 것을 알고 있다. 

이 드로어블 객체는 그릴 수 있는 모든 것을 의미하는데 대표적으로 ShapeDrawable,

BitmapDrawable, PicutreDrawable, LayerDrawable등이 있다.

그리기 객체의 형태로는 PNG, JPEG 이미지 등을 표현하는 **비트맵(Bitmap)**, 이미지가 자동으로 늘어나는 부분을 설정하여 사용하는  **나인패치(NinePatch)**, 도형 그리기가 가능한  **셰이프(Shape)**, 세로축의 순서에 따라 그리는  **레이어 (Layer)** 등이 있다.

**그리기 메서드를 사용하면 다양한 그래픽을 그릴 수 있는데 왜 굳이 그리기 객체르 만들어서** **그리는 방법이 따로 있는 걸까?**

그래픽을 그리는 하나의 단위를 그리기 객체로 만들어 두면 각각의 그래픽 그리기 작업을 독립적인 객체로 나누어 관리 할 수 있는 장점이 생기기 때문이다.

그리기 객체를 사용하는 방법은 크게 세 가지로 나눌 수 있다.

|**구분**  | **설명** |
|:--:|:--:|
|리소스 파일의 사용  | 프로젝트 리소스에 이미지와 같은 파일을 포함시킨 후 <br>읽어 들여 사용한다. |
|XML로 정의하여 사용| 그리기 객체의 속성을 정의한 XML 파일을 정의하여 사용한다.|
|소스 코드에서 객체를 <br>만들어 사용| 소스 코드에서 new 연산자를 이용하여 그리기 객체를 만든 후 <br>  사용한다. 비트맵 이미지를 주어진 좌표 값에 그린다.|

---

### ShapeDrawable에 정의된 정보를 이용해 그라데이션 효과 주기

**CustomViewDrawable.java**

```java
public class CustomViewDrawable extends View {  
  
    private ShapeDrawable upperDrawable;  
    private ShapeDrawable lowerDrawable;  
  
    public CustomViewDrawable(Context context){  
        super(context);  
  
        init(context);  
    }  
  
    public CustomViewDrawable(Context context, AttributeSet attrs){  
        super(context, attrs);  
  
        init(context);  
    }  
  
    private void init(Context context){  
  
        /* 윈도우 매니저를 이용해 뷰의 폭과 높이 확인 */  
	    WindowManager manager = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);  
        Display display = manager.getDefaultDisplay();  
        int width = display.getWidth();  
        int height = display.getHeight();  
  
        /* 리소스에 정의된 색상 값을 변수에 설정*/  
		Resources curRes = getResources();  
        int blackColor = curRes.getColor(R.color.color01);  
        int grayColor = curRes.getColor(R.color.color02);  
        int darkGrayColor = curRes.getColor(R.color.color03);  
  
        upperDrawable = new ShapeDrawable(); // Drawable 객체 생성  
  
	    RectShape rectangle = new RectShape();  
        rectangle.resize(width, height * 2 / 3);  
        upperDrawable.setShape(rectangle);  
        upperDrawable.setBounds(0,0,width,height * 2 / 3);  
  
        LinearGradient gradient = new LinearGradient(0, 0, 0, height * 2 / 3,  
                                        grayColor,blackColor, Shader.TileMode.CLAMP);  
  
        Paint paint = upperDrawable.getPaint();  
        paint.setShader(gradient); // Paint 객체에 새로 생성한 LinearGradient 객체를 Shader로 설정  
  
	    lowerDrawable = new ShapeDrawable();  
	  
        RectShape rectangle2 = new RectShape();  
        rectangle2.resize(width, height * 1 / 3);  
        lowerDrawable.setShape(rectangle2);  
        lowerDrawable.setBounds(0, height * 2 / 3, width, height);  
  
        LinearGradient gradient2 = new LinearGradient(0, 0, 0, height * 1 / 3,  
                                            blackColor, darkGrayColor, Shader.TileMode.CLAMP);  
  
        Paint paint2 = lowerDrawable.getPaint();  
        paint2.setShader(gradient2);  
 
    }  
  
  
    protected  void onDraw(Canvas canvas){  
        super.onDraw(canvas);  
  
        upperDrawable.draw(canvas);         // onDraw 메서드 안에서 Drawable 객체 그리기  
		lowerDrawable.draw(canvas);  
    }  
}
```

뷰가 채워지는 화면의 크기를 알아오기 위해 시스템 서비스 객체인 윈도우 매니저를 참조한다. 색상 정보는 XML 파일로 저장되었으며, /app/res/values/ 폴더 밑에 color.xml 이라는 이름으로 저장되어 있다.

이렇게 색상을 지정할 때는 소스 코드에서 설정하는 경우와 XML 리소스에 설정하는 경우로 나눌 수 있다. 소스 코드에서는 Color 클래스에 정의된 상수 또는 argb 메서드를 이용한다.

```java
int color = Color.BLUE; // 파란색 사용
int transColor = Color.argb(128, 0, 0, 255); // 반투명 파란색 사용
```

XML 리소스를 이용하는 겨웅에는 XML 파일에 \<color> 태그로 정의된 색상 값을 지정한 후, 소스 코드에서 리소스 객체의 getColor 메서드를 이용하여 읽어 온다.
위와 같은 효과를 줄 수 있다. 이번 예제에서는 XML 소스를 이용한다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <color name="transcolor">#6600000ff</color>
</resources>
```

```java
// 소스코드
int transcolor = getResources().getColor(R.color.transcolor);
```
<br>

XML 에서 색상 값을 설정하기 위해 /app/res/values/colors.xml 파일을 열고 color01, coloro2, color03 이라는 이름을 가진 색상 값을 추가한다.

```xml
     ....
<color name="color01">#FF000000</color>  
<color name="color02">#FF888888</color>  
<color name="color03">#FF333333</color>
     ....
```

그리기 객체는 draw 메서드를 직접 호출하여 그릴 수 있으므로 onDraw 메서드 내에서 호출하되 파라미터로 캔버스 객체를 전달한다.

LinearGradinent를 이용하면 뷰 영역의 위쪽 2/3와 아래 쪽 1/3을 따로 채워줌으로써 위쪽에서부터 아래쪽으로 색상이 조금씩 변하는 배경 화면을 만들 수 있다.

**LinearGradient**는 선의 형태로 그라데이션 효과를 준다.

마지막으로 **MainActivity.java**를 수정한다.

```java
public class MainActivity extends AppCompatActivity {  
  
    @Override  
  protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        CustomViewDrawable view = new CustomViewDrawable(this);  
        setContentView(view);  
    }  
}
```

---

**결과**

<img width="392" alt="스크린샷 2022-01-30 오후 9 51 06" src="https://user-images.githubusercontent.com/54762273/151700491-cc19c351-1be3-4906-ab5d-0cb916ea1ca4.png">


