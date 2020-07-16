---
title: Mac终端命令大全
date: 2018-05-13 15:06:27
tags: [Mac终端命令]
category: [工具学习]
---

# 常用命令

## 查看
在终端里输入`ls`就可以查看文件和文件夹，但隐藏的文件就无法查看，使用`ls -a`即可。
<!--more-->
## 创建目录
命令：`mkdir`
使用说明：输入命令随后空格再输入目录名即可。 
案例：创建一个test目录则对应命令就是`mkdir test`

## 进入目录
cd命令进入目录比如要进入test目录则就是`cd test`

## 返回上一级目录
命令：`cd..`

## 删除目录（空目录）
命令：`rmdir` 
案例：`rmdir test`（rmdir命令后面空格在加上目录名） 
注意：此删除不会出现在废纸篓里

## 删除目录（非空或者空目录都可以删除）推荐使用
命令： `rm -rf`
案例：`rm -rf test`（`rm -rf`命令后面空格在加上目录名） 
注意：此删除不会出现在废纸篓里

## 创建文件
命令:`touch` 
笔者感觉这个命令还是挺靠谱的。Mac如果不用第三方工具（sublime等）是无法可视化创建一个txt文件。而用这个命令即可创建。 
示例（创建一个test为名的txt文件）：`touch test.txt`

## 删除文件
命令：rm 
案例（删除一个test.txt文件）：`rm test.txt`
注意：此删除不会出现在废纸篓里

## 拷贝
命令：cp 
案例（拷贝一个test.txt文件并重新命名为test2.txt）：`cp test.txt test2.txt`

## 查找
命令：find 
案例（查找当前目录下所有的txt文件）：`find *.txt`

## 显示当前的目录
命令：`pwd`

## 打开成可视化的文件夹
命令：`open` 
说明：如果是`open .`则是打开当前命令里的目录，如果要打开指定的文件夹或者文件则格式为`open 要打开的路径`即可。
# Mac 打开、编辑 .bash_profile 文件
一般在Mac上配置环境变量时经常要创建、编辑 .bash_profile文件。 
创建该文件时一般都会选择在当前用户目录下，即Mac下的.bash_profile 文件的路径是 `/Users/YourMacUserName/.bash_profile `(如果该文件已经创建过的话)

1、创建 .bash_profile
 （1） 启动终端Terminal
 （2） 进入当前用户的home目录： cd ~   或 cd /Users/YourMacUserName
 （3）输入touch .bash_profile
2、查看 、编辑 .bash_profile 文件
 （1）终端输入 open -e .bash_profile
 （2）编辑
 （3）关闭即可保存修改
3、更新刚配置的环境变量
  输入`source .bash_profile`
  
# MAC 设置环境变量PATH 和 查看PATH

## 理论篇
Mac系统的环境变量，加载顺序为：
`/etc/profile` `/etc/paths` `~/.bash_profile` `~/.bash_login` `~/.profile` `~/.bashrc`

`/etc/profile`和`/etc/paths`是系统级别的，系统启动就会加载，后面几个是当前用户级的环境变量。后面3个按照从前往后的顺序读取，如果`/.bash_profile`文件存在，则后面的几个文件就会被忽略不读了，如果`/.bash_profile`文件不存在，才会以此类推读取后面的文件。`~/.bashrc`没有上述规则，它是`bash shell`打开的时候载入的。
PATH的语法为如下
```
#中间用冒号隔开
export PATH=$PATH:<PATH 1>:<PATH 2>:<PATH 3>:------:<PATH N>
```
## 上述文件的科普
- /etc/paths （全局建议修改这个文件 ）
编辑 paths，将环境变量添加到 paths文件中 ，一行一个路径
Hint：输入环境变量时，不用一个一个地输入，只要拖动文件夹到 Terminal 里就可以了。
- /etc/profile （建议不修改这个文件 ）
全局（公有）配置，不管是哪个用户，登录时都会读取该文件。
- /etc/bashrc （一般在这个文件中添加系统级环境变量）
全局（公有）配置，bash shell执行时，不管是何种方式，都会读取此文件
- .profile 文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行.并从/etc/profile.d目录的配置文件中搜集shell的设置; **使用注意**：如果你有对/etc/profile有修改的话必须得重启你的修改才会生效，此修改对每个用户都生效。
- ./bashrc 每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.**使用注意** 对所有的使用bash的用户修改某个配置并在以后打开的bash都生效的话可以修改这个文件，修改这个文件不用重启，重新打开一个bash即可生效。
- ./bash_profile 该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取.（每个用户都有一个.bashrc文件，在用户目录下）**使用注意** 需要需要重启才会生效，/etc/profile对所有用户生效，~/.bash_profile只对当前用户生效。
- source ./.bash_profile 或者 ./.profile 环境信息生效

