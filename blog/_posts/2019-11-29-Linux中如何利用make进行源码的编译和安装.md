写这篇文章仅仅来解释make工具的源码编译功能，不去涉及gcc， 不去涉及[为什么源码编译安装软件](https://blog.csdn.net/qq_37392757/article/details/77526892)。
首先，make是一个编译程序，但是这仅仅是一个程序，不能适用于所有的内核，所以在编译不同的源码的时候需要不同的规则来编译，而这个规则写在Makefile文件当中，当使用make的时候，make会使用该目录下的Makefile作为参数配置文件，来进行make的编译操作，听起来好像很简单，那么来看一下详细步骤吧


----------
1.首先取得源文件（在这里我们以apache的配置为例）

```
[root@localhost张大帅哥 ~]# ls
anaconda-ks.cfg  httpd-2.2.34.tar.bz2  公共  视频  文档  音乐
bin              initial-setup-ks.cfg  模板  图片  下载  桌面

```
我这里有一个源码包，先把它移动到/usr/local/src/下进行压缩

```
[root@localhost张大帅哥 ~]# mv httpd-2.2.34.tar.bz2 /usr/local/src/
[root@localhost张大帅哥 ~]# cd /usr/local/src/
[root@localhost张大帅哥 src]# ls
httpd-2.2.34.tar.bz2

```
2.解压这个[tarball](https://baike.baidu.com/item/tarball/6505617)
 

```
[root@localhost张大帅哥 src]# tar -xvf httpd-2.2.34.tar.bz2 
[root@localhost张大帅哥 src]# ls
httpd-2.2.34  httpd-2.2.34.tar.bz2
```
3.进入新建的目录下，查阅INSTALL和README相关文件，一般情况下我们拿到源码包之后都会先去查阅这两个文件，因为这两个文件就相当于说明书,查看之后安装相关软件（也可以在下一步用configure来检测后提示安装）

```
[root@localhost张大帅哥 src]# cd httpd-2.2.34/
[root@localhost张大帅哥 httpd-2.2.34]# ls
ABOUT_APACHE  CHANGES        httpd.dsp       libhttpd.dep  NOTICE            server
acinclude.m4  config.layout  httpd.mak       libhttpd.dsp  NWGNUmakefile     srclib
Apache.dsw    configure      httpd.spec      libhttpd.mak  os                support
build         configure.in   include         LICENSE       README            test
BuildAll.dsp  docs           INSTALL         Makefile.in   README.platforms  VERSIONING
BuildBin.dsp  emacs-style    InstallBin.dsp  Makefile.win  README-win32.txt
buildconf     httpd.dep      LAYOUT          modules       ROADMAP

```
4.建立Makefile：在这里Makefile文件并不需要我们自己去编辑，configure（自动检测程序）会检测操作环境，同时建立Makefile文件，可指定安装目录


```
[root@localhost张大帅哥 httpd-2.2.34]# ./configure --prefix=/app/apache22

```
5.编译：使用make，它便会自动检测此目录下的Makefile文件,然后按照规则进行编译

```
[root@localhost张大帅哥 httpd-2.2.34]# make

```
6.安装：make会依据Makefile文件中的安装选项进行安装

```
[root@localhost张大帅哥 httpd-2.2.34]# make install

```
这样编译安装就完成了。（我的是安装到了/app目录下）
我们可以进入/app/apache22中来开启apache服务，首先需要在/etc/profile.d下创建.sh文件，写入export PATH=$PATH:/app/apache22/bin，source读取并执行这个文件然后再进
入/app/bin/apache22/bin下开启服务

```
[root@localhost张大帅哥 profile.d]# vim title.sh
[root@localhost张大帅哥 profile.d]# source title.sh 
[root@localhost张大帅哥 bin]# apachectl start
```
打开浏览器输入ip即可查看显示内容(记得查看之前清除防火墙)，浏览器显示内容可在/app/apache22/htdocs/index.html 中进行设置

