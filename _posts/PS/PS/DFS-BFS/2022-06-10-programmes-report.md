---
title: "프로그래머스 신고 결과 받기"
author: seyoung
date: '2022-06-09 23:35:23 +0900'
categories: PS implements
tags: [programmers, level1]
math: true
mermaid: true
---


## 프로그래머스 신고 결과 받기


<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/172909920-ae98f898-59ba-4377-9786-7857102a6acf.PNG">


**풀이**

 - **소요 시간 약 30분**
 

```java


class Solution {
    public static int[] solution(String[] id_list, String[] report, int k) {
        HashMap<String, Integer> map = new HashMap<>();  // 신고당한 횟수
        String stopUser [] = new String[id_list.length];        // 정지 당해야할 유저
        HashMap<String, ArrayList<String>> user_rpList = new HashMap<>();
        int cnt = 0;


        int[] answer = new int[id_list.length];

        // 유저별 신고 당한 횟수 담는 맵 초기화, 유저가 신고한 아이디 리스트 초기화
        for(var id : id_list) {
            map.put(id, 0);
            user_rpList.put(id, new ArrayList<>());
        }

        // 유저별 신고 당한 횟수
        Loop:
        for(int i=0; i < report.length; i++){
            boolean check = false;
             StringTokenizer st = new StringTokenizer(report[i]," ");
             String user = st.nextToken();
             String rp_user = st.nextToken();

             var tmp = user_rpList.get(user);   // 신고한 유저의 신고 목록 ArrayList


             for(var name: tmp) {
                 if(name.equals(rp_user))       // 만약 이미 신고를 했었으면 목록에 추가하지 않고 건너뛰기
                      continue Loop;
             }

             user_rpList.get(user).add(rp_user);
             map.replace(rp_user, map.get(rp_user)+1);



        }

        // 신고 당한 횟수가 정지 기준을 넘으면 정지처리
        for(int i=0; i<id_list.length; i++) {
            if (map.get(id_list[i]) >= k)
                stopUser[cnt++] = id_list[i];
        }

        // 자기가 신고한 게정이 몇개 정지 되었는지 개수를 샘
        for(int i=0; i< id_list.length; i++){
            var tmp = user_rpList.get(id_list[i]);

            for(var name : tmp){
                for(int j=0; j< stopUser.length; j++){
                    if(name.equals(stopUser[j])){
                        answer[i]++;
                    }
                }
            }
        }


        return answer;
    }
}

```



