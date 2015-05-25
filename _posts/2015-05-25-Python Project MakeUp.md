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

* util.py

这个文件相对比较简单， 就是用来将输入文件解析成一个个块（block）。 注意整个文件是用空行来分割块的， 所以即使文件的结尾也要加上一个空行来作为一个分割符

<font color = "red"> Source Code </font>

```
def lines(file):
    for line in file:
        yield line

def blocks(file):
    fp = open(file)
    block = []
    for line in fp.readlines():
        if line.strip():
            block.append(line)
        elif block:
            yield ''.join(block).strip()
            block = []
    fp.close()
```
</br>


* handlers.py 

这个就是一个处理文件， 用来对已经识别到的标记做处理。 比如已经识别到某文件块是一个paragraph, 那么就要输出```<p>和</p>```这两个HTML元素。

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

这个模块用来设定可以匹配的规则，包含两个部分：

* condition 用来判断某一个block是否满足当前的规则， 如果满足当前的规则， 就可以将type作为参数去执行handler的操作， 也就是去标记输出

* action 这里的action在基类中完成， 因为他的功能比较简单， 就是简单的执行加标记， 输出data的任务

<font color="red"> Source Code </font>

```
'''
Rule Class contains two parts:
1: action
2: condition

action is used for used to makeup the text, you can see the function in class called Rule,
in this class, just always return true because I presume that when encounting with a fit condition,
it will stop to continue to find the condition any more.

condition returns true or false, and true means this block is ok for this rule's condition.
'''

#Base Calss
class Rule:
    def action(self, block, handler):
        handler.start(self.type) #type is a attribute in subclass
        handler.feed(block)
        handler.end(self.type)
        return True


class HeadingRule(Rule):
    type = "heading" #Care the synatx of this name, fit with the name in html_render
    def condition(self, block):
        return not '\n' in block and len(block) <= 70 and not block[-1] == ":"

class TitleRule(HeadingRule):
    type = "title"
    first = True

    def condition(self, block):
        if not self.first:
            return False
        self.first = False
        return HeadingRule.condition(self, block)

class ListItemRule(Rule):
    type = "listitem"

    def condition(self, block):
        return block[0] == '-'

    def action(self, block, handler):
        handler.start(self.type)
        handler.feed(block[1:].strip())
        handler.end(self.type)
        return True

class ListRule(ListItemRule):
    type = "list"
    inside = False

    def condition(self, block):
        return True

    def action(self, block, handler):
        if not self.inside and ListItemRule.condition(self, block):
            handler.start(self.type)
            self.inside = True # in ul
        elif self.inside and not ListItemRule.condition(self, block):
            handler.end(self.type)
            self.inside = False # out ul
        return False

class ParagraphRule(Rule):
    type = "paragraph"
    def condition(self, block):
        return True
```
</br>

* makeup.py

这是整个程序的主模块。 之前存在handler的模块， rule的模块， util的模块， 现在makeup要将他们整合起来， 形成一个系统。
整个makeup的操作流程大概是： 
* 先添加filter规则和rule规则， filter是用来做一替换操作的， 比如将Email换成超链接的形式

* 对每一个block先根据filter规则对block进行格式化， 然后去判断一下是否满足某个rule， 如果满足就进行标记

<font color="red"> Source Code </font>

```
import sys, re
from handlers import *
from rules import *
from util import *

class Parser:
    def __init__(self, handler):
        self.handler = handler
        self.rules, self.filters = [], []

    def addRule(self, rule):
        self.rules.append(rule)

    #Special
    def addFilter(self, pattern, name):
        def filter(block, handler):
            return re.sub(pattern, handler.sub(name), block)
        self.filters.append(filter)

    def parse(self, file):
        self.handler.start('document')
        for block in blocks(file):
            for filter in self.filters:
                block = filter(block, self.handler)

            for rule in self.rules:
                if rule.condition(block):
                    last = rule.action(block, self.handler)
                    if last:
                        break
            self.handler.end("document")

class TextParser(Parser):
    def __init__(self, handler):
        Parser.__init__(self, handler)
        self.addRule(ListRule())
        self.addRule(ListItemRule())
        self.addRule(TitleRule())
        self.addRule(HeadingRule())
        self.addRule(ParagraphRule())

        emphasis_pattern = r"\*(.+?)\*"
        url_pattern = r"(http://[.a-zA-Z/]+)"
        email_pattern = r"([1-9\.a-zA-Z]+@[1-9\.a-zA-Z]+)"

        self.addFilter(emphasis_pattern, 'emphasis')
        self.addFilter(url_pattern, 'url')
        self.addFilter(email_pattern, 'email')

if __name__ == "__main__":
    handler = RenderHTML()
    parser = TextParser(handler)
    file = r"C:\Users\tcOops\Desktop\a.txt"
    parser.parse(file)
```
