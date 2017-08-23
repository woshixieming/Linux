### 安装Nginx ###
```
[~] yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel wget
[~] wget http://nginx.org/download/nginx-1.10.2.tar.gz
[~] tar -zxvf nginx-1.10.2.tar.gz
[~] cd nginx-1.10.2
[~] ./configure
[~] make
[~] make install
[~] whereis nginx
[~] /usr/local/nginx/sbin/nginx
#打开浏览器，输入ip地址，看到welcome to nginx！即安装成功
```    
---
### Welcome to nginx! ###

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.      
Commercial support is available at nginx.com. 

Thank you for using nginx.

---

###### 设置开机启动 ######
> 参考官方提供的脚本-[这里是链接](https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/)  
> 
> 需要修改的地方  
> `nginx='/usr/sbin/nginx'` 修改成nginx执行程序的路径  
> `NGINX_CONF_FILE='/etc/nginx/nginx.conf'` 修改成配置文件的路径。


### 安装PHP ###
```
#默认使用yum安装的版本是5.3，可使用下面升级PHP的方法进行安装
```

### 升级PHP ###
```
#查找php有关的源码
[~] yum list installed | grep php

[~] yum remove php.x86_64 php-cli.x86_64 php-common.x86_64 php-fpm.x86_64 php-gd.x86_64 php-mbstring.x86_64 php-mysql.x86_64 php-pdo.x86_64

[~] rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm

[~] yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64 php56w-odbc.x86_64 php56w-xml.x86_64 php56w-xmlrpc.x86_64 php56w-soap.x86_64 php56w-fpm.x86_64

[~] service php-fpm restart
```    
    
    
### 安装mysql ###
```
#检查系统是否安装其他版本的MYSQL数据
[~] yum list installed | grep mysql
[~] yum -y remove mysql-libs.x86_64

#安装及配置
[~] wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
[~] rpm -ivh mysql-community-release-el6-5.noarch.rpm
[~] yum repolist all | grep mysql

#安装MYSQL数据库
[~] yum install mysql-community-server -y

#设置为开机启动
[~] chkconfig --list | grep mysqld
[~] chkconfig mysqld on

#进入数据库
[~] mysql -uroot -p

#如果出现ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'错误，可能是服务未开启，也可能是/var/lib/mysql的访问权限问题 
#如果是服务未开启：service mysqld start 
#如果是访问权限问题：chown -R openscanner:openscanner /var/lib/mysql

#修改密码
>mysql use mysql;
>mysql update user set password=password(123456) where user='root';
>mysql flush privileges; **必须执行该句**
>mysql exit;  

#重新登录
[~] mysql -uroot -p
``` 