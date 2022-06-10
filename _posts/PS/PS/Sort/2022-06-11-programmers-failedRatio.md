---
title: "프로그래머스 실패율"
author: seyoung
date: '2022-06-11 03:52:23 +0900'
categories: PS Sort
tags: [programmers,sort,level1]
math: true
mermaid: true
---


## 프로그래머스 실패율

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/173131648-3967d17b-d45c-4546-934c-0f5f5420ebc3.PNG">


**풀이**

 - **개인적으로 지금까지 푼 프로그래머스 문제 중 제일 오래 걸렸다**
 - **예외 잡느라 -_-... 정렬은 많이 약하구나 느꼈고 정렬도 많이 연습해야 겠다**
 - **소요 시간 약 1시간 30분**
 

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

class Solution {
    public static int[] solution(int N, int[] stages) {
        int[] answer = new int[N];

        Arrays.sort(stages);

        Map<Integer, Double> stageRatio = new HashMap<>();


        for(int i=1; i<=N; i++){
            int curStageNum = 0;
            int noClear = 0;

            if(i > stages[stages.length-1] )
                stageRatio.put(i, 0.0);
             else{
                for (int j = 0; j < stages.length; j++) {
                    if (stages[j] >= i) curStageNum++;

                    if (stages[j] == i && stages[j] >= i) noClear++;
                }
                stageRatio.put(i, (double) noClear / (double) curStageNum);
            }
        }

        List<Integer> keySetList = new ArrayList<>(stageRatio.keySet());
        Collections.sort(keySetList, (o1, o2) -> (stageRatio.get(o2).compareTo(stageRatio.get(o1))));

        int cnt  = 0;
        for(var a: keySetList)
            answer[cnt++] = a;


        return answer;
    }
}
```



