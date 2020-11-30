---
title: "[Golang] Goroutine과 Channel"
date: 2020-11-28T23:30:43+09:00
draft: false
tags: [
    "go",
    "golang",
    "sw",
]
---
## Goroutine
goroutine은 경량 스레드이다.

(Green thread 혹은 light-weight thread 라고도 부른다)

> green threads are threads that are scheduled by ___a runtime library or virtual machine (VM)___ instead of natively by the underlying operating system (OS). - [Wikipedia](https://en.wikipedia.org/wiki/Green_threads)

## Channel
channel은 데이터를 보내는 통로이다.

자료구조 queue의 목적으로 써도 문제가 없다.

버퍼 사이즈를 명시하지 않으면 사이즈가 0이다.

    ch1 := make(chan int) // 버퍼 사이즈가 0
	ch2 := make(chan int, 1024) // 버퍼 사이즈가 1024


## 동작 방식
처음 goroutine을 사용하다보면 아래와 같은 에러를 자주 보게 된다.

    fatal error: all goroutines are asleep - deadlock!

아래의 원리를 이해하면 더 이상 실수를 하지 않을 수 있다.

**송신하는 쪽은 다음을 확인한다.**

1. channel에 버퍼가 남았냐? 있으면 집어 넣고 넘어간다. (버퍼 사이즈 초과시 에러)
2. 없다면 수신자가 나타날 때까지 대기(block) 한다.

**수신하는 쪽에서는 다음을 확인한다.**

1. 해당 channel 버퍼에 데이터가 들어있니? 있으면 가져온다.
2. 데이터가 들어올 때 까지 대기(block) 한다. (만약 돌고 있는 goroutine 없으면 에러)

## range와 close

range를 쓴는 경우에는 반드시 close(ch)을 해줘야 에러가 안난다.

close(ch)은 해당 channel에 더 이상 데이터가 못들어니 range를 종료해라는 뜻이다.

--- 
## 팩트 체크

- 버퍼가 없는(=버퍼 사이즈가 0인) 경우, 송신하는 쪽에서는 Block이 되는 걸 확인할 수 있다.

코드

	ch := make(chan string)

	go func(ch chan string) {
		fmt.Println("start: ", time.Now().Second())
		ch <- "안녕?"
		fmt.Println("end: ", time.Now().Second())
	}(ch)

	time.Sleep(3 * time.Second)

	fmt.Println(<-ch)

결과

    start:  44
    안녕?
    end:  47

- 수신하는 측에서 3초 까지 block 되었다가 남은 goroutine이 없어 에러를 발생하는 것을 확인 할 수 있다.
~~~
ch := make(chan string)

go func(ch chan string) {
    time.Sleep(3 * time.Second)
}(ch)

fmt.Println(<-ch)
~~~
