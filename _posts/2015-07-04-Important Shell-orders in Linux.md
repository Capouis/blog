---
layout: default
title: Important Shell-orders in Linux
comments: true
---

#### 常见的shell命令总结

--------------

## 命令格式

正常情况下的命令格式都是： 命令  -选项  参数， 比如 ls -l /bin
如果有多个选项， 可以写在一起
</br></br>


## 文件处理命令

```
文件共享里面有两种连接方式： 硬连接 | 软连接。 硬连结的意思是一个档案可以有多个名称，而软连结的方式则是产生一个特殊的档案，该档案的内容是指向另一个档案的位置。硬连接是存在同一个档 案系统中，而软连接却可以跨越不同的档案系统。
软连接： ln -s file name
硬连接： ln file name
```

```
chmod
change the permission mode of a file, 改变文件或者目录的权限
在这之前需要了解 ls这个命令， ls -l file 显示该文件的类别， 拥有者信息， 权限等。
其中权限有r, w , x 分别表示可读， 可写， 可执行文件， 当对应位为1时， 表示有这个权限， 比如111表示3种操作都可以， 001表示不可读也不可写， 但是可执行。 所以 chmod 777 file, 就能将文件权限修改掉。
完整的指令为： chmod [{ugo}{+-=}{rwx}][文件或者目录]
                     [mode = 421] [文件或目录]
                     -R 递归修改
                     
  比如： chmod g+w file 表示增加文件所在组的写权限
```  


```
chown
change file ownership 改变文件的拥有者
比如chown nobody file 改变文件的所有者为nobody
```

```
chgrp 
改变文件或目录所属的组
chgrp adm file 改变文件所属组是adm， 查看系统文件有哪些分组可以用group命令
```

```
which
显示系统命令所在目录 比如which grep 返回grep命令所在的位置：/bin/grep
```

```
find
find [搜索范围] [匹配条件]
比如： find /bin -name grep 查找/bin路径下grep命令
       find / -size +204800  在根目录下查找 >100MB的文件
       find /bin -user tcoops 在/bin目录下查找所有者为tcoops的文件
       find /etc -size +1024 -a -size -2048 查找/etc目录下>1024B <2048B的文件
```       

```     
grep
这个指令很重要， grep -iv [指定字串] [文件] 在文件中搜索字串匹配的行并输出
      -i 不区分大小写
      -v 排除指定字串
比如： grep -iv fuck ～/fuck.py 查找fuck.py文件中除了fuck以外的字串
       grep -i fuck ~/fuck.py 查找fuck.py文件中包含fuck字串的行
```

```
man
查看命令的帮助信息 man grep
info grep， help grep 也可以查看命令的帮助信息  
```









