**数据库的作用是什么？**

数据库，见名知意，也就是存放数据的仓库，就像你的电话本一样，存放着联系人的数据，而数据库存储的数据比电话本多的多了，并且是在服务器上的数据存储，我们平时玩的游戏每个账号的等级，装备什么的，各种数据全都是在数据库中存储着，我们登录账号的实质就是访问数据库，所以这个数据很重要，当服务器出问题坏掉了，我们的数据库中的数据可不能丢失，这也就需要我们去做

----------


**数据库的备份**

为什么要做数据库的备份？这个问题显得很捞，这么重要的东西，怎么能只有这一份呢？

模拟工作环境中数据库的备份，在工作环境中我们的服务器可不是说停就停的，所以我们要做的就是在线备份，在不停服务器的情况下完成数据库的备份，也就是所说的热备。

需要机器，两台，一台服务器，一台作远程存储的机器

- 服务器	    172.18.254.136     
- 远程存储   172.18.154.103

## 服务器端设置 ##

**1，安装mariadb数据库,并启动服务**

    [root@localhost张大帅哥 ~]# yum install -y mariadb-server
    [root@localhost张大帅哥 ~]# systemctl start mariadb

**2，编辑主配置文件**

    [root@localhost张大帅哥 ~]# vim /etc/my.cnf
添加一行配置来启动二进制日志，在这里我把数据文件的目录也改了，都在同一个目录下，一会备份了好去找。默认在/var/lib/mysql下。
    
    [mysqld]
    datadir=/mariadb/data
    log_bin=/mariadb/logbin/log-bin
这里log_bin=表示开启二进制日志并且日志文件存放在/mariadb/logbin下，格式为log-bin.0000X

**4,创建所需的两个目录并设置所属人所属组**
    
    [root@localhost张大帅哥 ~]# mkdir -p /mariadb/{data,logbin}
    [root@localhost张大帅哥 ~]# chown -R mysql.mysql /mariadb/
**5,在数据库中增加两张表**

    MariaDB [(none)]> create database db1;
    Query OK, 1 row affected (0.00 sec)
    
    MariaDB [(none)]> create database db2;
    Query OK, 1 row affected (0.00 sec)
**6，备份数据**

    [root@localhost data]# mysqldump -A -F --single-transaction --master-data=2 > /mariadb/bakup/fullbak_$(date +%F_%T).sql
执行这条命令生成备份文件并导至bakup目录下设置名字

**7，生成新数据**

因为是模拟生产环境，所以数据库中可能会一直有数据的更改，所以，我们这时候再对数据库做一下更改

    MariaDB [(none)]> use db1;
    Database changed
    MariaDB [db1]> create table t1;
    ERROR 1113 (42000): A table must have at least 1 column
    MariaDB [db1]> create table t1(id tinyint);
    Query OK, 0 rows affected (0.01 sec)
我们在数据库db1中创建了一张t1表

**8，查看产生的新数据是从哪个二进制文件开始的**

     [root@localhost张大帅哥 ~]# vim /mariadb/bakup/fullbak_2018-10-11_07\:01\:16.sql
    -- CHANGE MASTER TO MASTER_LOG_FILE='log-bin.000004', MASTER_LOG_POS=245;
由文件中的这一行可以看出我们的备份这一刻的数据是记录在了04的二进制日志文件中，所以我们往远程备份的时候要把这一个二进制文件也备份过去

**9，备份到远程**

    [root@localhost张大帅哥 ~]# scp /mariadb/bakup/fullbak_2018-10-11_07\:01\:16.sql /mariadb/logbin/log-bin.000004 root@172.18.254.103:/root/mysqlbakup

**10，如果服务器出问题，数据库数据坏了，我们就可以用这两个文件来恢复，数据文件可以直接导入，二进制文件需要经过以下转换才能导入**

    [root@localhost张大帅哥 ~]# mysqlbinlog /root/mysqlbakup/log-bin.000004 > /root/bin.sql

这样把两个sql文件导入数据库即可。



至此，我们模拟数据库的备份与恢复就完成了，生产环境中我们可以写计划任务来每天备份数据库文件，这种热备不用停机即可完成备份。还有一个基于lvm快照的几乎热备的备份功能，在此篇博文中不再介绍，如需了解，请留言。