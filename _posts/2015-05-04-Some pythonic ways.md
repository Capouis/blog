---
layout: default
title: Some pythonic methods in python
comments: true
---

![fuck](/blog/images/C++.png)

#所谓的pythonic是指用优美的方式写python， 而不是写出C或者Java的风格出来。 在python交互shell里面输入`import this`之后， 就会出现一些很经典的python设计哲学， 其实不光是python， 这几点对于整个软件工程都是适用的。  我总结了一下自己遇到的一些pythonic的例子， 本文也将持续更新。

* 2015/5/4
* 1 关于快速排序， 仿造C++的快速排序写法可以写出风格比较相近的python code:
```
void foo(int s, int e){
	if(s >= e) return;
	int fst = a[s];
	int i = s, j = e;
	while(i < j){
		while(i < j && a[j] >= fst) --j;
		a[i] = a[j];
		while(i < j && a[i] <= fst) ++i;
		a[j] = a[i];
	}
	a[i] = fst;
	if(i > s) foo(s, i-1);
	if(i < e) foo(i+1, e);
} 
```
但是很容易写的不够pythonic， 比较pythonic的写法是：
```
def quicksort(array):
    less = []
    greater = []

    if len(array) <= 1:
        return array
    p = array.pop()
    for x in array:
        if x <= p: less.append(x)
        else: greater.append(x)
    return quicksort(less) + [p] + quicksort(greater)
```

