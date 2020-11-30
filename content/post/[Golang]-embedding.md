---
title: "[Golang] Embedding"
date: 2020-11-30T11:07:30+09:00
draft: false
tags: [
    "go",
    "golang",
    "sw",
]
---

임베딩(Embedding)에 대해 알아보자.   
자바의 상속과는 달리, Golang은 임베딩을 지원한다.   
Effective java를 보면 '상속 대신 조합(composition)을 하라'라고 하는데 여기의 조합과 임베딩은 유사한 개념이다.   

- 참고로 상속은 부모-자식간 강한 커플링을 맺게 되므로 좋지 않다.
- 조합은 내부 구성품에 접근하기 위한 getter/setter가 필요하지만 임베딩은 이조차 필요없이 흡수(?)가 된다는 면에서 차이가 있다.

interface와 struct 모두 임베딩이 가능하다. 단,  
1. interface는 interface만 포함할 수 있다.   
2. struct는 struct과 interface 둘 다 포함할 수 있다.   

임베딩을 하게 되면 안쪽에 있는 메소드를 바깥쪽에서 마음데로 가져다 쓸 수 있다.   
아래 코드에서 A는 마치 자기가 B인 마냥 동작한다. (물론 타입이 B는 아니다)  
그 이유는 A가 B를 임베딩 했기 때문이다.   
(드래곤볼에서 마인부우가 다른 녀석들을 흡수 한 뒤 흡수한 녀석들의 기술을 쓰는 것을 떠올려보자!)

~~~
type A struct {
	B
}

type B struct{}

func (b B) call() {
	fmt.Println("Hi~ it's B!")
}

func main() {
	a := A{}
	a.call()
}
~~~
결과
~~~
Hi~ it's B!
~~~

만약 중복 메소드가 존재하는 경우에는 아래와 같은 규칙을 따른다.
1. 바깥쪽에 있는 interface(혹은 struct)의 메소드를 우선적으로 호출한다.
2. 만약 같은 레벨에 여러개가 있는 경우에는 에러가 발생시킨다. 이를 피하기 위해서는 해당 인터페이스 이름을 넣고 호출한다.

~~~
type A struct {
	B
}

func (a A) call() {
	fmt.Println("it's A~~")
}

type B struct{}

func (b B) call() {
	fmt.Println("Hi~ it's B!")
}

func main() {
	a := A{}
	a.call()
}
~~~
결과
~~~
it's A~~
~~~
자바의 메소드 오버라이딩을 생각해보면 쉽다.   
concreate 클래스가 메소드를 오버라이딩하면 concreate 클래스의 메소드가 호출 되는 것과 같은 이치이다.   

동일한 레벨에 있는 경우는 컴파일 에러가 발생한다.   
그 이유는 누구꺼의 메소드를 호출하는건지 애매하기 때문이다.   
~~~
type A struct {
	B
	C
}

type B struct{}

func (b B) call() {
	fmt.Println("Hi~ it's B!")
}

type C struct{}

func (c C) call() {
	fmt.Println("Hi~ it's C!")
}

func main() {
	a := A{}
	a.call() // compile error
}
~~~

이런 경우, 명시적으로 어느 interface의 메소드인지를 알려줘야 한다.   
~~~
func main() {
	a := A{}
	a.C.call() // ok
}
~~~

struct와 interface가 모두 임베딩된 경우에는 interface에 해당하는 concrete struct를 넣고 초기화 시켜야 한다.   
아래 예제는 Logger의 구현체를 Job 생성 시 주입하였다.   

~~~
type Job struct {
	Command string
	*log.Logger
}
func main() {
	job := &Job{"job", log.New(os.Stdout, "", log.Ldate|log.Ltime)}
	job.Println("hi~")
}
~~~
결과
~~~
2020/11/30 11:14:03 hi~
~~~

와~! Logger를 임베딩 했을 뿐인데, Job은 logger의 모든 인터페이스를 사용할 수 있게 되었다.  
게다가 앞으로 logger의 구현체를 바꾸기만 하면 Job은 아무런 변경없이도 새로운 Logger기능을 변경할 수 있는 것이다.   (박수...)


### 레퍼런스
- https://golang.org/doc/effective_go.html#embedding
