---
title: CentOS7.4 install vsftp
date: 2018-02-09 16:37:03
categories: Linux
tags: [CentOS7.4,vsftp]
---
CentOS7.4安装vsftp及配置
vsftp版本：vsftpd-3.0.2-22.el7.x86_64
<!-- more -->

# 安装vsftpd
查看是否已经安装vsftpd
```bash
rpm -qa | grep vsftpd
```
如果没有，就安装，并设置开机启动
```bash
yum -y install vsftpd
chkconfig vsftpd on
```
当前yum安装的FTP版本为：vsftpd-3.0.2-22.el7.x86_64
# 修改配置
所谓虚拟用户就是没有使用真实的帐户，只是通过映射到真实帐户和设置权限的目的。虚拟用户不能登录CentOS系统。
修改配置文件：
打开/etc/vsftpd/vsftpd.conf，做如下配置
```bash
listen=YES
anonymous_enable=NO //设定不允许匿名访问
dirmessage_enable=YES
allow_writeable_chroot=YES
#以下两行设置作用为启用日志
dual_log_enable=YES
vsftpd_log_file=/var/log/vsftpd.log
xferlog_std_format=YES
chroot_local_user=YES
guest_enable=YES
guest_username=admin
user_config_dir=/etc/vsftpd/vuser_conf //设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径。存放虚拟用户个性的CentOS FTP服务文件(配置文件名=虚拟用户名
pam_service_name=vsftpd //PAM认证文件名。PAM将根据/etc/pam.d/vsftpd进行认证
local_enable=YES
accept_timeout=60
connect_timeout=60
idle_session_timeout=60
data_connection_timeout=120
#chown_uploads=YES
#chown_username=admin
#pasv_enable=YES
#pasv_max_port=60000
#pasv_min_port=20000
```
经验证，FTP3.0.2版本必须加参数：allow_writeable_chroot=YES，6.8默认安装vsftpd-2.2.2-13.el6_6.1.x86_64不加也能生效，如果3.0.2不加此参数会报如下错误：
500 OOPS: vsftpd: refusing to run with writable root inside chroot()
根本原因在于，从vsftpd_2.3.5版开始，取消了根目录的可写权限。因此，网上的普遍解决方案是以下两种：

## 方案一
```bash
chmod a-w /vaf/ftp
```
## 方案二
vim /etc/vsftpd.conf add the following
```bash
allow_writeable_chroot=YES
```
经验证，如果加上参数allow_writeable_chroot，即使去掉参数chroot_local_user=YES，也可正常访问。
进行认证：
首先，安装Berkeley DB工具，很多人找不到db_load的问题就是没有安装这个包。
```bash
yum install db4 db4-utils
```
然后，创建用户密码文本/etc/vsftpd/vuser_passwd.txt ，注意奇行是用户名，偶行是密码
```bash
test
123456
```
接着，生成虚拟用户认证的db文件
```bash
db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db
```
随后，编辑认证文件/etc/pam.d/vsftpd，全部注释掉原来语句，再增加以下两句：
```bash
auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd
```
mkdir /etc/vsftpd/vuser_conf/
```bash
vi /etc/vsftpd/vuser_conf/test //文件名等于vuser_passwd.txt里面的账户名，否则下面设置无效

write_enable=YES //可写
anon_world_readable_only=NO
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
local_root=/httx/run/appdown/paasftpuser /虚拟用户根目录,根据实际情况修改
```
#### 设置Selinux
```bash
setsebool -P ftp_home_dir=1   //设置ftp可以使用home目录
sersebool -P allow_ftpd_full_access=1 //设置ftp用户可以有所有权限
```
设置FTP根目录权限
```bash
mkdir /ftp/www   //创建目录
chmod R 755 /ftp
chmod R 750 /ftp/www
```
ftp访问目录权限尽量最小
#### 设置防火墙
打开/etc/sysconfig/iptables
在“-A INPUT –m state --state NEW –m tcp –p –dport 22 –j ACCEPT”，下添加：
```bash
-A INPUT -m state --state NEW -m tcp -p -dport 21 -j ACCEPT
```
然后保存，并关闭该文件，在终端内运行下面的命令，刷新防火墙配置：
```bash
service iptables restart
```
OK，FTP搭建完成！