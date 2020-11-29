---
title: "[Golang] 포인터 리시버 vs 밸류 리시버"
date: 2020-11-26T09:38:28+09:00
draft: false
tags: [
    "go",
    "golang",
    "development",
]
---

Value receiver와 Pointer receiver, 2가지가 receiver가 있다.

아래와 같은 구조체가 있다고 가정하고
```
type Person struct {
	Name string
}
```

Value receiver의 예
```
func (p Person) String() string {
    return p.Name + "!!!"
}
```
Pointer receiver의 예
```
func (p *Person) String() string {
    return p.Name + "!!!"
}
```


두 가지 receiver를 언제 어떻게 사용해야하는지 헷갈려서 공부한 내용을 기록한다.

---

우선 아래의 원칙을 알아야 한다.

### 원칙1. 둘 중에 한 가지만 정의할 수 있다.
- 동일한 메소드를 value와 pointer receiver 형태 두 가지 다 정의하면 컴파일 에러가 난다.
- 즉, value 를 쓸지 pointer 를 쓸지 정해야한다. (어떻게 정하는지는 맨 아래에 설명)

### 원칙2. Value receiver는 value와 pointer 모두 받을 수 있다.
- 이유는 pointer 변수 p를 넘기는 경우 컴파일러가 자동으로 (*p)와 같이 변환해주기 때문이다. 일종의 편의기능.

### 원칙3. Pointer receiver는 pointer만 받을 수 있다.
- pointer가 아닌 value를 넘기는 행위는 value는 immutable을 전제로하는 것인데, 만약 컴파일러가 (&p)를 자동으로 만들어주면 pointer receiver에 의해 값이 변경될 수도 있다. 그러므로 pointer 전달 시, value receiver는 호출되는 것을 막는 것은 당연한 것이다.

거꾸로 호출하는 입장에서 생각해보면,
- value 변수는 value receiver 메소드만 호출 가능하다. (v.method() => (&v).method() 는 불가능)
- pointer 변수는 value와 pointer receiver 메소드 둘 다 호출 가능하다. (p.method() => (*p).method() 로 변환 가능)

### 사용 방법
- 수정이 필요없다 => Value receiver 를 만든다.
- 수정이 필요하다 => Pointer receiver 를 만든다.
- 수정은 필요없는데 copy할게 너무 많다 => Pointer receiver를 쓰고 항상 pointer를 넘긴다.
- Map, Slice는 Value receiver 를 사용한다. 
