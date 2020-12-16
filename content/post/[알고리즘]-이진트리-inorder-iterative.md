---
title: "[알고리즘] 이진트리 Inorder Iterative"
date: 2020-12-16T20:15:23+09:00
draft: false
tags: [
    "알고리즘",
    "이진트리",
    "반복"
]
---

이런 건 그냥 외워야하나 싶다.

~~~
type TreeNode struct {
	Val   int
	Left  *TreeNode
	Right *TreeNode
}

func inorder(root *TreeNode) {
    var st []*TreeNode // 처음 stack에 담지 않는다
    
    for root != nil || len(st) > 0 {
        for root != nil {
            st = append(st, root)
            root = root.Left
        }
        root = st[len(st)-1]
        st = st[:len(st)-1] // pop
        
        fmt.Println(root.Val)

        root = root.Right
    }
}
~~~