---
title: "프로그래머스 모의고사"
author: seyoung
date: '2022-06-24 21:26:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 모의고사


<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175535542-a3baa435-dded-4d81-91e8-a1b6ca661042.PNG">




**풀이**

 - **간단한 구현 문제**
 - **ArrayList -> 배열로 전환할 떄 `list.stream().mapToInt(Integer::intValue).toArray();` 사용**
 
 
내가 푼 풀이 

```java
import java.util.*;

class Solution {
    public int[] solution(int[] answers) {
        int one[] = {1, 2, 3, 4, 5};
        int two[] = {2, 1, 2, 3, 2, 4, 2, 5};
        int three[] = {3, 3, 1, 1, 2, 2, 4, 4, 5, 5};
        int count[] = new int[3];
        
        for(int i=0; i<answers.length; i++){
            if(one[i%one.length] == answers[i]) count[0]++;
            if(two[i%two.length] == answers[i]) count[1]++;
            if(three[i%three.length] == answers[i]) count[2]++;
            
        }
    
        Integer arr[] = {count[0],count[1], count[2]};
        Arrays.sort(arr,Collections.reverseOrder());
        
        ArrayList<Integer> list = new ArrayList<>();
        
        for(int i=1; i<=3; i++){
            if(arr[0] == count[i-1])
                list.add(i);
        }
        
    
        return list.stream().mapToInt(Integer::intValue).toArray();
    }
}
```


