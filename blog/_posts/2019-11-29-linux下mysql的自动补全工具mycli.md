习惯了在bash命令行下的tab自动补全功能，在linux下安装mysql，或者是mariadb后发现不能自动补全，这就让我很难受，所以我找到了一个很强大的工具可以实现自动补全

----------

这个工具就是mycli，这个工具很强大，先来看一下
![在这里插入图片描述](https://img-blog.csdn.net/20180925144350198?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
我用的是centos7下的mariadb数据库，当然你们看到了我的密码。
如果我正常登陆是什么样的呢？
![在这里插入图片描述](https://img-blog.csdn.net/20180925144826434?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这正常的就是这种情况。好了，下面介绍如何安装这个工具了
**基于什么**
这个工具是基于python安装的，所以要先安装pip和依赖包，pip是什么呢？？[这就是pip](https://baike.baidu.com/item/PIP/20435212?fr=aladdin)
开始安装吧
**1，安装依赖包**

```
[root@localhost ~]# yum -y install epel-release
[root@localhost ~]# yum -y install python-pip
[root@localhost ~]#yum install python-devel

```
**2，清除yum缓存**

```
[root@localhost ~]# yum clean all

```
**3，安装mycli**

```
[root@localhost ~]#  pip install mycli

```
执行完之后遇到了这样一个报错

```
You are using pip version 8.1.2, however version 18.0 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```
我们就可以去执行报告的这个命令

```
[root@localhost ~]#  pip install --upgrade pip
```
至此，mycli安装完成

**测试mycli**

```
[root@localhost张大帅哥 ~]# mycli -uroot -p111
(2003, "Can't connect to MySQL server on 'localhost' ([Errno 2] No such file or directory)")
Failed to connect by socket, retrying over TCP/IP
Version: 1.17.0
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Jonathan Slenders
mariadb root@localhost:(none)> 

```
登陆成功！！！
执行mysql命令即可有补全和提示功能！
