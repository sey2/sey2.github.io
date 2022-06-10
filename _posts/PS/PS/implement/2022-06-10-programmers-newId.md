---
title: "프로그래머스 카카오 프렌즈 컬러링 북"
author: seyoung
date: '2022-06-09 23:35:23 +0900'
categories: PS DFS-BFS
tags: [programmers,DFS,BFS,level1]
math: true
mermaid: true
---


## 프로그래머스 카카오 신규 아이디 추천 


<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/173030490-13fbb464-9e77-4cf2-adac-f64da082d1d1.PNG">

```
1단계 new_id의 모든 대문자를 대응되는 소문자로 치환합니다.
2단계 new_id에서 알파벳 소문자, 숫자, 빼기(-), 밑줄(_), 마침표(.)를 제외한 모든 문자를 제거합니다.
3단계 new_id에서 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환합니다.
4단계 new_id에서 마침표(.)가 처음이나 끝에 위치한다면 제거합니다.
5단계 new_id가 빈 문자열이라면, new_id에 "a"를 대입합니다.
6단계 new_id의 길이가 16자 이상이면, new_id의 첫 15개의 문자를 제외한 나머지 문자들을 모두 제거합니다.
     만약 제거 후 마침표(.)가 new_id의 끝에 위치한다면 끝에 위치한 마침표(.) 문자를 제거합니다.
7단계 new_id의 길이가 2자 이하라면, new_id의 마지막 문자를 new_id의 길이가 3이 될 때까지 반복해서 끝에 붙입니다.
```


**풀이**

 - **IDE 환경에서는 StringBuilder의 isEmpty() 메서드가 돌아 가지만 프로그래머스에서는 돌아가지 않아서 length 함수로 문자열이 비어 있는지 확인 하였다.**

 - **소요 시간 약 25분**
 

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

class Solution {
    public String solution(String new_id) {
        new_id = new_id.toLowerCase();
        StringBuilder sbNew_id = new StringBuilder(new_id);

        for(int i=0; i<sbNew_id.length(); i++){
            char curChar = sbNew_id.charAt(i);

            //  new_id에서 알파벳 소문자, 숫자, 빼기(-), 밑줄(_), 마침표(.)를 제외한 모든 문자를 제거합니다.
            if( !(curChar >= 'a' && curChar <='z') && curChar !='-' && curChar != '_' && curChar != '.' && !(curChar >= '0' && curChar <='9')){
                sbNew_id.deleteCharAt(i); i --;
            }
        }

        //  new_id에서 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환합니다.
        for(int i=0; i<sbNew_id.length()-1; i++){
            if(sbNew_id.charAt(i) == '.' && sbNew_id.charAt(i+1) == '.'){
                sbNew_id.deleteCharAt(i); i--;
            }
        }

        // new_id에서 마침표(.)가 처음이나 끝에 위치한다면 제거합니다.
        if(sbNew_id.charAt(0) == '.')
            sbNew_id.deleteCharAt(0);
        else if(sbNew_id.charAt(sbNew_id.length()-1) == '.')
            sbNew_id.deleteCharAt(sbNew_id.length()-1);

        //  new_id에서 마침표(.)가 처음이나 끝에 위치한다면 제거합니다.
        if(sbNew_id.length() == 0)
            sbNew_id.append("a");

        // new_id의 길이가 16자 이상이면, new_id의 첫 15개의 문자를 제외한 나머지 문자들을 모두 제거합니다.
        // 만약 제거 후 마침표(.)가 new_id의 끝에 위치한다면 끝에 위치한 마침표(.) 문자를 제거합니다
        if(sbNew_id.length() >= 16)
            sbNew_id = sbNew_id.delete(15,sbNew_id.length()+1);

        if(sbNew_id.charAt(sbNew_id.length()-1) == '.' )  sbNew_id.deleteCharAt(sbNew_id.length()-1);

       //new_id의 길이가 2자 이하라면, new_id의 마지막 문자를 new_id의 길이가 3이 될 때까지 반복해서 끝에 붙입니다.

        if(sbNew_id.length() <= 2){
            char last = sbNew_id.charAt(sbNew_id.length()-1);
            for(int i=sbNew_id.length(); i<3; i++){
                sbNew_id.append(last);
            }
        }

        String answer = sbNew_id.toString();
        return answer;
    }
}
```



