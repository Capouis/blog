---
layout: default
title: Git and Github - Simple Tutorial
comments: true
---

首先區分git 與 github：

* git是一种版本控制系统， 跟svn、cvs是同级的概念
* github是一个网站，给用户提供git服务。这样你就不用自己部署git系统，直接用注册个账号，用他们提供的git服务就可以

大概介紹一下如何在github來管理自己的項目
（有人曾經很有趣的這樣解釋過github :)  ---> [:)]("http://www.zhihu.com/question/20070065")

### <font color="red"> *安裝Git* </font>

第一步肯定是要安裝Git,  在ubuntu的終端下可以執行下面的命令：

```
sudo apt-get install git git-core git-gui git-doc git-svn git-cvs gitweb gitk git-email git-daemon-run git-el git-arch
```


### <font color="red"> *設置SSH鏈接以及git信息* </font>

因爲在本地與服務器上都有代碼， 那麼在本地你要能夠遠程鏈接服務器， 這裏通過SSH進行鏈接
（在windows下安裝了github的客戶端之后， 提供了設置賬戶的界面， 可以直接使用git-shell, 下面介紹的是ubuntu下的git安裝）

* 先檢查一下SSH能不能鏈接：

```
    ssh -T git@github.com  
```

如果看到：

```
    Warning: Permanently added ‘github.com,204.232.175.90′ (RSA) to the list of known hosts.  
    Permission denied (publickey).  
```
则说明可以连接。


* 检查~/.ssh目录下是否有id_rsa（私钥）和id_rsa.pub（公钥）文件，正常情況下是有的（.ssh是隱藏文件， 注意執行命令時候的目錄位置）， 如果沒有則要輸入：

```
    ssh-keygen -t rsa -C "your email"  
```

* 在瀏覽器下進入github主頁面：Account settings -> SSH Keys -> Add SSH Key，将id_rsa.pub文件中的字符串复制进去（整個文件完全複製， 不要忽略任何東西）

* 再次检查SSH连接情况：

```
    ssh -T git@github.com  
```
正常情況下， 會提示你可以鏈接了， 那麼你就可以在本地鏈接github了

* 設置git的個人信息:

這裏就是設置用戶名與郵箱， 在終端輸入：

```
    git config --global user.name "name"  
    git config --global user.email "email"   
```
    

### <font color="red"> *github的使用* </font>

* 創建項目

創建項目有兩種方式： 從別人的github上fork 或者自己主動創建

如果是主動創建項目， 可以先在github上先創建項目， 然後clone到本地。 github爲每個項目都提供了一個分支以及一個url進行標識。
所謂分支是指一個項目不同的分流， 這些分流之間互不影響， 自成一派， 但是可以請求合併到主流中去。 根據github提供的url, 我們可以
使用git將新創建的項目clone到本地

```
git clone url
```

更爲常見的地方是在本地先創建項目， 然後使用git push到github上, 這裏細說一下步驟：
* 先在本地創建項目文件夾, 進入文件夾後：
* 
```
git init
```
進行git環境的初始化。 


* 初始化之后要爲項目創建一個分支名稱， 不創建默認應該是master分支， 你也可以使用

```
git checkout --orphan gh-pages
```
來創建一個名叫gh-pages的分支（這裏只是舉個例子， 當你push到github上去之后， 也是這個分支名稱， 也就是說你創建的項目的源分支就是
gh-pages了）

* 下面進行首次提交
比如現在的本地項目文件夾爲空， 要首次創建一個read me 文件， 可以這樣：
先在vim或者其他文件編輯器中編輯rrad me 文件， 然後在bash中進入項目所在文件夾：

```
git status -s
```
這句話可以看到當前環境下文件發生的變化， 會提醒你Add read me文件， 如果你想添加read me文件， 那麼可以輸入：

```
git add read me
```
當然你頁可以輸入： git add .   這裏的. 表示添加所有變動的文件， 同時要注意修改文件之后頁同樣使用git add
下面要注意的是， 要執行這句話：

```
git commit -m "log something"
```
爲何要commit, 因爲其實git是分層次的， 當你在本地創建完項目之后， 只是添加在本地跟git沒有任何關係
當你git add之后就將變動添加到git的緩衝區中， 但是還沒有正式提交到本地的git環境中， 只有當你commit之后， 變動的文件
才能真正的在git環境中體現

執行完上面的
