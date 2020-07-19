<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# Linux

- **Linux**是**基于Unix**操作系统的一种操作系统，但Unix是收费的，Linux是**开源免费**的
- Linux分为内核版本和发行版本，内核版本是开源免费的，发行版本是一些公司在内核版本的基础上推出的产品，有可能收费
- **Linux图形化界面不完善，但其开源免费、漏洞少，适用于服务器**
- 在电脑上安装Linux操作系统的方式：虚拟机或双系统
- 选择用虚拟机软件VMware安装CentOS，再用SecureCRT远程操控
- 使用命令行输入`ifconfig`可以获取当前虚拟机的IP地址，若不出现IP地址而是出现`ens33`，则：
```
解决方法如下：
    [root@localhost ~]# 	
    [root@localhost network-scripts]# vi ifcfg-ens33 ifcfg-ens33
    将ONBOOT设置为yes，:wq保存退出
	service network restart 重启网络连接
```
- 若无法ping通外网，则：
```
解决方法如下：
	vim /etc/sysconfig/network-scripts/ifcfg-ens33
	修改ONBOOT=yes
	service network restart 重启网络连接
	如果还不行就重启虚拟机
```
- 解决每次重启虚拟机时IP都随机分配而不固定的问题：[点击查看博客](https://blog.csdn.net/weiyongle1996/article/details/75050738)
- 解决虚拟机可以ping通主机，主机却ping不通虚拟机（导致SecureCRT也连不上虚拟机）的问题：[点击查看博客](https://blog.csdn.net/u013068789/article/details/80296260)
- 关闭防火墙：`service iptables stop`
- 网络服务：
 - 重启网络服务：`service network restart`
 - 开启网络服务：`service network start`
 - 关闭网络服务：`service network stop`

## Linux中的目录结构

- `/`代表系统的根目录
- 命令行前面的`~`代表当前用户的根目录（如root用户的根目录是`/root`）

## Linux常用命令

- 清屏：`clear`或`ctrl + L`
- 命令提示：`命令 --help`
- 显示当前目录路径：`pwd`
- 创建空文件：`touch`，如`touch a.txt`
- 文件列表（`ls`、`ll`）：
 - `ls` 查看当前目录下的文件
 - `ls -a` 查看当前目录下的所有文件（包括隐藏文件）
 - `ll`或`ls -l` 查看当前目录下文件及其详细信息
- 切换目录（`cd`）：
 - `cd 目录路径` 切换到指定目录（可用Tab智能补全）
 - `cd ..` 切换到上一层
 - `cd /` 切换到系统的根目录
 - `cd ~` 切换到当前用户的根目录
 - `cd -` 返回上一次所在目录
- 创建目录(`mkdir`)、删除目录（`rmdir`）：
 - `mkdir xxx` 创建目录xxx
 - `mkdir -p xxx/yyy` 创建多级目录aaa/bbb，若当前目录下不存在aaa目录，则会自动创建aaa目录
 - `rmdir xxx` 删除目录xxx（要求xxx为空目录）
- 查看文件（`cat`、`more`、`less`、`tail`）：
 - `cat xxx` 查看文件xxx（全部显示，如果文件过长，只能显示最后一屏）
 - `more xxx` 查看文件xxx（一屏一屏地显示，按回车下一行，按空格下一屏，按q或Ctrl+C退出）
 - `less xxx` 查看文件xxx（一屏一屏地显示，按上下键可以移动，按回车下一行，按空格下一屏，按q或Ctrl+C退出）
 - `tail -num xxx` 查看文件xxx的最后num行（例如查看日志时只想看最近的几条记录）
 - `tail -f xxx` 查看文件xxx的最后几行，且可以动态地显示文件的更新，按Ctrl+C退出
- 拷贝（`cp`）、移动（`mv`）、删除文件（`rm`）：
 - `cp a.txt ../b.txt` 将a.txt拷贝到上一层目录下并重命名为b.txt（若没有写新的文件名，则不更改文件名）
 - `mv a.txt ../b.txt` 将a.txt移动（剪切）到上一层目录下并重命名为b.txt（若没有写新的文件名，则不更改文件名）
 - `rm a.txt` 删除文件a.txt（不能删除非空目录）
 - `rm -r aaa` 递归删除文件aaa或目录aaa（可以删除非空目录）
 - `rm -f a.txt` 不询问删除文件a.txt（不能删除非空目录）
 - `rm -rf aaa` 不询问递归删除文件aaa或目录aaa（可以删除非空目录）
 - `rm -rf *` 删除所有文件
 - `rm -rf /*` 删除操作系统中所有文件，自杀！
- 打包、压缩、解压缩（常用参数：`-c`创建新的tar文件，`-v`显示运行过程信息，`-f`指定文件名，`-z`使用gzip压缩命令进行压缩，`-x`解开tar文件）：
 - `tar -cvf xxx.tar ./*` 将当前目录下所有文件打包成xxx.tar（未进行压缩）
 - `tar -zcvf xxx.tar.gz ./*` 将当前目录下所有文件打包并压缩成xxx.tar.gz
 - `tar -xvf xxx.tar` 解开xxx.tar
 - `tar -zxvf xxx.tar.gz -C ../yyy` 将xxx.tar.gz解压缩并解开到上一级目录的yyy目录下
- 查找文件（`find`）、查找内容（`grep`）：
 - `find -name ins*` 查找名称以"ins"开头的文件
 - `grep Adress a.txt --color -B2 -A3` 在a.txt中查找"Address"及其前面两行和后面三行，同时高亮显示keyword（后面三个参数可以省略）
- VIM文件编辑器：
 1. `vim a.txt`进入到a.txt中，是命令行模式，不能修改文件
 2. 按`i`，进入插入模式，可以修改文件
 3. 按`ESC`退出插入模式，回到命令行模式，不能修改文件
 3. 按`:`，进入底行模式，可以输入`wq`保存并退出、`q`退出、`q!`不保存强制退出等，还可以`/xxx`将文件中所有xxx高亮显示
- 重定向输出（`>`、`>>`）：
 - `ifconfig > a.txt` 将`ifconfig`命令本来要输出到命令行的内容输出到a.txt中（覆盖）
 - `cat a.txt >> b.txt` 将`cat a.txt`命令本来要输出到命令行的内容追加到b.txt中（追加）
- 管道（`|`）：将前面命令的输出作为后面命令的输入，可以将命令进行组合使用，例如：
 - `ifconfig | more` 通过`more`方式查看`ifconfig`命令输出的内容
- 系统命令（`ps`、`kill`）：
 - `ps -ef` 查看所有进程
 - `ps -ef | grey ssh` 查找名称包含ssh的进程
 - `kill 2888` 杀死编号为2888的进程
 - `kill -9 2888` 强制杀死编号为2888的进程

### 主机名配置

- `hostname` 查看主机名
- `hostname xxx` 修改当前主机名为xxx，但不会做持久化（即重启后失效）
- `hostnamectl set-name xxx` 修改主机名为xxx（永久生效）

### 域名映射

- Linux中的`/etc/hosts`文件相当于Windows中的`C:\Windows\Systems32\drivers\etc\hosts`文件

## Linux权限

- 使用`ll`命令查看目录内容，第一行就是表示每个文件的权限，它有10个字段，分为4组：
```
Linux权限分为四组："- --- --- ---"
 1. 第1位表示文件类型：-表示文件，d表示目录，l表示链接（相当于Windows中的快捷方式）
 2. 第2~4位表示当前用户对该文件/目录所具有的权限：r表示可读，w表示可写，x表示可执行
 3. 第5~7位表示当前组内其他用户对该文件/目录所具有的权限：r表示可读，w表示可写，x表示可执行
 4. 第8~10位表示其他组的用户对该文件/目录所具有的权限：r表示可读，w表示可写，x表示可执行
```
例如`drwxr-xr-x`，表示类型为文件夹，当前用户可读可写可执行，组内其他用户不可写但可读可执行，其他组用户不可写但可读可执行
- 修改权限的命令（`chmod`）：
 - `chmod u=rwx,g=rx,o=rx a.txt` 将a.txt的权限修改为当前用户可读可写可执行，组内其他用户不可写但可读可执行，其他组用户不可写但可读可执行
 - **`chmod 755 a.txt` 用二进制表示权限**，如7表示111，即可读可写可执行，5表示101，即可读不可写可执行，从左到右三个数分别代表当前用户、组内其他用户、其他组用户的权限

## Linux系统中文件上传和下载方式

###### 方式一：FileZilla

- 在主机上安装FileZilla，连接虚拟机，拖曳就可以进行上传和下载

###### 方式二：lrzsz

1. 在Linux上安装lrzsz，使用yum安装：`yum install lrzsz`
1. 然后在SecureCRT的`Session Options > Terminal > X/Y/Zmodem`查看和设置上传文件夹和下载文件夹
2. `rz`，在弹出的框中选择文件，就可以上传到Linux的当前目录下
3. `sz 文件名`可以将Linux上的文件下载到下载文件夹中

###### 方式三：SFTP（推荐，好用且安全）

1. 在SecureCRT上按Alt+P进入SFTP传输窗口
2. `put 文件路径`可以将文件上传到Linux
3. `get 文件路径`可以将Linux上的文件下载到主机
4. `pwd`可以查看上传文件会到达的目录（Linux上的目录，默认是当前用户的根目录）
5. `lpwd`可以查看下载文件会到达的目录（主机上的目录，默认是`C:\Users\Cris_JiangDT\Documents`）

## Linux安装JDK

- Linux安装JDK步骤：[点击查看博客](https://blog.csdn.net/qq_42815754/article/details/82968464)
- 手动安装：
 1. Oracle官网下载JDK（tar.gz格式），上传到Linux，一般放到`/usr/local/jdk`中，然后`tar -zxvf`进行解压
 2. 添加环境变量：在`/etc/profile`中添加：
```
# set java enviroment
JAVA_HOME=/usr/local/jdk/jdk-11.0.7
CLASSPATH=.:$JAVA_HOME/lib.tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH
```
其中JAVA_HOME的值应和Linux中JDK解压的位置相同
 3. `source /etc/profile`使更改的配置生效
 4. `java -version`查看JDK是否安装成功

## Linux安装MySQL

- Linux安装MySQL及设置允许远程连接步骤：[点击查看博客](https://blog.csdn.net/wohiusdashi/article/details/89358071)
- 设置了远程连接后，可以在主机的命令行通过`mysql -h指定IP地址 -u用户名 -p`来连接虚拟机上的MySQL（注意需要开启虚拟机的MySQL服务以及设置允许远程连接）

## Linux安装Tomcat

1. 在Apache官网上找到要下载的Tomcat的.tar.gz文件链接，Linux中`wget 链接`进行下载
2. `tar -zxvf 文件`进行解压
3. 进入bin目录，`./startup.sh`执行开启操作
4. 在主机浏览器输入`虚拟机IP:8080`进行访问

## Linux安装Redis

- Linux安装Redis步骤：[点击查看博客](https://blog.csdn.net/qq_30764991/article/details/81564652)
1. Redis是C语言开发的，需要安装C语言的编译环境：`yum install gcc-c++`
2. 在Redis官网上找到要下载的Redis的.tar.gz文件链接，`wget 链接`进行下载
3. `tar -zxvf 文件`进行解压
4. `cd`进入解压得到的目录（如redis-5.0.4），然后`make`编译
5. 安装，`make install PREFIX=/usr/local/redis`，其中PREFIX指定安装路径
6. 将解压得到的目录（如redis-5.0.4）中的redis.conf配置文件复制到安装路径下的bin目录中
7. 进入Redis安装路径下的bin目录，`./redis-server`执行Redis服务端进行启动
8. 克隆对话，进入Redis安装路径下的bin目录，`./redis-cli`执行Redis客户端进行启动
9. 关闭Redis的方式：进入Redis安装路径下的bin目录，`./redis-cli shutdown`进行关闭

## 将项目部署到Linux上

1. 首先要将项目中的绝对路径都改为相对路径，并将主机上的数据库导入到Linux上的数据库
2. 将项目打成war包，并上传到Linux的Tomcat的webapps目录下
3. 启动Linux的Tomcat
4. 可以在主机浏览器访问Linux部署的项目
5. 可以在`Tomcat安装目录 > logs > catalina.out`查看日志（即控制台的输出）（用`tail -f catalina.out`命令可以实时显示）


# Nginx

- Nginx是一个**高性能的HTTP和反向代理web服务器**，同时也提供了IMAP/POP3/SMTP服务，**支持高并发**
- Nginx应用场景：
 - **HTTP服务器**：Nginx可以独立提供HTTP服务，可以做**网页静态服务器**
 - **反向代理，负载均衡**：若网站访问量高，单个服务器（如Tomcat）无法承受，就需要多个服务器，但又要保证相同的域名，就可以用Nginx做多台服务器集群的反向代理，并且可以在多台服务器之间实现负载的均衡化
 - 虚拟主机：Nginx可以实现在一台服务器上虚拟出多个主机，可部署多个静态网站

## Linux安装Nginx

- Linux安装Nginx步骤：[点击查看博客](https://www.cnblogs.com/xxoome/p/5866475.html)
1. 需要安装C语言的编译环境：`yum install gcc-c++`（若已安装过，可跳过）
2. 安装Nginx所依赖的环境：`yum install pcre-devel、zlib-devel、openssl-devel`
3. 在Nginx官网下载.tar.gz文件，并上传到Linux，解压
4. 进入Nginx安装目录（里面有名为configure的可执行文件），配置`./configure --prefix=/usr/local/nginx/nginx-1.18.0`（其中prefix后面是安装目录），成功后，Nginx安装目录下会出现MakeFile文件夹
5. `make`进行编译
6. `make install`进行安装
7. 在安装目录下创建logs文件夹`mkdir logs`
8. 启动Nginx，在安装目录下的sbin目录下执行`./nginx`，在主机浏览器中输入Linux的IP地址即可（Nginx的默认端口号就是80），若无法打开，则可能是因为服务器的80端口未打开
```
执行"firewall-cmd --query-port=80/tcp"，显示no，说明服务器的80端口未打开，则开启80端口：
firewall-cmd --add-port=80/tcp --permanent
#重启防火墙
systemctl restart firewalld
```
然后刷新浏览器即可
9. `./nginx -s stop`或者`./nginx -s quit`关闭Nginx，`./nginx -s reload`重启Nginx

### Nginx的静态资源部署功能

- Nginx可以做网页静态服务器：
 - 将静态网页资源上传到Linux服务器`Nginx安装目录 > html目录`
 - 到`Nginx安装目录 > sbin`下`./nginx -s reload`重启Nginx
 - 可在浏览器访问部署的静态资源

### Nginx的虚拟主机功能

- Nginx可以在一台服务器上虚拟出多个主机，修改`Nginx安装目录 > conf > nginx.conf`里设置多个`server{}`内容，通过配置端口号、主机名和默认访问的资源，来实现一台服务器上虚拟多台主机，部署多个静态资源，例如：
```
    server {
        listen       80;	# 端口号
        server_name  localhost;	# 主机名或域名

        location / {
            root   html;	# 静态资源目录
            index  index.html index.htm;	# 默认访问的静态资源
        }

        error_page   500 502 503 504  /50x.html;	# 错误页面
        location = /50x.html {
            root   html;
        }
    }
	
	# 通过Nginx虚拟多台主机
	server {
        listen       81;
        server_name  myServerName;

        location / {
            root   xxx;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```
这样就可以只用一台服务器的情况下，通过不同的域名或端口号访问不同的资源，如`localhost:80`会访问`Nginx安装目录 > html目录`中的index.html，而`myServerName:81`会访问`Nginx安装目录 > xxx目录`中的index.html（域名需要购买，或在`/etc/hosts`中配置）

### Nginx的反向代理与负载均衡功能

#### 反向代理

- **正向代理是代理客户端**，比如VPN，多个用户可以访问同一个主机IP，再由该主机来连接外网
![](.\MyPic\proxy.bmp)
- **反向代理是代理服务端**，比如Nginx反向代理服务器集群
![](.\MyPic\reverseProxy.bmp)

- 配置反向代理，在`Nginx安装目录 > conf > nginx.conf`配置文件中进行配置，添加`upstream xxx{}`，并将需要反向代理的资源的默认静态资源目录改为该反向代理，例如：
```
	# 配置反向代理
	upstream myReverseProxy {
		server localhost:82;
	}
	
	server {
        listen       82;
        server_name  localhost;

        location / {
			# 将默认静态资源目录改为上面配置的反向代理
            # root   html;
			proxy_pass http://myReverseProxy;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```
重启Nginx后，通过端口号82访问的静态资源，就会被加上反向代理

#### 负载均衡

- 服务器集群中，反向代理根据每台服务器性能等的差异，要均衡每台服务器的负载（例如若所有服务器性能相同，则应尽可能保证每台服务器被访问的几率相同），这就叫做负载均衡
- Nginx配置负载均衡，在`Nginx安装目录 > conf > nginx.conf`配置文件中进行配置，在反向代理中添加的`upstream xxx {}`中添加多个服务器IP/域名即可，还可以配上权重weight（默认是1）来设置每个服务器被访问的几率，例如：
```
	# 配置负载均衡
	upstream myReverseProxy {
		server 192.168.119.13 weight=2;	# 50%几率
		server 192.168.119.14;	# 25%几率
		server 192.168.119.15;	# 25%几率
	}

	server {
        listen       82;
        server_name  localhost;

        location / {
			# 将默认静态资源目录改为上面配置的反向代理
            # root   html;
			proxy_pass http://myReverseProxy;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```
重启Nginx后，通过82端口访问服务器时，通过反向代理的负载均衡，配置的三个服务器会随机选择一个访问，且概率由权重决定