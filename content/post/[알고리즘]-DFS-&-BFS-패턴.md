---
title: "[알고리즘] DFS & BFS 패턴"
date: 2020-12-06T10:09:23+09:00
draft: false
tags: [
    "알고리즘",
    "dfs",
    "bfs"
]
---

## DFS
### 재귀
~~~
procedure DFS(G, v) is
    label v as discovered
    for all directed edges from v to w that are in G.adjacentEdges(v) do
        // 넣을 때 방문 확인 및 처리를 한다
        if vertex w is not labeled as discovered then
            recursively call DFS(G, w)
~~~
### 반복
~~~
procedure DFS_iterative(G, v) is
    let S be a stack
    S.push(v)
    while S is not empty do
        v = S.pop()
        // 꺼낸 다음에 방문 확인 및 처리를 한다
        if v is not labeled as discovered then
            label v as discovered
            for all edges from v to w in G.adjacentEdges(v) do 
                S.push(w)
~~~

## BFS
### 반복
~~~
procedure BFS(G, root) is
    let Q be a queue
    // 초기화 필요
    label root as discovered
    Q.enqueue(root)
    while Q is not empty do
        v = Q.dequeue()
        for all edges from v to w in G.adjacentEdges(v) do
            // 넣을 때 방문 확인 및 처리를 한다
            if w is not labeled as discovered then
                label w as discovered
                Q.enqueue(w)
~~~

## 차이
- DFS는 stack을, BFS는 queue를 사용한다
- 반복문의 경우, DFS는 꺼낸 다음, BFS는 넣기 전에 방문 확인 및 처리를 한다.

## 참고
- https://en.wikipedia.org/wiki/Depth-first_search
- https://en.wikipedia.org/wiki/Breadth-first_search