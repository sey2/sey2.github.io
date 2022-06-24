---
title: "프로그래머스 두 개 뽑아서 더하기"
author: seyoung
date: '2022-06-25 02:35:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 두 개 뽑아서 더하기

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175613311-1618bda5-8f29-43ba-8fa4-0ecc31eabd42.PNG">




**풀이**

 - **소요 시간 약 5분**
 
 
내가 푼 풀이 

```java
import java.util.*;

class Solution {
    public int[] solution(int[] numbers) {
        Arrays.sort(numbers);
        ArrayList<Integer> list = new ArrayList<>();
        
        for(int i=0; i<numbers.length-1; i++){
            for(int j=i+1; j<numbers.length; j++){
                int sum = numbers[i] + numbers[j];
                
                if(!list.contains(sum)) list.add(sum);
            }
        }
        
        return list.stream().sorted().mapToInt(Integer::intValue).toArray();
    }
}
```
