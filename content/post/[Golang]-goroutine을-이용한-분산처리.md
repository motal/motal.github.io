---
title: "[Golang] Goroutine을 이용한 분산처리"
date: 2020-11-29T10:25:19+09:00
draft: false
tags: [
    "go",
    "golang",
    "development",
]
---

피보나치를 일꾼 4명에게 동시에 계산하도록 만들어보자.   

### 설명 

- worker는 일꾼이고 모두 goroutine으로 실행된다.   
- jobs와 results는 각각 작업들과 결과물들을 전달하는 buffered 채널이다.      
- worker 내부에는 jobs 채널로 부터 일을 계속 수신하고 그 결과는 results 채널로 전달한다.   
- jobs 채널에 0~99까지의 작업을 전달한다.
- jobs에는 총 100개의 작업을 전달하고 jobs 채널을 종료(close)한다.   
- 메인 스레드는 results 채널에서 오는 결과를 기다리고 있다.   


### 코드   
~~~
func main() {
	jobs := make(chan int, 100)
	results := make(chan string, 100)

	for i := 0; i < 100; i++ {
		jobs <- i
	}
	close(jobs)

	go worker(1, jobs, results)
	go worker(2, jobs, results)
	go worker(3, jobs, results)
	go worker(4, jobs, results)

	for i := 0; i < 100; i++ {
		fmt.Println(<-results)
	}
}

func worker(num int, jobs <-chan int, results chan<- string) {
	for n := range jobs {
		results <- fmt.Sprintf("%d번 goroutine - %d", num, fibonacci(n))
	}
}

func fibonacci(n int) int {
	if n < 0 {
		return 0
	}
	if n == 1 {
		return 1
	}
	return fibonacci(n-1) + fibonacci(n-2)
}

~~~

### 결과

1~4번 goroutine 들이 번갈아가면서 피보나치의 결과를 보내고 있는 것을 볼 수 있다.   

~~~
2번 goroutine - 267914296
4번 goroutine - 433494437
3번 goroutine - 701408733
1번 goroutine - 1134903170
2번 goroutine - 1836311903
4번 goroutine - 2971215073
3번 goroutine - 4807526976
~~~   

### CPU 사용량

와우!    
400%에 근접해 있는 것을 볼 수 있는데 이는 worker가 4명이기 때문이다.   
만약 worker의 수를 3개로 줄이면 300%가 된다.   
이런 패턴을 이용하면 cpu core 수를 고려한 최적의 동시성 처리가 가능할 것 같다.      

![](/img/fibonacci-distribute-cpu.png)

