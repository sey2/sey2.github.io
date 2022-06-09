---
title: "프로그래머스 카카오 프렌즈 컬러링 북"
author: seyoung
date: '2022-06-09 23:35:23 +0900'
categories: PS DFS-BFS
tags: [programmers,DFS,BFS,level2]
math: true
mermaid: true
---


## 프로그래머스 카카오 프렌즈 컬러링 북


<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/172873881-60f864bc-89a4-4a19-8533-8a990c7f53e3.PNG">


**풀이**

 - **bfs를 이용하여 해결**

 - **소요 시간 약 20분**
 

```java

class Pos{
    public int x;
    public int y;

    public Pos(int y, int x){
        this.y = y;
        this.x = x;
    }

}

class Solution {
    int dx[] = {0, 0, -1, 1};   // 상 하 좌 우
    int dy[] = {-1, 1, 0, 0};
    boolean visited[][];
    PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());

    public int[] solution(int m, int n, int[][] picture) {
        int numberOfArea = 0;
        int maxSizeOfOneArea = 0;
        visited = new boolean[m][n];

        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(visited[i][j] == false && picture[i][j] != 0){
                    numberOfArea++;
                    bfs(i, j, picture[i][j], picture);
                }
            }
        }

        maxSizeOfOneArea = pq.poll();

        int[] answer = new int[2];
        answer[0] = numberOfArea;
        answer[1] = maxSizeOfOneArea;
        return answer;
    }

    public void bfs(int y, int x, int sig, int [][] picture){
        Queue<Pos> queue = new LinkedList<>();
        queue.add(new Pos(y,x));
        visited[y][x] = true;

        int len = 1;

        while(!queue.isEmpty()){
            Pos tmp = queue.poll();
            int curX = tmp.x;
            int curY = tmp.y;

            for(int i=0; i<4; i++){
                int nextY = curY + dy[i];
                int nextX = curX + dx[i];

                if(nextX >= 0 && nextX < picture[0].length && nextY >= 0 && nextY < picture.length){
                    if(picture[nextY][nextX] == sig && visited[nextY][nextX] == false){
                        visited[nextY][nextX] = true;
                        len++;
                        queue.add(new Pos(nextY,nextX));
                    }
                }
            }
        }
        pq.add(len);

    }
}
```



