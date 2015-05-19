---
layout: default
title: Some Details in Different Programming Languages
comments: true
---

###经常容易混淆不同语言之间的语言特性或者内存分配等细节， 不得不记录下来做一些区分

* <font color="red">C++/C# 对象初始化问题</font>

(1)在C++中存在指针的概念， 可以动态分配内存， 或者直接new一个对象， 比如：

```
class instance{
    private:
        int x, y;
    public:
        instance(int a, int b){
        }

        instance(){}
};

int main(){
    instance *c = new instance;
    instance *d = new instance();
    instance *e = new instance(1, 2);
    instance f;
}
```
c, d, e为动态分配内存。
c定义没有括号，也就是没有显式的初始化， 此时默认使用不带参数的构造函数。 当类中不显式定义构造函数的时候， 类是会有默认构造函数的， 但是当显式定义了构造函数之后， 默认的构造函数就不起作用了， 此时若不显式定义不带参数的构造函数， 便会报错

d是显式的使用无参数的构造函数初始化， e是使用带参数的构造函数初始化， 至于f就是最普通的类定义， 不是指针类型， 就是一个变量。

(2)在C#中只存在值类型与引用类型的区分， 所以类定义也比较简单， instance g = new instance(), 必须要显式的初始化。
<br/></br>


* <font color="red">C语言内存分配的问题</font>


