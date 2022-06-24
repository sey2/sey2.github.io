---
title: "프로그래머스 콜라츠 추측"
author: seyoung
date: '2022-06-25 02:20:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 콜라츠 추측

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175611248-351a2d91-593e-44b5-9dbe-7a6b698b2b6e.PNG">




**풀이**

 - **아래 풀이 말고도 재귀로도 풀 수 있을 것 같았지만 속도 차원에서 아래와 같이 해결하였다.**

 - **소요 시간 약 5분**
 
 
내가 푼 풀이 

```java
class Solution {
    public int solution(int num) {
        int answer = 0;
        long num1 = num;

        for(answer=0; (num1!=1 && answer <500); answer++)
            num1 = (num1 % 2 == 0) ? (num1 / 2) : (num1*3) + 1;

        return (answer==500) ? -1 : answer;
    }
}
```
