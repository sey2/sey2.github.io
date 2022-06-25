---
title: "프로그래머스 정수 내림차순으로 배치하기"
author: seyoung
date: '2022-06-25 06:44:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 정수 내림차순으로 배치하기

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175768138-ebfb4d6f-4f18-4205-9827-3b40fc840c31.PNG">




**풀이**

 - **소요 시간 약 5분**
 
 
내가 푼 풀이

```java
import java.util.*;

class Solution {
    public long solution(long n) {
        String str = Long.toString(n);
        PriorityQueue<String> pq = new PriorityQueue<>(Collections.reverseOrder());
        
        for(int i=0; i<str.length(); i++) pq.add(Integer.toString(str.charAt(i)-48));
        
        String ans ="";
        while(!pq.isEmpty()) ans+= pq.poll(); 
        
        return Long.parseLong(ans);
    }
}
```

