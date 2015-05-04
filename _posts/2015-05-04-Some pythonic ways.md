---
layout: default
title: Some pythonic methods in python
comments: true
---

![fuck](/blog/images/C++.png)

####所谓的pythonic是指用优美的方式写python， 而不是写出C或者Java的风格出来。 在python交互shell里面输入`import this`之后， 就会出现一些很经典的python设计哲学， 其实不光是python， 这几点对于整个软件工程都是适用的。  我总结了一下自己遇到的一些pythonic的例子， 本文也将持续更新。

* 2015/5/4

1 关于快速排序，仿造C++的快速排序写法可以写出风格比较相近的python code:

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


<br/>
<br/>
2 交换两个数

在其他语言中， 交换两个数最常见的做法是加入一个中间值作为一个临时仓库（当然可以通过^避免中间变量：a ^= b, b ^= a, a ^= b），
而在python中一切都变得简单了：
a, b = b, a 就可以交换两个数的值， 原因在于后面的b, a可以看成一个tuple,  tuple是不可变类型。



3 字符串连接的问题
 
  python字符串效率问题之一就是在连接字符串的时候使用‘+’号，但是使用+号的话，python需要申请N-1次内存空间，然后进行字符串拷贝。 原因是字符串对象PyStringObject在python当中是不可变对象，所以每当需要合并两个字符串的时候，就要重新申请一个新的内存空间（大小为两个字符串长度之和）来给这个合并之后的新字符串, 然后进行拷贝, 所以用+号效率非常低。
  
  建议在连接字符串的时候使用字符串本身的方法join，这个方法能提高效率，原因是它只是申请了一次内存空间，因为它可以遍历list中的元素计算出总共需要申请的内存空间的大小，一次申请完。

```
#not pythonic 
list = ['a', 'b', 'c']  
ans = ''  
for i in list:  
    ans += i  
  
#pythonic  
list = ['a', 'b', 'c']  
ans = ''.join(list) 
```
