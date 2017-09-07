
`Git安装请自行百度、谷歌或参考本文下方相关链接` 

### 搭建Git服务器

1、创建一个运行git服务的用户及用户组


	[~] groupadd git  
	[~] useradd -g git git

	#把git用户附加到www用户组，用于自动部署，根据项目目录的用户组而定
	[~] usermod -a -G www git
	
	# 禁止git用户使用shell登录
	[~] vim /etc/passwd
	git:x:1001:1001:,,,:/home/git:/bin/bash
	改为：
	git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell

2、创建SSH证书

	[~] ssh-keygen -t rsa -C "your-email@xxx.com"
	# 点三次回车，即可生成。
	# 把 ~/.ssh/id_rsa.pub 里的内容copy到 /home/git/.ssh/authorized_keys 
	[~] cat ~/.ssh/id_rsa.pub>>/home/git/.ssh/authorized_keys
	# 如果没有.ssh文件夹或者authorized_keys文件，则手动创建


3、初始化仓库


	[~] git init --bare /your/path/git/test.git
	# 会初始化一个裸仓库，一般都以.git结尾
	
	[~] chown git:git test.git
	# 更改仓库所有者

4、克隆远程仓库

	# 在各自电脑上克隆远程仓库
	[~] git clone git@xxx.xxx.xxx.xxx:/your/path/git/test.git


### 配置Hooks，实现自动部署

	[~] cd /your/path/git/test.git/hooks
	
	# 在这个文件夹里会有很多文件，每个节点都会有一个文件，具体文件作用点可参考文章末尾(参考链接-3)这篇文章
	# 自动部署使用post-receive文件，如果不存在，则直接创建一个或用vim创建并打开。
	# 有的版本会有post-receive.sample文件，则复制一份。
	[~] cp post-receive.sample post-receive
	[~] vim post-receive


##### 编辑post-receive文件

	#!/usr/bin/bash
	
	xmPwd=`pwd`
	IS_BARE=$(git rev-parse --is-bare-repository)
	if [ -z "$IS_BARE" ]; then
	echo >&2 "fatal: post-receive: IS_NOT_BARE"
	exit 1
	fi
	
	#unset GIT_DIR  
	# 这里是个坑，GIT_DIR指向的是根目录下的 .git目录，
	# 建议不要使用，很多文章里都有写这句，
	# 个人实践下来，还是给注释掉了，不然会引起git提交报错
	
	# 网站目录
	webSiteRoot="/www/wwwroot/web/"
	echo "==============================================="  
	cd $webSiteRoot
	#unset GIT_DIR # 同上，即使放在这个地方也会有坑
	echo "open and pull $webSiteRoot"
	sudo git checkout .
	sudo git pull origin master
	sudo chown -R www:www $webSiteRoot
	sudo chmod 0755 -R $webSiteRoot
	time=`date`
	echo "web server pull at webserver at time: $time."  
	echo "Finished!"
	echo "================================================"  
	
	# 写日志，测试用，可删除
	echo "post-receive operate" >> ~/post-receive.log
	
	# 执行完，回到原目录
	cd $xmPwd

---
	# 最后记得给文件添加执行权限
	[~] chmod +x post-receive
	
	# 以上完成自动部署配置


> 请使用 *命令行形式* 进行流程测试   
> 也可使用 `source /path/to/git/repository/name.git/hooks/post-receive` 进行调试

	#错误处理
	sudo: no tty present and no askpass program specified
	
	[~] vim /etc/sudoers
	#增加或修改
	username  ALL=(ALL)  NOPASSWD:ALL

---

以下是命令行形式push代码时，返回的信息：

	$ git push origin master
	Counting objects: 2, done.
	Delta compression using up to 4 threads.
	Compressing objects: 100% (2/2), done.
	Writing objects: 100% (2/2), 221 bytes | 0 bytes/s, done.
	Total 2 (delta 1), reused 0 (delta 0)
	remote: ===============================================
	remote: open and auto pull /www/wwwroot/web/
	remote: From /data/git/gtdysd
	remote:  * branch            master     -> FETCH_HEAD
	remote: Updating 909b6cc..7e5c8aa
	remote: Fast-forward
	remote:  test.txt | 1 -
	remote:  1 file changed, 1 deletion(-)
	remote:  delete mode 100644 test.txt
	remote: web server pull at webserver at time: Thu Sep  7 18:24:09 CST 2017.
	remote: Finished!
	remote: ================================================
	To 192.168.0.xx:/data/git/test.git
	   909b6cc..7e5c8aa  master -> master




> 经常出问题的地方，就是各个文件夹的权限分配  
> 1、Git仓库所属用户及用户组  
> 2、项目目录所属用户及用户组、权限  
> 3、post-receive文件所属的用户及用户组、执行权限
> 4、posst-receive文件内sudo的命令权限

**该版本目前仅支持同服务器部署(即：Git仓库和项目目录在同一个服务器)，跨服务器自动部署待更新...**

*最后吐槽下：Markdown编辑器是确实好用，但是各家用的版本样式是真杂，有道云上的文章想要放到github上就得重新修改样式*


参考资料：  
1、[廖雪峰的官方网站-Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)  
2、[git-scm.com-中文手册](https://git-scm.com/book/zh/v2)  
3、[githooks(5) Manual Page-英文版](https://www.kernel.org/pub/software/scm/git/docs/githooks.html)  
4、[www.kernel.org-git文档](https://www.kernel.org/pub/software/scm/git/docs/)