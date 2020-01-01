这两天在研究vim，发现这个东西确实是很强大的，随着版本的不断更新，支持的功能也越来越多，我们可能把vim升级后也没发现哪里好用了，跟以前感觉差不多，前几天一起学习的有人把vim更新到了8.1最新版本，但是看起来步骤还是比较麻烦的，我也试着来了一下，经过几次尝试成功更新到了8.1。在这里把这篇博客分享给大家，有想更新到8.1的可以去试试。

----------

[vim8.1的安装](https://www.cnblogs.com/FrancisDrakeK/p/9439489.html)

这篇博客中只介绍了安装8.1，8.1版本跟7.4版本看起来没什么不一样。在这里我给大家介绍一个升级vim的方法，这里只能升级到8.0，但我感觉够用了。
接下来就来整吧。
首先需要来配置yum update的yum源

```
[root@localhost张大帅哥 ~]# curl -L https://copr.fedorainfracloud.org/coprs/mcepl/vim8/repo/epel-7/mcepl-vim8-epel-7.repo -o /etc/yum.repos.d/mcepl-vim8-epel-7.repo
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   306  100   306    0     0     89      0  0:00:03  0:00:03 --:--:--    89

```
curl是一个利用URL是利用URL语法在命令行方式下工作的开源文件传输工具。

```
[root@localhost张大帅哥 ~]# yum update -y vim*

```
OK！！！搞定，是不是很简单
![这里写图片描述](https://img-blog.csdn.net/20180823205901674?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
仅仅升级到8.0当然不能显示出vim的强大，强大之处在于vim的插件功能，版本高支持的插件功能就变多了，插件功能也越来越强大了。今天闲来简单安装一款非常**适合于写shell脚本的插件**
这个插件叫做bash-support，这个插件适合去编写bash脚本，会大大提高写脚本的速率。这个插件只识别以.sh结尾的脚本文件，所以我们在编辑脚本的时候后面带上.sh就能直接使用这款插件了。
下面我们开始吧
首先我们要下载插件的压缩包，在这里我为大家准备好啦。[百度网盘](%E9%93%BE%E6%8E%A5%EF%BC%9Ahttps://pan.baidu.com/s/1PYMbyNAsoDqMT4_AGFWl7g)提取码：p2wc
首先我们需要取得这个插件
从windows下把下载好的压缩包拿到linux下

```
[root@localhost张大帅哥 ~]# rz
```
如果没有就yum install lrzsz安装。
新建一个~/.vim文件夹，把压缩包移动到这里解压

```
[root@localhost张大帅哥 ~]# mkdir ~/.vim
[root@localhost张大帅哥 ~]# mv bash-support.zip ~/.vim
[root@localhost张大帅哥 ~]# cd ~/.vim
[root@localhost张大帅哥 .vim]# unzip bash-support.zip 
```
完成到这一步其实已经差不多了，接下来就是激活它，在家目录下创建.vimrc文件，并向其中写入 filetype plugin on，保存退出

```
[root@localhost张大帅哥 ~]# vim ~/.vimrc

```
然后我们就可以使用插件功能了，我们来试一下。

```
[root@localhost张大帅哥 ~]# vim test.sh
```
![这里写图片描述](https://img-blog.csdn.net/20180824080834435?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

出现这样的文件头，我们可以自己设置文件头的内容，因为这里的东西采用的都是键位映射，这里的键位映射采用的都是(\+字母)组成，而这个跟打字速度有关，如果需要，就要在三秒内打完。常用的键位映射我会在最后跟大家说，这里先介绍改文件头，输入\ntw回车，之后选择1，回车，再次选择1，回车就会进入这样的界面
![这里写图片描述](https://img-blog.csdn.net/20180824210848304?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
在这里的单引号中写入你自定义的东西然后Esc，qw跟vim的操作一样退出就行了。然后再一个新的脚本中打开查看，记得要在另一个新的脚本中打开查看哦。打开之后就能看到设置的效果了。
![这里写图片描述](https://img-blog.csdn.net/20180824212325483?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



下面为大家说一下这个插件最强大的功能。
如果我想插入一块注释信息，那么就可以键入\cfr,然后在这个注释块中编写信息
![这里写图片描述](https://img-blog.csdn.net/20180824212404684?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2Zlc3Nvcm1hbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
下面是一些用于插入语句的键映射（n – 普通模式, i – 插入模式，v 可视模式）
\sc – case in … esac （n, i）
\sei – elif then （n, i）
\sf – for in do done （n, i, v）
\sfo – for ((…)) do done （n, i, v）
\si – if then fi （n, i, v）
\sie – if then else fi （n, i, v）
\ss – select in do done （n, i, v）
\su – until do done （n, i, v）
\sw – while do done （n, i, v）
\sfu – function （n, i, v）
\se – echo -e "…" （n, i, v）
\sp – printf "…" （n, i, v）
\sa – 数组元素, ${.[.]} （n, i, v） 和其它更多的数组功能。
下面是一些运行操作键映射的列表：
\rr – 更新文件，运行脚本（n, i）
\ra – 设置脚本命令行参数 （n, i）
\rc – 更新文件，检查语法 （n, i）
\rco – 语法检查选项 （n, i）
\rd – 启动调试器（n, i）
\re – 使脚本可/不可执行(*) （n, i）
使脚本可执行
编写完脚本后，保存它然后输入 \re 和回车键使它可执行。
要显示帮助，在普通模式下输入：
\hh – 内建帮助
\hm – 命令帮助
更多参考资料，可以查看文件：
~/.vim/doc/bashsupport.txt 和
~/.vim/doc/tags
这是一个非常好的东西，相信在经过一段时间的熟悉之后，能熟练使用的时候，编写脚本的速度会大大提升的，因为其强大，在这里我就不再一一列举其功能了。只能靠你们自己去慢慢摸索了哦。

最后跟大家说一个改变vim主题的方式，一般来说，我们的vim配色跟终端的配色是一样的，要怎么不一样呢，在这里vim有自己的主题

```
[root@localhost张大帅哥 etc]# cd /usr/share/vim/vim80/colors/
[root@localhost张大帅哥 colors]# ls
blue.vim      desert.vim    koehler.vim  peachpuff.vim  slate.vim
darkblue.vim  elflord.vim   morning.vim  README.txt     tools
default.vim   evening.vim   murphy.vim   ron.vim        torte.vim
delek.vim     industry.vim  pablo.vim    shine.vim      zellner.vim

```
在这个目录下以.vim结尾的都是vim的主题配色，我们可以通过在/etc/vimrc中更改主题名来更改vim主题，在这里我用的主题是叫darkblue

```
[root@localhost张大帅哥 ~]# vim /etc/vimrc

```
在/etc/vimrc文件中添加一行信息       colorscheme darkblue
后面的darkblue就是主题名，上面说过了，可以换成自己喜欢的颜色

