首先来看看什么是[lamp](https://baike.baidu.com/item/lamp/66952?fr=aladdin "lamp")

什么是[wordpress](https://baike.baidu.com/item/WordPress/450615?fr=aladdin](https://baike.baidu.com/item/WordPress/450615?fr=aladdin "WordPress")
今天来说说如何基于lamp来搭建一个个人博客网站，当然需要用到的是wordpress，还要有数据库来存储数据，有http服务去支持，以及php和数据库的连接。下面来说一下过程



----------



**环境：CentOS7**

**IP：172.18.254.103**

**1，安装包，取消防火墙，关闭selinux**
    
    [root@localhost ~]# yum -y install httpd php mariadb-server php-mysql
    [root@localhost ~]# systemctl stop firewalld
    [root@localhost ~]# setenforce 0
    [root@localhost ~]# vim /etc/selinux/config 
    SELINUX=disabled

**2，创建虚拟主机**

    [root@localhost ~]# vim /etc/httpd/conf.d/blog.conf
    	<VirtualHost *:80>
    	ServerName blog.zhang.com
    	DocumentRoot "/vhost/blog/htdocs"
    	CustomLog "logs/blog.zhang.com_access_log" combined
    	<Directory "/vhost/blog/htdocs">
    	Require all granted
    	</Directory>
    	</VirtualHost>

**3，编辑mariadb配置文件**

    [root@localhost ~]# vim /etc/my.cnf
    
    [mysqld]
    datadir=/var/lib/mysql
    socket=/var/lib/mysql/mysql.sock
    skip_name_resolve

**4，创建目录并下载wordpress压缩包**

    [root@localhost ~]# mkdir -p /vhost/blog
    [root@localhost ~]# cd /vhost/blog/
    [root@localhost blog]# wget https://cn.wordpress.org/wordpress-4.9.4-zh_CN.tar.gz
    [root@localhost blog]# ls
    wordpress-4.9.4-zh_CN.tar.gz
    [root@localhost blog]# tar -xvf wordpress-4.9.4-zh_CN.tar.gz
    [root@localhost blog]# ln -sv wordpress htdocs
    [root@localhost blog]# setfacl -m u:apache:rwx htdocs/

**5，重启mariadb服务并创建数据库后授权**

    [root@localhost ~]# systemctl restart mariadb
    [root@localhost ~]# mysql
    MariaDB [(none)]> create database blog;
    Query OK, 1 row affected (0.00 sec)
    
    MariaDB [(none)]> grant all on blog.* to 'wpuser'@'172.18.254.%' identified by 'wppass';
    Query OK, 0 rows affected (0.00 sec)

**6，重启httpd服务，并在windows下编辑hosts文件**

    [root@localhost ~]# systemctl restart httpd

在windows下编辑C盘：/windows/System32/drivers/etc/hosts文件

添加一行：172.18.254.103  blog.zhang.com

保存退出，如果不能保存的话，请设置文件属性中的安全选项使文件可写。

**7，最后即可在浏览器中访问blog.zhang.com**

这样根据网页内容显示设置之后，个人博客网站即搭建完毕。