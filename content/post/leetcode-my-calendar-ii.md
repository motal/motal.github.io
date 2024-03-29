---
title: "Leetcode - My Calendar II"
date: 2021-08-22T12:35:08+09:00
draft: false
---

## 문제 내용 
https://leetcode.com/problems/my-calendar-ii/  
스케줄이 동시에 3개가 겹치는지를 판단해야하는 문제

## 솔루션
### 접근1
종료 시간을 기준으로 정렬을 해서 바로 앞과 앞앞을 비교해면 되지 않을까 생각해봄.  
경우의 수가 많아져서 이 방법은 아니다라고 판단.  

### 접근2
힌트를 읽어봄.  
음 스케줄과 겹친 스케줄, 2개를 각각 보관하는 방법.  
2개의 스케줄이 겹치냐를 판단하는 코드가 헷갈림 (-_- 미치겠다)
~~~
if (a.start < b.end && b.start < a.end) { // 겹친다 }
~~~
위 코드를 이해하는데 한참 생각했다.

### 접근3
솔루션 읽고 한참 생각했다.  
시작 시간에는 +1을 종료 시간에는 -1을 더한다.  
앞 시간에서 부터 더해나가면서 3 이상이 되는 경우, 롤백(-1, +1)을 해주고 return false.  
문제는, 시간의 범위가 매우 크므로 배열을 사용할 수가 없기 때문에 TreeMap을 이용하였다.  
접근2와 시간 복잡도는 똑같지만 3개가 아닌 N개가 겹치는 경우를 처리할 수 있다.