---
title: "프로그래머스 [1차] 다트 게임"
author: seyoung
date: '2022-06-25 06:44:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 1차 다트 게임

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175800921-970ab3ad-15da-4a06-8109-410576cf8fec.PNG">


 
 
내가 푼 풀이

```java
import java.util.*;

class Solution {
    public static int solution(String dartResult) {
        int answer = 0;
        int score = 0;
        int idx = 0;

        ArrayList<Integer> list = new ArrayList<>();

        for(int i=0; i<dartResult.length(); i++){
            char c = dartResult.charAt(i);

            if(c=='1' && dartResult.charAt(i+1) =='0'){
                score = 10;
                i++;
            }
            else if(c >= '0' && c <= '9'){ score = c - 48; }
            else if(c == 'S') { list.add(score); idx++; }
            else if(c == 'D') { list.add((int)Math.pow(score, 2)); idx++; }
            else if(c == 'T') { list.add((int)Math.pow(score, 3)); idx++; }
            else if(c == '*') {
                if(idx > 1)
                    list.set(idx-2,list.get(idx-2)*2);
                list.set(idx-1,list.get(idx-1)*2);
            }else if(c == '#'){ list.set(idx-1,-list.get(idx-1)); }
        }

        for(int num : list)
            answer+=num;


        return answer;
    }
}
```

