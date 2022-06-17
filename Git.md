# Git

### 1 Git与SVN的主要区别

#### 1.1 SVN

+ SVN是==集中式版本控制系统==，版本库放在==中央服务器==
+ 工作的时候用的个人电脑首先要从中央服务器获取最新版本，工作完成后需要再将最新的版本推送的中央服务器
+ 工作时必须联网

#### 1.2 Git

+ Git是==分布式版本控制系统==，没有中央服务器
+ 每个人的电脑就是一个完整的版本库，工作的时候不需要联网
+ 工作后再将新的版本推送到服务器供他人使用

### 2 Git使用

#### 2.1 Git工具

+ **Git Bash：**Unix与Linux风格的命令行，使用最多，推荐最多

+ **Git CMD：**Windows风格的命令行

+ **Git GUI：**图形界面的Git

#### 2.2 Linux的基本命令

+ ``cd  //改变目录``
+ ``cd ..  //返回上一级目录``
+ ``pwd  //print working directory 显示当前所在的目录路径``
+ ``ls(或者ll)  //列出当前目录的所有文件，括号内的信息更详细``
+ ``touch fileName.fileType  //新建一个文件``
+ ``rm fileName.fileType  //删除一个文件``
+ ``mkdir dirName  //新建文件夹(目录)``
+ ``rm -r dirName  //-r代表recursion 删除一个文件夹``
+ ``mv fileName.fileType  //移动文件``
+ ``reset  //重新初始化终端or清屏``
+ ``clear  //清屏``
+ ``history  //查看历史命令``
+ ``help  //帮助``
+ ``exit  //退出``
+ ``#  //表示注释``

#### 2.3 配置用户名和邮箱

+ ``git config --global user.name "KallenKozuki" #名称``
+ ``git config --global user.email "kallen_kozuki@163.com" #邮箱``

### 3 Git基本理论

#### 3.1 本地目录+远程仓库的数据传输结构

+ **远程仓库（Remote Repository）：**托管代码的服务器，主要用于远程数据交换

> ↑↑ ``git push`` ↑↑			↓↓ ``git pull`` ↓↓

+ **资源库（Repository）：**本地安全存放数据的位置，包含了提交过的所有版本的数据，其中HEAD文件代表最新版本

> ↑↑ ``git commit`` ↑↑			↓↓ ``git reset`` ↓↓

+ **暂存区（Stage/Index）：**临时存放改动，本质是一个文件，暂存需要改动的文件列表

> ↑↑ ``git add`` ↑↑			↓↓ ``git checkout`` ↓↓

+ **工作目录（Working Directory）：**平时存放代码的地方

#### 3.2 工作流程

+ 在工作目录中添加、修改文件
+ 将需要进行版本管理的文件放入暂存区
+ 将暂存区的文件提交到本地git仓库
+ 将本地git仓库推送到远程

### 4 Git使用

#### 4.1 创建仓库

+ 本地创建：``git init``
+ 远程克隆：``git clone url``

#### 4.2 文件状态

**概述：**版本控制就对文件的版本控制，要对文件进行修改、提交等操作。文件共有四种状态，不同状态在提交时会有不同的性质

+ **==Untracked：==**此文件在文件夹中，并没有加入到git库，不参与版本控制，利用``git add``可改变状态为**Staged**
+ **==Unmodify：==**文件已入本地库，未修改，与文件夹中的一致，修改后变为**Modified**，使用``git rm``移出版本库，成为==Untracked==
+ **==Modified：==**文件已修改，仅仅是修改，没有其他操作，使用``git add``进入**Staged**状态，利用``git checkout``则丢弃修改变回**Unmodify**，本质上是从库中取出修改前的文件覆盖当前的修改
+ **==Staged：==**暂存状态，执行``git commit``则同步到本地库中，此时库内的文件又变为与本地文件夹一致，即所有文件变为**Unmodify**状态

> ``git status``可以查看当前的状态

#### 4.3 .gitignore

+ 文件中的空行或者井号 ``#`` 开始的行会被忽略
+ 可以使用Linux通配符： ``*`` 例如代表任意多个字符
+ 名称前加一个 ``!`` 表示例外规则
+ 文件名称前加一个 ``/`` 表示要忽略的文件在根目录下，而子目录中的文件不忽略
+ 名称后加一个 ``/`` 表示忽略此目录下的所有文件

```git
#这一行会被忽略，表示注释
*.txt		#表示所有的 txt 文件都会被忽略，不会被添加至仓库
!test.txt	#此时 test.txt 不会被忽略
/temp		#仅忽略根目录的temp文件，不忽略子目录中的同名文件
dir/		#表示忽略 dir 目录下的所有文件
doc/*.txt	#忽略 doc 目录下的所有 txt 文件，但不会忽略 doc 下子目录中的txt文件，如 doc/next/test.txt
```

