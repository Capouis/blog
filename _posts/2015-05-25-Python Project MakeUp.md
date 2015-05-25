---
layout: default
title: Python Project - MakeUp
comments: true
---

####<font color="green"> 在 *python基础教程* 这本书里面介绍了很多小的项目， 为提高python的设计以及软件的设计能力提供了非常好的资源，可以尝试做一下。
</font>
===

这个项目是用来标记一个文本文件， 使他成为可以展示的HTML页面

实现它的意义不在于项目本身的难度（比较简单）， 而在于通过这个项目了解如何一步步的学会构建可以重构的代码

整个项目的文件结构是：

```
MakeUp
├── util.py 
├── handlers.py
├── rules.py
├── makeup.py
```

* handlers.py 

这个就是一个处理文件， 用来对已经识别到的标记做处理。 比如已经识别到某文件块是一个paragraph, 那么就要输出*<p>和</p>*这两个HTML元素。

OOP部分设计的思路是： 将可以抽象出来的东西都放在基类里面，比如这里的Handler， 然后不同的处理类来继承这个基类， 并且重写属于自己的一些
属性或者方法。 这种设计思想在之后的Rules和makeup文件里面都有体现。

值得一提的是在sub方法里面， 用了一个嵌套函数， 构建了一个闭包。 要看清返回的是什么， 也要了解RE这个模块是如何进行sub(替换)的

<font color = "red"> Source Code </font>

```
__author__ = 'tcOops'

class Handler:
    def callback(self, prefix, name, *args):
        foo = getattr(self, prefix+name, None)
        if callable(foo):
            return foo(*args)

    def start(self, name):
        self.callback('start_', name)

    def end(self, name):
        self.callback('end_', name)

    def sub(self, name):
        def substitution(match):
            ans = self.callback('sub_', name, match)
            if ans is None:
                ans = match.group(0)
            return ans
        return substitution

class RenderHTML(Handler):
    def start_document(self):
        print "<html><head></head><body>"
    def end_document(self):
        print "</body></html>"

    def start_paragraph(self):
        print "<p>"
    def end_paragraph(self):
        print "</p>"

    def start_heading(self):
        print "<h3>"
    def end_heading(self):
        print "</h3>"

    def start_list(self):
        print "<ul>"
    def end_list(self):
        print "</ul>"

    def start_listitem(self):
        print "<li>"
    def end_listitem(self):
        print "</li>"

    def start_title(self):
        print "<h2>"
    def end_title(self):
        print "</h2>"

    '''
    filter
    '''
    def sub_emphasis(self, match):
        return "<em>%s</em>" %match.group(1)

    def sub_url(self, match):
        return "<a href=%s>%s</a>" %(match.group(1), match.group(1))

    def sub_email(self, match):
        return '<a href="Email: %s">%s</a>' %(match.group(1), match.group(1))

    '''
    process datam, here just output it to the standard output
    '''
    def feed(self, block):
        print block
```
</br>

* rules.py






