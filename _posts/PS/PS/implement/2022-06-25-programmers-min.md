---
title: "프로그래머스 제일 작은 수 제거하기"
author: seyoung
date: '2022-06-25 02:35:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 제일 작은 수 제거하기

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175767626-b3497e55-e726-4462-b72f-73b8456bcbd7.PNG">




**풀이**

 - **소요 시간 약 7분**
 
 
내가 푼 2가지 풀이

```java
import java.util.*;

class Solution {
    public static int[] solution(int[] arr) {
        ArrayList<Integer> list = new ArrayList<>();
        PriorityQueue<Integer> queue = new PriorityQueue<Integer>();
        
        for(int num : arr){ list.add(num); queue.add(num); }
        
        list.remove(list.indexOf(queue.poll()));
        
        if(list.size() == 0) list.add(-1);
        
        return list.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

---

```java
class Solution {
    public static int[] solution(int[] arr) {
        int min = arr[0];
        int idx = 0;

        if(arr.length==1) return new int[]{-1};

        int answer[] = new int[arr.length-1];

        for(int i=1; i<arr.length; i++){
            if(arr[i] <= min ){
                min = arr[i];
                idx = i;
            }
        }

        for(int i=0; i<idx; i++) answer[i] = arr[i];
        for(int i=idx+1; i<arr.length; i++) answer[i-1] = arr[i];

        if(answer.length == 0) answer[0] = -1;

        return answer;
    }
}
```
