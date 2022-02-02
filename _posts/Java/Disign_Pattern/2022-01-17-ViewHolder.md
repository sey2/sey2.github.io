---
title: "ViewHolder Pattern"
author: seyoung
date: '2022-01-17 21:56:00 +0900'
categories: Java Design_Pattern
tags: [android,viewholder,pattern,recyclerview,adapter]
math: true
mermaid: true
---

## ViewHolder Pattern

RecyclerView를 공부하던 중 ViewHolder 개념이 자주 나오는데 dt

그에 대한 설명이 책에 없어서 따로 정리하고자 글을 쓴다.

**RecyclerView는 Adapter Pattern을 통해 리스트 형태로 표시할 데이터와 리스트 아이템 각각의 레이아웃을** 
**연결하는 방식이다.**

자신만의 어댑터를 만들 때 **RecyclerView.Adapter**를 상속하는데 이 때 **RecyclerView.ViewHolder**를 넘겨 줘야 하고

**OnCreateViewHolder(), onBindViewHolder()** 등의 추상 메소드를 반드시 구현해줘야 한다.

<br>

> <a href="https://sey2.github.io/posts/RecyclerView/#recycler-view%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%B4-%EB%A6%AC%EC%8A%A4%ED%8A%B8-%EB%AA%A8%EC%96%91%EC%9C%BC%EB%A1%9C-%EB%B3%B4%EC%97%AC%EC%A3%BC%EA%B8%B0">어댑터를 만드는 과정은 이 링크의 PersonAdapter.java를 참조</a>

---

### 필요한 이유?

RecyclerView 이전에 그의 조상인 ListView가 있었다고 한다.

ListView에서 특정 데이터들을 리스트 형태로 보여주는 원리는 아래와 같다.

데이터 각각에 대해 아이템의 레이아웃을 구성하는 View 를  `inflate`  하고,  `Inflating`  된 뷰에서  `findViewById()`를 통해 데이터를 끼워 맞춰주면서 리스트 형태로 만들어준다.

하지만 뷰를 매번 인플레이팅하는 작업은 무거운 작업이었기 때문에 매끄로운 스크롤을 보장하지 못했다.

따라서 getView() 메소드 내의 convertView라는 녀석을 활용하여, 스크롤이 내려가면서 

맨 위에 있던 아이템들은 화면에서 사라지고 

다른 새로운 아이템을 구성해야할 때 뷰를 새롭게 인플레이팅하기보다,

 **기존에 사용하던 View 를 다시 갖다 쓰는 방법**을 사용하곤 했다.  즉, **재활용성을 강조**한 것이다.

이렇게 재활용성을 높였지만, 뷰를 구성하기 위한 findViewById() 호출 역시 매우 많은 비용이 드는 작업이기에

데이터의 개수가 늘어나는 만큼 성능 저하가 발생하게 된다.

---

### findViewById()이 고비용 작업인 이유

일반 뷰 (TextView, ImageView 등 단일 뷰 자체)에 대한 findViewById()는 자기 자신의 ID만 확인하기 때문에 
비용이 클 이유가 없다.
<br>

**findViewById() 내부 구현**
```java
@Nullable
public final <T extends View> T findViewById(@IdRes int id) {
  if (id == NO_ID) {
    return null;
  }
  return findViewTraversal(id);
}

protected <T extends View> T findViewTraversal(@IdRes int id) {
  if (id == mID) {
    return (T) this;
  }
  return null;
}
```

**하지만 여러 개의 자식 뷰를 포함하고 있는 레이아웃은, 자식들까지 모두 확인하는 과정이 필요하다.**

**이 과정은 DFS 탐색과 같은데 매번 자식 뷰를 모두 확인해와서 비용이 크게 발생할 수 밖에 없다.**

```java
@Override
protected <T extends View> T findViewTraversal(@IdRes int id) {
    if (id == mID) {
        return (T) this;
    }

    final View[] where = mChildren;
    final int len = mChildrenCount;

    for (int i = 0; i < len; i++) {
        View v = where[i];

        if ((v.mPrivateFlags & PFLAG_IS_ROOT_NAMESPACE) == 0) {
            v = v.findViewById(id);

            if (v != null) {
                return (T) v;
            }
        }
    }

    return null;
}
```

**따라서 findViewById()의 호출 횟수를 줄이기 위해 탄생하게 된 것이 바로 ViewHolder Pattern이다.**

---

### ViewHolder Pattern 개념

**findViewById()를 반복적으로 호출하는 것을 막기 위해 ViewHolder라는 디자인 패턴이 등장했다.**

> ViewHolder 패턴은, **각 뷰의 객체를 ViewHolder 에 보관함**으로써 뷰의 내용을 업데이트하기 위한 **`findViewById()`  메소드 호출을 줄여** 효과적으로 **성능 개선**을 할 수 있는 패턴이다. 
> 
> ViewHolder 패턴을 사용하면, **한 번 생성하여 저장했던 뷰는 다시  `findViewById()`  를 통해
>  뷰를 불러올 필요가 사라지게 된다.**

<br>


만약 데이터를 1번부터 10번까지 리스트 형태로 보여줄 때,

 스마트폰의 화면의 크기 때문에 5개까지 밖에 못 보여준다고 가정해보자.

사용자가 스크롤을 하게 되면, 최상단에 있던 1번이 눈에 보이지 않게 될 것이다.

그와 동시에 6번이 아이템이 화면에 새롭게 보인다. 자 ! 그러면 **findViewById()를 일일히 호출하여서**

**레이아웃에 데이터를 바인딩하지 않고, 기존 1번 아이템을 그려줄 때 사용했던 View를 재사용하여**

**이미 불러왔던 레이아웃에 데이터만 채워주는 것이다.** 이렇게 하면 재사용성을 높이고 

불필요한 비용을 줄이게된다. 

하지만 ListView에서는 일일히 ViewHolder 생성 코드를 직접 작성해줘야 하는 단점이 있었다.

**이러한 단점을 보완하기 위해 프레임워크 차원에서 강제로 ViewHolder 패턴을 구현하도록 해줘야하는**

**RecyclerView라는게 탄생 했다.**

--- 

### RecyclerView 에서의 ViewHolder

`RecyclerView.Adapter` 를 상속하여 어댑터를 만들 때, `onCreateViewHolder()` 과 `onBindVIewHolder()`, `getItemCount()` 이렇게 세 추상 메소드를 반드시 구현해줘야 한다.

---
### onCreateViewHolder()

**ViewHolder 를 새로 만들어야 할 때 호출**되는 메소드로, 

이를 통해  **각 아이템을 위한 XML 레이아웃을 활용한 뷰 객체를 생성하고 이를 뷰 홀더 객체에 담아 리턴**해준다. 

다만 ViewHolder 가 아직 어떠한 데이터에 바인딩된 상태가 아니기 때문에

  **각 뷰의 내용 (`TextView`  의 Text 등) 은 채우지 않는다**.

뷰의 내용을 채워야 하는 경우 (해당 아이템이 화면에 보여지는 경우)

 아래 메소드에서 레이아웃의 내용들을 채우게 된다.

---

### onBindViewHolder()

ViewHolder 를  **어떠한 데이터와 연결할 때 호출되는 메소드로,** 

이를 통해  **뷰 홀더 객체들의 레이아웃을 채우게**  된다.  

`position`  이라는 파라미터를 활용하여  **데이터의 순서에 맞게 아이템 레이아웃을 바인딩**해줄 수 있다.

---

### getItemCount()

뷰 홀더가 관리하는 아이템의 개수를 반환하는 메소드이다. 