**NFS**

----------


网络文件系统，也是是TCP/IP协议集所提供的一种子协议，这个本质是文件系统，将远程计算机磁盘挂载到本地，像本地磁盘一样操作，这个的限制就是在类UNIX的操作系统上使用。

**NIS**

网络信息服务，即实现账号的共享服务

**AUTOFS**

文件系统自动挂载，什么是自动挂载呢，如果是用NFS，我们需要挂载才能访问，但是对于普通用户来说不能执行挂载操作，总不能把管理员账户登陆密码给他，让他挂载，这样就显得很捞。所以这个autofs作用就是让用户在访问的时候自动挂载，不访问时又自动卸载。

我们要用这三个来实现我们题目所说的用户家目录账号的共享，也就是只需要有一台服务端提供共享服务，其他的客户端能够登录服务端的用户账号并且进入家目录。
我们在这里就只用两台机器去实现（当然也可以多搞几台客户端机器）
|服务端| 客户端 |  
|--|--|
| centos7 | centos6 |
|172.18.254.136| 172.18.254.120 |


|  服务端设置|  
|--|--|

**1，安装所需包**

```
[root@localhost张大帅哥 ~]#  yum install -y ypserv ypbind yp-tools
```

**2，配置NIS**

配置NIS域，这个设置是永久生效（域的名字随便起）
```
[root@localhost张大帅哥 ~]# vim /etc/sysconfig/network
	NISDOMAIN=zhang
```
这个设置是当前生效

```
[root@localhost张大帅哥 ~]# nisdomainname zhang
```
编写NIS主配置文件
在文件最后一行添加以下内容，允许自己以及这个网段内的主机连接自己，其他的拒绝连接
```
[root@localhost张大帅哥 ~]# vim /etc/ypserv.conf
	127.0.0.1:*:*:none
	172.18.254.0/255.255.255.0:*:*:none
	*:*:*:deny
```

**3，重启NIS服务**

重启服务并设为开机启动
```
[root@localhost张大帅哥 ~]# systemctl restart ypserv.service 
[root@localhost张大帅哥 ~]# systemctl enable ypserv.service 
```

**4，创建几个用户并指定家目录**

我这里设置家目录在/app/homedir下，这个目录要先创建好

```
[root@localhost张大帅哥 ~]# for n in {1..5};do useradd -d /app/homedir/nisuser$n nisuser$n;echo 123 |passwd --stdin nisuser$n;done
```
用for循环创建了五个nisuser用户，密码为123，家目录都在/app/homedir下为各自名字

**5，生成nis数据库**


```
[root@localhost张大帅哥 ~]# /usr/lib64/yp/ypinit -m
  At this point, we have to construct a list of the hosts which will run NIS
  servers.  localhost is in the list of NIS server hosts.  Please continue   to     add
  the names for the other hosts, one per line.  When you are done with the
  list, type a <control D>.
  next host to add:  localhost
  next host to add: 
```
执行这条命令后，根据提示我们按CTRL+d，然后按y，生成NIS数据库

**6，配置NFS**

安装NFS包

```
[root@localhost张大帅哥 ~]# yum -y install nfs-utils
```
编辑子配置文件把/app/homedir下的家目录共享出去，自配置文件的名字也是随便起，最好起的自己知道啥意思

```
[root@localhost张大帅哥 ~]# vim /etc/exports.d/nis.exports
	/app/homedir *(rw,sync)
```
重启NFS

```
 [root@localhost张大帅哥 ~]# systemctl restart nfs
```
|客户端设置|  
|--|--|

**1，连接服务端数据库**

首先要有ypbind
```
[root@localhost ~]# yum install -y ypbind
[root@localhost ~]# setup
```
使用setup命令后就能出现一个类似图形化的界面，选择第一项，然后NEXT
![在这里插入图片描述](https://img-blog.csdn.net/20181009204913105?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
找到 use nis 这一项 然后按空格键，最后NEXT,在下一个弹出的界面中第一行填写在服务器端设置的域的名字，我的是zhang，第二行写服务端的ip，我的是172.18.254.136，然后完成。
可能成功之后还会弹出一下这个界面，直接返回就行了，这样我们就连接成功了，这时候我们可以查看本地用户，用getent查看

```
[root@localhost ~]# getent passwd
```
我们可以看到我们在服务端创建的五个用户，这时候账户共享已经成功，那么，家目录呢？最后一个autofs

**1，装包**

```
[root@localhost ~]#  yum -y install autofs
```

**2，编辑主配置文件**

```
[root@localhost ~]# vim /etc/auto.master
 /app/homedir    /etc/auto.zhang --timeout=5
```
添加这样一行，这里写的是目录名，基名是在指定的auto.zhang 文件中写
复制一份模板，然后编辑

```
[root@localhost ~]# cp /etc/auto.misc /etc/auto.zhang
[root@localhost ~]# vim /etc/auto.zhang 
	*               -fstype=nfs     172.18.254.136:/app/homedir/&
```
将复制的文件中没有注释的一行更改为这样，这个意思就是说，*是什么，&就是什么，跟后向引用差不多。

**3，重启autofs服务**

```
[root@localhost ~]# service autofs restart
```
最后，我们需要在本地创建/app/homedir目录用来挂载这些用户的家目录

```
[root@localhost ~]# mkdir -p /app/homedir
```
|测试|  
|--|--|
直接切换到nisuser2下
```
[root@localhost ~]# su - nisuser2
[nisuser2@localhost ~]$ ls -a
  .  ..  .bash_logout  .bash_profile  .bashrc  .mozilla
  [nisuser2@localhost ~]$ df
172.18.254.136:/app/homedir/nisuser2
                      52403200 4518272  47884928   9% /app/homedir/nisuser2
```
用户家目录切换成功，并且自动挂载，退出后，回到root下再查df挂载，会在一定时间内取消挂载


通过这种方法，我们在本地没有某个用户和其家目录的的情况下实现了对账户的共享，家目录的漫游，这种方法在生产环境中很常见，账户及家目录的漫游，自动挂载，自动取消挂载。



