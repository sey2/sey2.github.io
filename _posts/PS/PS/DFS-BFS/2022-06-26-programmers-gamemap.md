---
title: "프로그래머스 게임 맵 최단거리"
author: seyoung
date: '2022-06-26 20:02:23 +0900'
categories: PS implements
tags: [programmers,implements,level2]
math: true
mermaid: true
---


## 프로그래머스 게임 맵 최단거리

<img width="1230" alt="" src="https://user-images.githubusercontent.com/54762273/175811125-7a64f470-f5a9-4357-b1a4-0cab64703b1e.PNG">

## 풀이
 - 시작점을 기준으로 bfs 돌려서 큐에 넣을 때마다 거리 + 1씩 해줘서 넣어주면 끝 !
 - 풀이 시간 25분 ide 사용 x

 
 
내가 푼 풀이

```java
import java.util.*;
class Pos{
    int x;
    int y;
    
    public Pos(int y, int x) { this.y = y; this.x = x;}
}

class Solution {
    static int dx[] = {0, 0, -1, 1};
    static int dy[] = {-1, 1, 0, 0};
    static int visited[][];
    
    public int solution(int[][] maps) {
            
        return bfs(maps,new Pos(0,0));
    }
    
    public static int bfs(int[][] maps, Pos start){
        Queue<Pos> queue = new LinkedList<>();
        queue.add(start);
        
        visited = new int[maps.length][maps[0].length];
        visited[start.y][start.x] = 1;
        
        while(!queue.isEmpty()){
            Pos cur = queue.poll();
            
            for(int i=0; i<4; i++){
                int nextY = cur.y + dy[i];
                int nextX = cur.x + dx[i];    
                
                if(nextX >=0 && nextX < maps[0].length && nextY >=0 && nextY < maps.length){
                    if(visited[nextY][nextX] == 0 && maps[nextY][nextX]==1){
                        visited[nextY][nextX] = visited[cur.y][cur.x] + 1;
                        queue.add(new Pos(nextY, nextX));
                    }
                }
            }
        }
        
        return (visited[visited.length-1][visited[0].length-1] == 0) ? -1 : visited[visited.length-1][visited[0].length-1];
    }
}
```

