---
title: "안드로이드 xml 배경 적용하다 삽질한 건에 대하여"
author: seyoung
date: '2022-02-18 02:53:00 +0900'
categories: 삽질_일기
tags: [xml]
math: true
mermaid: true
---


## 안드로이드  xml 배경 적용 오류

메인 액티비티의 전체 배경색을 바꿀려고 그라데이션 xml 파일을 정성스럽게 만든 후

메인 액티비티의 Constraint Layout 배경색에 적용 시켰는데 아래와 같이 오류가 났다.

![1](https://user-images.githubusercontent.com/54762273/154539344-1bfda867-48ad-4d04-84b4-af98d4f44ee6.PNG)


코드를 잘못 짯나 해서 xml 태그를 계속 바꾸어 가며 2시간 삽질을 하다 진전이 없어 포기를 하였다.

이때 당시에 Layout에는 xml 파일을 적용 시키면 안되는줄 알았다.

그래서 배경색을 이미지 파일로 적용 시키고 다른 작업을 하던 중 우연히 어떤 글을 보았는데 

LinearLayout에 xml 파일로 배경색을 적용하는데 오류가 난다는 글이였다.

그래서 답변에 있는 코드 xml 파일을 복붙해서 LinearLayout에 적용 해보니 잘된다 -_-

뭐지 하면서 내가 작성한 코드랑 다른 점을 살펴보는데 `\<gradient>` 태그 속성에 `android:angle` 값이 

달랐다. ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape>
            <gradient
                android:angle="120"
                android:startColor="#B2EBEA"
                android:endColor="#FCEFE4"
                android:type="linear" />
        </shape>
    </item>
</selector>
```

위는 내가 작성한 코드인데 `android:angle` 값이 당연히 120도가 되는 줄 알았는데 .. 

검색을 해보니 줄 수 있는 값은 0, 90, 180, 270도로 정해져 있었다.

하.. ㅋㅋ 진짜 화가 치밀어 오르네

그래서 아래와 같이 수정한 후

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape>
            <gradient
                android:angle="270"
                android:startColor="#B2EBEA"
                android:endColor="#FCEFE4"
                android:type="linear" />
        </shape>
    </item>
</selector>
```

실행을 해보니 아래와 같이 문제 없이 잘 적용되었다 !!

![1](https://user-images.githubusercontent.com/54762273/154541143-8290214b-1c6f-4acc-b751-d13fe3fd0fbb.PNG)