---
title: "프로그래머스 비밀지도"
author: seyoung
date: '2022-06-23 03:05:23 +0900'
categories: PS implements
tags: [programmers,implements,level1]
math: true
mermaid: true
---


## 프로그래머스 비밀지도 


<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175106917-593e3c80-cd51-40d0-9a33-224802cfcf6c.PNG">




**풀이**

 - **간단한 구현 문제**

 - **소요 시간 약 10분**
 
 - **다른 사람의 풀이를 보니 머리가 띵했다.. 이렇게도 풀 수 있구나 ..**
 
내가 푼 풀이 

```java
import java.io.IOException;
import java.util.ArrayList;

class Solution {
    public static String[] solution(int n, int[] arr1, int[] arr2) {
        ArrayList<String> list1 = new ArrayList<>();
        ArrayList<String> list2 = new ArrayList<>();

        for(int i=0; i<arr1.length; i++){
            list1.add(toBinary(arr1[i], n));
            list2.add(toBinary(arr2[i], n));
        }

        String[] answer = new String[n];

        for(int i=0; i < list1.size(); i++){
            StringBuilder sb = new StringBuilder();
            for(int j=0; j<list1.size(); j++) {

                if (list1.get(i).charAt(j) == '1' || list2.get(i).charAt(j) =='1')
                    sb.append("#");
                else
                    sb.append(" ");
            }
            answer[i] = sb.toString();
        }


        return answer;
    }

    public static String toBinary(int num, int pow){
        StringBuilder sb = new StringBuilder();

        while(num>0){
            sb.insert(0,Integer.toString(num%2));
            num /= 2;
        }

        if(pow > sb.length()){
            for(int i=sb.length(); i< pow; i++)
                sb.insert(0,"0");
        }

        return sb.toString();
    }
}
```

다른 사람 풀이 

```java
class Solution {
  public String[] solution(int n, int[] arr1, int[] arr2) {
        String[] result = new String[n];
        for (int i = 0; i < n; i++) {
            result[i] = Integer.toBinaryString(arr1[i] | arr2[i]);
        }

        for (int i = 0; i < n; i++) {
            result[i] = String.format("%" + n + "s", result[i]);
            result[i] = result[i].replaceAll("1", "#");
            result[i] = result[i].replaceAll("0", " ");
        }

        return result;
    }
}


```