## 环境
Linux CentOS6.8 

## 安装服务
### yum方式安装
 
#### 1、安装Gearman服务
```
[root@xm ~]# yum install -y boost-devel gperf libevent-devel libuuid-devel
[root@xm ~]# yum install -y gearmand  
[root@xm ~]# gearman

gearman	Error in usage(No Functions were provided).

Client mode: gearman [options] [<data>]
Worker mode: gearman -w [options] [<command> [<args> ...]]

Common options to both client and worker modes.
	-f <function> - Function name to use for jobs (can give many)
	-h <host>     - Job server host
	-H            - Print this help menu
	-v            - Print diagnostic information to stdout(false)
	-p <port>     - Job server port
	-t <timeout>  - Timeout in milliseconds
	-i <pidfile>  - Create a pidfile for the process

Client options:
	-b            - Run jobs in the background(false)
	-I            - Run jobs as high priority
	-L            - Run jobs as low priority
	-n            - Run one job per line(false)
	-N            - Same as -n, but strip off the newline(false)
	-P            - Prefix all output lines with functions names
	-s            - Send job without reading from standard input
	-u <unique>   - Unique key to use for job

Worker options:
	-c <count>    - Number of jobs for worker to run before exiting
	-n            - Send data packet for each line(false)
	-N            - Same as -n, but strip off the newline(false)
	-w            - Run in worker mode(false)
//启动服务
[root@xm ~]# /etc/rc.d/init.d/gearmand start
正在启动 gearmand：                                        [确定]
[root@xm ~]# ps aux | grep gearman
gearmand 18930  0.0  0.2 483672  2164 ?        Ssl  15:37   0:00 /usr/sbin/gearmand -d
root     18941  0.0  0.0 103332   904 pts/1    S+   15:37   0:00 grep gearman
```
#### 2、安装PHP扩展
```shell
[root@xm ~]# wget http://pecl.php.net/get/gearman-1.1.2.tgz
[root@xm ~]# tar zxvf gearman-1.1.2
[root@xm ~]# cd gearman-1.1.2
[root@xm ~]# phpize
[root@xm ~]# ./configure
//configure时如果提示找不到php-config，需指定：–with-php-config
[root@xm ~]# ./configure --–with-php-config=/your/path/php/*/bin/php-config
//如果提示: configure: error: Please install libgearman
//则安装:libgearman-devel,
[root@xm ~]# yum install libgearman-devel
[root@xm ~]# make && make install
...
Installing shared extensions:     /php/lib/php/extensions/no-debug-non-zts-20131226/

//在php.ini配置文件里添加扩展链接，链接地址为安装完后显示的链接
//也可以用find命令查找
[root@xm ~]# find / -name gearman.so
[root@xm ~]# vim /path/for/php/php.ini
[gearman]
extension = /php/lib/php/extensions/no-debug-non-zts-20131226/gearman.so

//重启PHP服务，Nginx服务
//查看是否安装成功
[root@xm ~]# php -info | grep gearman
gearman
gearman support => enabled
libgearman version => 1.1.8
//或者
[root@xm ~]# php -m | grep gearman
gearman
```
#### 3、测试
```linux
[root@xm ~]# vim test.php
<?php
	echo gearman_version()."\r\n";
?>
[root@xieming server]# php test.php 
1.1.8
```
### 源码方式安装

#### 1、下载源码包
```shell
[~] wget https://launchpad.net/gearmand/1.2/1.1.12/+download/gearmand-1.1.12.tar.gz
```
#### 2、解压并安装Gearman服务
```shell
[~] tar zxvf gearmand-1.1.12.tar.gz
[~] cd gearmand-1.1.12
[~] ./configure
//错误解决
configure: error: could not find boost 错误解决  
[~]# yum install boost-devel*  
configure: error: could not find gperf 错误解决  
[~]# yum install gperf*  
configure: error: Unable to find libevent 错误解决  
[~]# yum install libevent-devel*  
configure: error: Unable to find libuuid 错误解决  
[~]# yum install libuuid-devel 
[~] make && make install
```
#### 3、启动Gearman服务
```shell
[~] /usr/local/sbin/gearmand
//Could not open log file "/usr/local/var/log/gearmand.log"
[~] mkdir /usr/local/var/log
[~] touch gearmand.log
//重新启动
[~] /usr/local/sbin/gearmand
[~] ps aux | grep gearmand
root     18663  0.0  0.0 407136  1380 ?        Ssl  15:42   0:00 gearmand -d
root     18754  0.0  0.0 112660   972 pts/4    S+   15:49   0:00 grep --color=auto gearmand
```
#### 4、安装PHP扩展
```
同【yum方式安装】【步骤2】	
```