这些文本处理工具都是不对原文件做修改的，一般用于筛选比较排序

----------

cut
----------


----------
这是一个文本截取工具，能从文本中的每一行提取片段。
cut [OPTION] FILE
     -d CHAR:以指定字符为分隔符
     -c file:分割字符
     -f FILELDS:挑选出字段（一般与-d搭配使用）
         #：指定单个字段
        #-#：连续的多个字段
        #，#离散的多个字段
例如：以/etc/passwd的后10行为例（在这里用到了tail，后面介绍，把后10行写进另一个文件中，便于操作演示）

```
[root@localhost张大帅哥 ~]# cat /etc/passwd |tail >test.txt
[root@localhost张大帅哥 ~]# cat test.txt 
ntp:x:38:38::/etc/ntp:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
gnome-initial-setup:x:989:983::/run/gnome-initial-setup/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
zhang:x:1000:1000:zhang:/home/zhang:/bin/bash
archlinux:x:1001:1001::/users/archlinux:/bin/bash
zhangfei:x:1002:1002::/home/zhangfei:/bin/bash

```
如果我们要取出用户名（也就是第一个冒号前的一段），可以这样操作

```
[root@localhost张大帅哥 ~]# cat test.txt |cut -d: -f1
ntp
gdm
gnome-initial-setup
sshd
avahi
postfix
tcpdump
zhang
archlinux
zhangfei

```
这样写的意思就是以冒号为分隔符，取出第一列，同样也可以取第二列，等。cut这两个选项是最常用的
sort
----------
----------
很明显，这是一个排序命令
sort [OPTION]  FILE
  -t CHAR：指定分隔符
  -n：基于数值大小
 -k#：用于排序比较的字段（一般与-t一起使用）
 -r：逆序排列
 -f：忽略字符大小写
 -u：重复的行只保留一份（这里重复的行指的是连续且相同的行）
 sort的-t -k跟cut的-d -f差不多，只不过一个是截取，一个是排序，还是刚才的例子，把第三列的数由大到小排列
 

```
zhangfei:x:1002:1002::/home/zhangfei:/bin/bash
[root@localhost张大帅哥 ~]# cat test.txt |sort -t: -k3 -n -r
zhangfei:x:1002:1002::/home/zhangfei:/bin/bash
archlinux:x:1001:1001::/users/archlinux:/bin/bash
zhang:x:1000:1000:zhang:/home/zhang:/bin/bash
gnome-initial-setup:x:989:983::/run/gnome-initial-setup/:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin

```
默认排序是从小到大，所以用-r逆序排列就是从大到小了
uniq
----------

----------
uniq   删除排序文本中的重复行
为了更好的演示，把test.txt文本中的内容改了。
 -u 只显示不重复的行
 -c 显示每行重复的次数
```
[root@localhost张大帅哥 ~]# cat test.txt 
a
b
b
b
c
d
d
e
e
[root@localhost张大帅哥 ~]# cat test.txt |uniq
a
b
c
d
e

```
这个作用跟sort -u的选项差不多。同样是重复的行

```
[root@localhost张大帅哥 ~]# vim test.txt
[root@localhost张大帅哥 ~]# cat test.txt |uniq -c
      1 a
      3 b
      1 c
      2 d
      2 e
[root@localhost张大帅哥 ~]# cat test.txt |uniq -u
a
c

```
wc
----------


----------
在这里wc可不是厕所的意思，这条命令用来统计。
wc
    -l:显示行数
    -m：显示字符数
    -c：显示字节数
    -w：显示单词数
   -L:显示最长的长度
只打wc则显示行数       单词数       字节数
我们继续来操作/etc/passwd的后十行

```
[root@localhost张大帅哥 ~]# cat test.txt |wc -l
10
[root@localhost张大帅哥 ~]# cat test.txt |wc -m
514
[root@localhost张大帅哥 ~]# cat test.txt |wc -c
514
[root@localhost张大帅哥 ~]# cat test.txt |wc -w
13
[root@localhost张大帅哥 ~]# cat test.txt |wc -L
73
[root@localhost张大帅哥 ~]# cat test.txt |wc 
     10      13     514

```
cat
----------


----------
cat命令是我们经常用的一条命令，就是获取文本内容的一条命令
 -E 显示$(在文本中每行的末尾都会有一个隐藏的$换行符)
 -v 显示windows文本中的换行符（从windows中传到linux中的文本每行末尾都会有一个隐藏的^M）
 -T 显示TAB
 -A（all）=EvT
 

```
[root@localhost张大帅哥 ~]# cat -E test.txt
ntp:x:38:38::/etc/ntp:/sbin/nologin$
gdm:x:42:42::/var/lib/gdm:/sbin/nologin$
gnome-initial-setup:x:989:983::/run/gnome-initial-setup/:/sbin/nologin$
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin$
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin$
postfix:x:89:89::/var/spool/postfix:/sbin/nologin$
tcpdump:x:72:72::/:/sbin/nologin$
zhang:x:1000:1000:zhang:/home/zhang:/bin/bash$
archlinux:x:1001:1001::/users/archlinux:/bin/bash$
zhangfei:x:1002:1002::/home/zhangfei:/bin/bash$

```
rev
----------


----------
这条命令就是这样用，直接跟文本文件就行了，或者管道也行
输出结果就是把标砖输入的内容每行倒着显示（我们只取test.txt中的前三行来演示，代码段中提出的head在后面提到）

```
[root@localhost张大帅哥 ~]# cat test.txt |head -3 |rev
nigolon/nibs/:ptn/cte/::83:83:x:ptn
nigolon/nibs/:mdg/bil/rav/::24:24:x:mdg
nigolon/nibs/:/putes-laitini-emong/nur/::389:989:x:putes-laitini-emong


```
head
----------


----------
这个单词意思是头，很明显是用来取出前几行的用法就是
head -n #简写为head -#，只打head默认显示前十行
去除第一行就是head -1
tail
----------


----------
这个用法跟head一样，只是tail是取后面，从后面开始取，默认输出后十行，如果我想输出文本中的某一行就可以把这两个结合起来用

```
[root@localhost张大帅哥 ~]# cat test.txt |head -3 |tail -1
gnome-initial-setup:x:989:983::/run/gnome-initial-setup/:/sbin/nologin

```
这样就只取出了第三行。
 



