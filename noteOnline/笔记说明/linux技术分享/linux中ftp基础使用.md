> 个人文章：<a href="http://write.blog.csdn.net/postlist" target="_blank">java真好喝</a>
**希望对你有所帮助**
<p style="text-align:right">
作者：刘成  日期：2019/9/6
</p>
#### 目录
[toc]
#### 检查是否安装
```
rpm -qa |grep vsftp
```
如果已经安装则会显示版本号
#### 安装和卸载ftp
- 安装及设置开机启动
```
yum install -y vsftp
chkconfig vsftpd on
```
<font color="#dd0000">注：</font>服务器有外网的情况下可以使用yum命令，使用阿里云腾讯云的话都有外网<br />

- 卸载
```
rpm -e vsftp
```
#### 关闭匿名登录
目的：可开启登录验证
 1. 将配置文件中”anonymous_enable=YES “改为 “anonymous_enable=NO”
<a id='jump_1'>
 2. 保存后，重启ftp。重启命令：`service vsftpd restart`（启动ftp服务命令则为`service vsftpd start`）</a>
#### 添加ftp账号
1. 命令：`useradd ftpadmin -s /sbin/nologin `。该账户路径默认指向/home/ftpadmin目录；如果需要将用户指向其他目录，命令：`useradd ftpadmin -s /sbin/nologin –d /www`(其他目录)
2. 设置密码：`passwd 被指定账户`。输入时光标无动静，其实在输入，放心输入两次密码确认 :|。
3. 重启ftp服务，重启命令:<a href="#jump_1">service vsftpd restart</a>
#### 设置ftp访问路径
在ftp配置文件（路径：`/etc/vsftpd/vsftpd.conf`）添加：`local_root=配置路径`（如local_root=/opt,访问ftp默认目录为opt）;在地址栏输入“ftp://访问地址”或使用<a href='#jump_2'>工具</a>访问可验证访问路径
<a id='jump_2'></a>
#### 工具分享
- [xshell](https://www.netsarang.com/zh/xshell/)：无界面纯命令的工具
- [Winscp](https://winscp.net/eng/download.php)：有文件目录的工具