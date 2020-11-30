---
title: "[Golang] Stack과 Queue"
date: 2020-11-28T22:58:47+09:00
draft: false
tags: [
    "go",
    "golang",
    "sw",
]
---

Golang에서 Stack과 Queue 자료구조를 사용해보자.

다행히 Slice만으로도 두 가지 용도로 사용할 수 있다.

---
## Stack
코드

	// push
	stack := []int{1, 2, 3}
	stack = append(stack, 4)
	stack = append(stack, 5, 6)

	fmt.Println(stack)

	// pop
	val, stack := stack[len(stack)-1], stack[:len(stack)-1]

	fmt.Println(stack, val)

결과

    [1 2 3 4 5 6]
    [1 2 3 4 5] 6


## Queue
코드

	// enqueue
	queue := []int{1, 2, 3}
	queue = append(queue, 4)

	fmt.Println(queue)

	// dequeue
	val, queue2 := queue[0], queue[1:]

	fmt.Println(val)
	fmt.Println(queue2)
결과

    [1 2 3 4]
    1
    [2 3 4]


## Queue (channel을 이용)
코드

	// enqueue
	queue := make(chan int, 1024)
	queue <- 1
	queue <- 2
	queue <- 3

	// dequeue
	fmt.Println(<-queue)
	fmt.Println(<-queue)
	fmt.Println(<-queue)
결과

    1
    2
    3

