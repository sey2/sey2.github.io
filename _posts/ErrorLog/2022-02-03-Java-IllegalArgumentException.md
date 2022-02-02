---
title: "자바 런타임 에러(IllegalArgumentException)"
author: seyoung
date: '2022-02-03 04:49:00 +0900'
categories: 삽질_일기
tags: [git, runtime, IllegalArgumentException]
math: true
mermaid: true
---


## 자바 런타임 에러(IllegalArgumentException)


![1](https://user-images.githubusercontent.com/54762273/152225585-bffb4871-4ddb-4e55-8942-aef33b6b5b2f.PNG)

백준 문제 풀다 제출을 해보니 런타임 에러가 나왔다.

eclips에서 돌렸을 때는 문제가 없었는데 ...

무슨 문제인가 보니 Compareable 값을 비교할 때 어떻게 정렬 할지 판단하기 모호하다는 뜻이다.

**문제의 코드**

```java
	@Override
	public int compareTo(Jewel other) {
		return (other.price - this.price);
	}
```

아래와 같이 <= 로 수정해주면 된다.

```java
	@Override
	public int compareTo(Jewel other) {
		return (other.price >= this.price);
	}
```

**Compareable 쓸 때 조심해야 할듯 !**