## 操作篇
### 全局设置
1. 创建一个文件：`sudo touch /etc/paths.d/mysql`
2. 用 vim 打开这个文件（如果是以 open -t 的方式打开，则不允许编辑）：`sudo vim /etc/paths.d/mysql`
3. 编辑该文件，键入路径并保存（关闭该 Terminal 窗口并重新打开一个，就能使用 mysql 命令了）`/usr/local/mysql/bin`
4. `source 相应的文件` 生效配置环境
### 单个用户设置
1. `cd ~`
2. `vim ~/.bash_profile` （任意一个文件中添加用户级环境变量）: `export PATH=/opt/local/bin:/opt/local/sbin:$PATH`
3. 把上述代码添加到~/.bash_profile上。
4. source 相应的文件 生效配置环境
### 查看PATH
`echo $PATH`
# screen命令整理
## 常用指令
- `screen`代表增加一个虚拟终端
- `ctrl + A + D` 挂起虚拟终端，进入真终端
- `kill xxxx` 结束虚拟终端 
- `screen -r` 进入虚拟终端
- `Ctr-a k` 杀死某一个窗口
1. 一个全屏窗口管理器，用他可以轻松在一个物理终端上获得多个虚拟终端的效果。
2. 简单来说，Screen是一个可以在多个进程之间多路复用一个物理终端的窗口管理器,这意味着你能够使用一个单一的终端窗口运行多终端的应用。Screen中有会话的概念，用户可以在一个screen会话中创建多个screen窗口，在每一个screen窗口中就像操作一个真实的telnet/SSH连接窗口那样。
3. screen可以看作**窗口管理器的命令行版本**，因为它是运行在一个linux终端上。在**screen中启动的每一个会话都有一个id**。可以切换。它的特性：
1、可恢复

在远程登录linux的时候，如果终端长时间没有动作会被机器强制断线，一旦终端的通信中断，那么这个会话上的所有内容都会丢失。在screen中的会话，只要screen本身没有终止，其内部运行的会话都可以恢复。这一点对于远程登录用户特别有用。
2、多窗口

可以在一次远程登录终端中保持多个窗口，并可切换。

Session（会话） window（窗口）

前者包含多个后者。Ctr+c就是新建了一个window（可以在window里恢复一个session，那样会增加复杂度）

所有的窗口使用exit退出之后，session会自己退出

合理的使用方法是，每一项任务都是一个会话，每个会话里都有许多功能窗口

`Screen –ls`查看本机运行的screen会话

`Screen –SU <name>`新建一个会话

`Ctr-a d`离开一个会话进入真终端（会话进入detached挂起，不影响各window里进程的运行）

`Screen –rU <name or id>`恢复某个会话（会话进入attached）

`Ctr-a c` 新建一个窗口。

`Ctr-a k` 杀死某一个窗口

`Ctr-a n/f` 多个active窗口之间进行切换

`Ctr-a w` 显示窗口列表

`Ctr-a 数字` 切换至某个窗口

`Ctr-a a`最近使用的两个窗口间切换

在一个会话中，exit可以退出一个窗口。退出到底可以退出一个会话。
中文乱码问题：

secureCRT 设置字体、字符集、编码
screen的设置问题。
`C+a  ：defencoding utf8`

`C+a  : encoding utf8 utf8`

# 常用命令详解
## 基本命令
### 列出文件
`ls 参数 目录名`        例: 看看驱动目录下有什么:`ls /System/Library/Extensions`
`参数 -w 显示中文，-l 详细信息， -a 包括隐藏文件`
### 转换目录     

cd    例：想到驱动目录下溜达一圈   `cd /System/Library/Extensions`

### 建立新目录
`mkdir 目录名`     例：在驱动目录下建一个备份目录 backup     `mkdir /System/Library/Extensions/backup`
在桌面上建一个备份目录 backup    `mkdir /User/用户名/Desktop/backup`

### 拷贝文件
`cp 参数 源文件 目标文件`    
例：想把桌面的Natit.kext 拷贝到驱动目录中  `cp -R /User/用户名/Desktop/Natit.kext /System/Library/Extensions`
参数R表示对目录进行递归操作，kext在图形界面下看起来是个文件，实际上是个文件夹。
把驱动目录下的所有文件备份到桌面backup
`cp -R /System/Library/Extensions/* /User/用户名/Desktop/backup`

