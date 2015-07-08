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
ls -l： 显示当前目录下的所有可见文件的详细属性

比如:
ls　-l
drwxr-xr-x　3　user　group　102　Mar11　22:56　Filename

    文件属性：drwxr-xr-x
    其中各个字符代表的意义：
    d: 代表是目录。其他的还有，-代表文件
    rwx：代表属主权限，-代表无权限；r代表具有可读权限； w代表具有可写权限；x代表具有可执行权限
    r-x：代表组权限
    r-x：代表其他用户权限
    文件硬链接数或目录子目录数：3 （一个空目录的该字段是2，表示该目录下有两个子目录，因为每一个目录都有一个指向它本身的子目录"." 和指向它上级目录的子目录".."）
    所有者：user
    所属用户组：group
    文件大小：102 byte
    修改时间：Mar11　22:56
    文件名：Filename
    
  还可以看固定的某一列：
  ls -l | awk '{print $1, $2}', 这样可以查看第1,2列 
```

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

</br> </br>

## 压缩解压命令

```
gzip： GNU zip
gzip [文件] 压缩之后的文件格式: .gz
```

```
gunzip: GNU unzip
解压缩.gz的压缩文件
```

```
tar: 压缩解压命令
功能：打包目录-> tar [-zcvf] [压缩后文件名称] [目录]
tar -zcvf dir1.tar.gz dir1 将dir1目录压缩成一个打包并且压缩的文件
tar还能解压缩： tar -zxvf dir1.tar.gz 
```


```
zip: 压缩文件或目录
zip [-r] [压缩后文件名称] [文件或目录]
-r 压缩目录
比如： zip -r rep.zip rep 将rep目录压缩为rep.zip
```


```
unzip: 解压缩zip压缩文件
```
</br> </br>


## 网络通信命令

```
ping: 测试网络连通性
ping [选项] [ip地址]
 -c 指定发送次数
 -s 指定数据包大小
```

```
ifconfig: 查看或设置网卡信息
```
</br> </br>

## 系统关机命令

```
shutdown: 关机
比如： shutdown -h now
```

```
reboot: 重启
```









