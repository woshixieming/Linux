### 一、安装Package Control组件

* 先确认是否已经安装好。点击：工具栏->Preferences，如果有Package Control，则说明已经安装过，可跳过这一步。
* 未安装组件，只有 Browser Packages 这一栏
* ctrl+`  在页面下方会有输入框显示，在输入框内复制粘贴以下代码并回车
> import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())

* 再次查看Preference，在最后一排会有Package Control栏

### 二、安装SFTP/FTP插件
* Ctrl+Shift+P，调出命令面板
* 输入install，调出Install Package，视网络情况等待数秒
* 输入sftp，一般第一个就是该插件，直接回车安装。需要等待数秒或几分钟。
* 安装成功，会跳出一个说明页面。可直接关闭。
* 此时，在工具栏，点击File，会有SFTP/FTP功能

### 三、使用SFTP/FTP
* 右键点击项目根目录->SFTP/FTP->Map to Remote
* 在项目根目录会生成一个json格式的配置文件：sftp-config.json

    > 修改：  
    > "type": "ftp", //根据自己具体类型填写sftp, ftp or ftps  
    >  
    > "upload_on_save": true, //保存后自动上传  
    > ...   
    > 
    > "host": "xxx.xxx.xxx.xxx",  
    > "user": "ftp_name",  
    > "password": "ftp_password",  
    > "port": "22",  
    >    
    >  "remote_path": "/example/path/", //如果是下面的子目录，填写具体目录 
    > 其他选项可默认。  

* 再次右键点击项目根目录->SFTP/FTP->Upload Folder，即可上传。