### 删除文件
`rm 参数 文件`   例：想删除驱动的缓存  `rm -rf /System/Library/Extensions.kextcache`     `rm -rf /System/Library/Extensions.mkext`
参数`－rf` 表示递归和强制，千万要小心使用，如果执行了 `rm -rf /` 你的系统就全没了

### 移动文件
`mv 文件`   例：想把AppleHDA.Kext 移到桌面    `mv /System/Library/Extensions/AppleHDA.kext /User/用户名/Desktop`
想把AppleHDA.Kext 移到备份目录中   `mv /System/Library/Extensions/AppleHDA.kext /System/Library/Extensions/backup`

### 文本编辑
`nano 文件名`   例：编辑natit Info.plist     `nano /System/Library/Extensions/Natit.kext/Info.plist`

 

## 目录操作

命令名                       功能描述                                             使用举例

`mkdir                        创建一个目录`                                      `mkdir dirname`

`rmdir                         删除一个目录`                                       `rmdir dirname`

`mvdir                        移动或重命名一个目录`                         `mvdir dir1 dir2`

`cd                             改变当前目录`                                       `cd dirname`

`pwd                          显示当前目录的路径名`                          `pwd`

`ls                              显示当前目录的内容`                             `ls -la`

 

## 文件操作

命令名                                 功能描述                                  使用举例

cat                                      显示或连接文件                       `cat filename`

od                                       显示非文本文件的内容            `od -c filename`

cp                                      复制文件或目录                        `cp file1 file2`

rm                                     删除文件或目录                         `rm filename`

mv                                    改变文件名或所在目录               `mv file1 file2`

find                                  使用匹配表达式查找文件             `find . -name "*.c" -print`

file                                  显示文件类型                                `file filename`

## 选择操作

命令名                             功能描述                                       使用举例

head                              显示文件的最初几行                       `head -20 filename`

tail                                 显示文件的最后几行                       `tail -15 filename`

cut                                显示文件每行中的某些域                 `cut -f1,7 -d: /etc/passwd`

colrm                            从标准输入中删除若干列                  `colrm 8 20 file2`

 

diff                                比较并显示两个文件的差异                `diff file1 file2`
 

 

sort                             排序或归并文件                                      `sort -d -f -u file1`

uniq                           去掉文件中的重复行                                  `uniq file1 file2`

comm                        显示两有序文件的公共和非公共行             ` comm file1 file2`

wc                            统计文件的字符数、词数和行数                    `wc filename`

nl                             给文件加上行号                                         `nl file1 >file2 `

## 进程操作

命令名                     功能描述                                                 使用举例

ps                           显示进程当前状态                                     `ps u`

kill                         终止进程                                                     `kill -9 30142`

## 时间操作

命令名                           功能描述                                            使用举例

date                    显示系统的当前日期和时间                           date

cal                                   显示日历                                       `cal 8 1996`

time                         统计程序的执行时间                            `time a.out`

## 网络与通信操作

命令名 功能描述 使用举例

telnet                                  远程登录                                 `telnet hpc.sp.net.edu.cn`

rlogin                                 远程登录                                 `rlogin hostname -l username`

rsh                       在远程主机执行指定命令                             `rsh f01n03 date`

ftp                   在本地主机与远程主机之间传输文件                `ftpftp.sp.net.edu.cn`

rcp                 在本地主机与远程主机 之间复制文件               `rcp file1 host1:file2`

ping                   给一个网络主机发送 回应请求                   `ping hpc.sp.net.edu.cn`

mail                          阅读和发送电子邮件                                          mail

write                      给另一用户发送报文                                  `write username pts/1`

mesg                    允许或拒绝接收报文                                                 `mesg n`

## Korn Shell 命令              

 命令名                                 功能描述                                        使用举例

history               列出最近执行过的 几条命令及编号                       `history`

r                         重复执行最近执行过的 某条命令                            `r -2 `

alias                            给某个命令定义别名                                  `alias del=rm -i`

unalias                     取消对某个别名的定义                                  `unalias del`

## 其它命令

命令名                                  功能描述                                         使用举例

uname                     显示操作系统的有关信息                              `uname -a`

clear                       清除屏幕或窗口内容                                        `clear`

env                        显示当前所有设置过的环境变量                         `env`

who                       列出当前登录的所有用户                                    `who`

whoami                  显示当前正进行操作的用户名                              `whoami`

tty                         显示终端或伪终端的名称                                         `tty`

stty                         显示或重置控制键定义                                        `stty -a`

du                                  查询磁盘使用情况                         `du -k subdir`
`df /tmp `                         显示文件系统的总空间和可用空间

`w `                                 显示当前系统活动的总信息


