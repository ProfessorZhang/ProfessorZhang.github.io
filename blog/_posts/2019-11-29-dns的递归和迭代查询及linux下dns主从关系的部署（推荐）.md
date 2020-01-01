
**什么是dns**
**dns存在的意义**
**dns解析的过程及原理**
**dns主从关系的部署**

----------
**什么是dns**
Domain Name Server的缩写，就是域名服务器的意思，域名包括域名服务器和域名解析器，域名是什么呢？我们平时在浏览器中输入的baidu.com就是域名，而www是主机名，www.baidu.com叫做完整主机名（FQDN）。dns是一个分布式，分层式的主机名管理架构，也可以说是一个数据库，这个数据库管理的就是各个域名对应的IP，在我们访问域名的时候就能直接访问到对应的IP。
**dns存在的意义**
为什么要有dns呢，因为我们知道，ip地址很难记，如果你想访问百度，或者淘宝，让你输入一大串ip地址，这样就显得很难受，并且我们也记不住那么多的ip，所以随之域名这个概念就有了，让我们就不需要去记ip了，只需要输入域名就可以访问互联网了。
**dns解析的过程及原理**
首先来说一下域名服务器，其实在我们平时输入的域名最后还有一个“.”，就是这样的baidu.com.  这最后一个点就是dns的根服务器，世界上一共有13台根服务器，一个为主根服务器在美国。其余12个均为辅根服务器，其中9个在美国，欧洲2个，位于英国和瑞典，亚洲1个位于日本。一般我们都是不写的，再说，域名分等级，有顶级域名，一级域名，二级域名等，可以在百科里了解更清楚，[域名级别](https://baike.baidu.com/item/%E5%9F%9F%E5%90%8D%E7%BA%A7%E5%88%AB/15536218?fr=aladdin)
![这里写图片描述](https://img-blog.csdn.net/20180913155611232?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这里就是这种结构。
接下来说一下域名查询的类型，递归查询和迭代查询（或者叫解析）
![这里写图片描述](https://img-blog.csdn.net/20180916115533533?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

上面(b)这种查询就是递归查询，首先，比如我们要去访问www.163.com
（1）本地客户机先向本地服务器进行递归查询
（2）本地域名服务器先在自己的hosts文件中查看有没有，如果没有，则会像根服务器进行递归查询，就是在问我这没有www.163.com的ip，你那有吗？然后根服务器回答我这没有www.163.com的ip，但是我知道二级域名服务器那可能有，并且在同时去通知一级域名服务器
（3）同时一级域名服务器及二级域名服务器都做跟服务器一样的操作，直到到达权威dns服务器，权威服务器把IP返回给本地域名服务器，本地域名服务器把ip提供给客户机，同时在本地缓存一份，这样就实现了解析

这种解析过程可以看到这种解析过程会导致根服务器压力过大，所以，迭代就很好的减少了根服务器的压力,跟上面的（a）是一样的
![这里写图片描述](https://img-blog.csdn.net/20180914191905686?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
就像这种就是迭代，首先客户机到本地域名服务器的递归查询，然后本地域名服务器会去询问根服务器，根服务器会告诉本地服务器.com的ip，然后本地域名服务器会再去询问顶级域名服务器，如此循环往下一级一级去询问，最后问到了权威dns‘服务器，得到了www.163.com的IP，返回给客户机。

我们平时用的联通宽带什么的，在访问某个域名的时候，其实就是一个这样的递归和迭代的过程，而主从关系又是怎么回事呢？？
正如名字所描述的一样，“主”和“从”的关系，在正常的环境中是，dns服务器并不是不会坏的，如果dns服务器坏了会怎么样？？那我们将面临“断网”，所以，我们需要“备份”，这里所说的备份也就是所谓的主从关系，前面说到的那十三台根服务器就是主从关系。下面我们通过在linux环境下搭建一个主从关系的dns服务器来了解这个东西。
在这里需要两台机器，在这里我就说是a机器和b机器了
首先在a机器上搭建主dns服务器。
安装bind
```
yum -y install bind
```
编辑配置文件

```
[root@localhost张大帅哥 ~]# vim /etc/named.conf 
	
options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };

```
配置文件中只需要修改两处，就是那两个any，然后编辑zone文件，也就是配置文件末尾指到的那个        include "/etc/named.rfc1912.zones"

```
[root@localhost张大帅哥 ~]# vim /etc/named.rfc1912.zones
```
在文件中我们复制其中的一个zone，并修改

```
zone "zhang.com" IN {
        type master;
        file "zhang.com.zone";
};
```
我的是这样写的，最后一条我们不要，这里我们起的一个域名就是zhang.com，（本人姓张^_^），指定的域名的数据文件是zhang .com.zone，接下来就去配置数据文件了。

```
[root@localhost张大帅哥 ~]# cd /var/named/
[root@localhost张大帅哥 named]# cp -p named.localhost zhang.com.zone 

```
复制一份模板文件然后修改，注意复制的时候一定要加-p选项，保留原文件的权限。接下来修改

```
[root@localhost张大帅哥 named]# vim zhang.com.zone
		
$TTL 1D
@       IN SOA  @ mail.zhang.com. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
zhang.com.      NS      ns1.zhang.com.
ns1     A       192.168.188.7
www     A       192.168.188.6

```
我的配置文件这样写的，主要就是下面的指向
NS 是nameserver的缩写，也就是dns服务器的缩写，后面跟的是dns服务器
A 是Address的缩写，后面记录的是ipv4地址
这几条记录的意思就是，将域名zhang.com域名指向ns1.zhang.com，ns1代表的就是ns1.zhang.com，指向192.168.188.7的ip，而www指的就是www.zhang.com，指向192.168.188.6（本机是192.168.188.7）


----------


之后来做从dns
在b机器上
前两步跟上面一样

安装bind
```
yum -y install bind
```
编辑配置文件

```
[root@localhost张大帅哥 ~]# vim /etc/named.conf 
	
options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };

```
然后来编辑   include "/etc/named.rfc1912.zones"

```
[root@localhost张大帅哥 ~]# vim /etc/named.rfc1912.zones
```

```
	zone "zhang.com" IN {
		type slave;
		masters { 192.168.188.7; };
		file "slaves/zhang.com.ZONE";
	};
```
跟上面不同的是 类型为slave（仆人），指定自己的master（主人），在这里要说的是，其实这种主从关系就是从主哪里下载数据库文件，也就是我们在主上编辑的/var/named下的文件，这样实现备份。file就是指定下载的文件放在那个目录下取名什么。

最后我们重启服务
a上

```
[root@localhost张大帅哥 named]# systemctl restart named

```
然后去b上，进入/var/named/slave目录下，本来什么东西都没有，这时候我们启动b的服务

```
[root@localhost ~]# cd /var/named/slaves/

[root@localhost slaves]# service restart named
```
（我用的a是centos7，b是centos6，所以重启服务的命令不相同，小伙伴在操作时要注意）
然后我们再看slave下就有文件了。这个文件便是a上的数据库文件。

```
[root@localhost slaves]# ls
zhang.com.ZONE
[root@localhost slaves]# cat zhang.com.ZONE
$TTL 1D
@	IN SOA	@ mail.zhang.com. (
					0	; serial
					1D	; refresh
					1H	; retry
					1W	; expire
					3H )	; minimum
zhang.com.	NS	ns1.zhang.com.
ns1	A	192.168.188.7
www	A	192.168.188.6

```

跟主dns的一样，至此，dns主从关系的服务器搭建完成。
