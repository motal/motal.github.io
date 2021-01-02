---
title: "정규표현식 With Golang"
date: 2021-01-02T11:27:30+09:00
draft: false
tags: [
    "go",
    "golang",
    "sw",
    "regex"
]
---

예제코드
~~~
package main

import (
	"fmt"
	"regexp"
)

func main() {

	match, _ := regexp.MatchString("p[a-z]+ch", "peach")
	fmt.Println(match)

	r, _ := regexp.Compile("p([a-z]+)ch")
	fmt.Println(r.MatchString("peach"))
	fmt.Println(r.FindString("peach"))
	fmt.Println(r.FindStringIndex("hel peach"))
	fmt.Println(r.FindStringSubmatch("punch"))
	fmt.Println(r.FindAllString("peach punch pinch", -1))
	fmt.Println(r.ReplaceAllString("peach po pinch", "xxx"))
}
~~~

결과
~~~
true
true
peach
[4 9]
[punch un]
[peach punch pinch]
xxx po xxx
~~~