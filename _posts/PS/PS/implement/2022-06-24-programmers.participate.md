---
title: "프로그래머스 완주하지 못한 선수"
author: seyoung
date: '2022-06-24 20:28:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 완주하지 못한 선수

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175525960-c7b2ae18-be0e-44c9-b862-2271e34201f7.PNG">




**풀이**

 - **처음에 ArrayList로 담아서 contains로 접근헀지만 몇몇 케이스가 시간 초과가 나와 HashMap으로 다시 풀었다.**

 - **소요 시간 약 20분**
 
 
내가 푼 풀이 

```java
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        HashMap<String, Integer> hash = new HashMap<>();
        String answer = "";
        
        for(String name : participant) hash.put(name, hash.getOrDefault(name,0) + 1);
        for(String name : completion) hash.put(name, hash.get(name)-1);
        
        for(String name : hash.keySet()){
            if(hash.get(name) != 0)
                answer = name;
        }
        

        return answer;
    }
}
```
