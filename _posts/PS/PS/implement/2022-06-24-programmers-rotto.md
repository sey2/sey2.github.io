---
title: "프로그래머스 로또의 최고 순위와 최저 순위"
author: seyoung
date: '2022-06-24 14:04:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 로또의 최고 순위와 최저 순위


<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175466074-5d6afe1f-acaf-4171-9305-814b81101359.PNG">




**풀이**

 - **간단한 구현 문제**

 - **소요 시간 약 5분**
 
 
내가 푼 풀이 

```java
import java.util.*;

class Solution {
    public int[] solution(int[] lottos, int[] win_nums) {
        int correct = 0;
        int zeroCnt = 0; 
        
        for(int i=0; i<win_nums.length; i++){
            for(int j=0; j<lottos.length; j++){
                if(lottos[j] == 0 && i == 0){
                    zeroCnt++;
                    continue;
                }else if(lottos[j] == win_nums[i]) correct++;
            }
        }
        
        
        int[] answer = new int[2];
        
        int min = (7-correct >=6)? 6 : 7-correct;
        answer[1] = min;
        
        correct += zeroCnt;
        int max = (7-correct >= 6)? 6 : 7-correct; 

        answer[0] = max;
        
        return answer;
    }
    
}
```

