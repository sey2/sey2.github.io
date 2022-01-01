---
title: "Deploy Error"
author: seyoung
date: '2022-01-01 10:27:00 +0900'
categories: 삽질 일기
tags: [git, error]
math: true
mermaid: true
---

## Deploy Erorr

블로그 테마 적용을 마치고 게시글을 올릴려고 했는데 Automatic build 오류가 났다. <br><br>
그 후 여러가지 변수를 바꾸어 보기도 하고 계속 구글링을 해보았지만 나오는건 없었다. 😥 <br><br>
그래서 차분히 오류 메세지를 보니 Deploy 과정 중 경로를 제대로 읽지 못하는것 같았다. <br><br>
밑에 이미지 중 /&quto; 가 들어가 있는데 따움표가 마지막에 왜 들어가지 곰곰히 생각하다 <br><br>
![error](https://user-images.githubusercontent.com/54762273/147842305-c843ae53-7a6b-4662-be85-f7aa94b96594.jpg) <br>
문제의 파일을 찾기 위해  VsCode로 data-toogle 을 검색하여 post-sharing.html 파일이 문제인 걸 확인하였는데, <br><br>
post-sharing 파일은 변수로 link를 읽어 오므로 해당 파일은 문제가 없는것 같았다. <br><br>
따라서 링크를 던져주는 파일 중 하나가 문제 인데 곰곰히 찾아보니 글을 작성할때 <br><br>
title: 제목 " <- " 콜론이 하나 빠져 있었다. <br><br>
title: "제목" 로 수정해주니 정상적으로 Deploy가 되었다.  


