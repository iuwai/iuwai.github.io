---
title: Linux内核升级
date: 2020-11-02 17:40:11
categories: Linux
tags: [CentOS7,kernel]
---
Linux内核大版本升级
<!-- more -->

# 3.10内核升级至4.4
## 环境说明
腾讯云  
Centos7.8  
内核版本：3.10.0-1127.19.1.el7.x86_64

## 目标
内核版本升级至当前最新稳定版：4.4.241-1.el7.elrepo.x86_64

## kernel 3.10->4.4升级步骤
### yum源修改
```shell
# 输入公钥
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# 升级安装ELRepo
yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
# 载入elrepo-kernel 元数据
yum --disablerepo=\* --enablerepo=elrepo-kernel repolist
```
### 查看可用rpm包
yum --disablerepo=\* --enablerepo=elrepo-kernel list kernel*

### 安装最新稳定版kernel
```shell
# lt：长期支持版本；ml：主线版本
yum --disablerepo=\* --enablerepo=elrepo-kernel install  kernel-lt.x86_64  -y
```
#### 删除旧版本工具包
```shell
yum remove kernel-tools-libs.x86_64 kernel-tools.x86_64  -y
```
#### 安装新版本工具包
```shell
yum --disablerepo=\* --enablerepo=elrepo-kernel install kernel-lt-tools.x86_64  -y
```
### 将内核启动修改为本机最新版本
#### 查看内核版本
```shell
# 查看内核启动顺序
[root@dev-node-1 ~]# awk -F \' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg
0 : CentOS Linux (4.4.241-1.el7.elrepo.x86_64) 7 (Core)
1 : CentOS Linux (3.10.0-1127.19.1.el7.x86_64) 7 (Core)
2 : CentOS Linux (3.10.0-1127.13.1.el7.x86_64) 7 (Core)
3 : CentOS Linux (0-rescue-4acec6de0e4741b6bc249381506fa844) 7 (Core)
4 : CentOS Linux (0-rescue-0ea734564f9a4e2881b866b82d679dfc) 7 (Core)
或
# 查看系统可用内核
[root@dev-node-1 ~]# grep "^menuentry" /boot/grub2/grub.cfg | cut -d "'" -f2
CentOS Linux (4.4.241-1.el7.elrepo.x86_64) 7 (Core)
CentOS Linux (3.10.0-1127.19.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-1127.13.1.el7.x86_64) 7 (Core)
CentOS Linux (0-rescue-4acec6de0e4741b6bc249381506fa844) 7 (Core)
CentOS Linux (0-rescue-0ea734564f9a4e2881b866b82d679dfc) 7 (Core)
```

```shell
# 查看当前实际启动顺序
[root@dev-node-1 ~]# grub2-editenv list
saved_entry=CentOS Linux (3.10.0-1127.19.1.el7.x86_64) 7 (Core)
```
#### 设置默认启动
```shell
[root@dev-node-1 ~]# grub2-set-default "CentOS Linux (4.4.241-1.el7.elrepo.x86_64) 7 (Core)"
```
或者
```shell
grub2-set-default 0     // 0代表查看内核启动顺序时的序号
```
或者
```shell
# 编辑/etc/default/grub文件
vim /etc/default/grub
GRUB_DEFAULT=saved 改为GRUB_DEFAULT=0
```
#### 生成grub配置文件
```shell
[root@dev-node-1 ~]# grub2-mkconfig  -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-4.4.241-1.el7.elrepo.x86_64
Found initrd image: /boot/initramfs-4.4.241-1.el7.elrepo.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1127.19.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1127.19.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1127.13.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1127.13.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-4acec6de0e4741b6bc249381506fa844
Found initrd image: /boot/initramfs-0-rescue-4acec6de0e4741b6bc249381506fa844.img
Found linux image: /boot/vmlinuz-0-rescue-0ea734564f9a4e2881b866b82d679dfc
Found initrd image: /boot/initramfs-0-rescue-0ea734564f9a4e2881b866b82d679dfc.img
done
```
### 重启&验证
```shell
[root@dev-node-1 ~]# reboot

[root@dev-node-1 ~]# uname -r
4.4.241-1.el7.elrepo.x86_64
```

### 删除旧内核（可选）
#### 查看本机内所有内核
```shell
[root@dev-node-1 ~]# rpm -qa | grep kernel
kernel-3.10.0-1127.13.1.el7.x86_64
kernel-headers-3.10.0-1127.13.1.el7.x86_64
kernel-lt-4.4.241-1.el7.elrepo.x86_64
kernel-lt-tools-4.4.241-1.el7.elrepo.x86_64
abrt-addon-kerneloops-2.1.11-57.el7.centos.x86_64
kernel-devel-3.10.0-1127.13.1.el7.x86_64
kernel-3.10.0-1127.19.1.el7.x86_64
kernel-lt-tools-libs-4.4.241-1.el7.elrepo.x86_64
```
#### yum remove
```shell
yum remove kernel-3.10.0-1127.13.1.el7.x86_64 \
kernel-devel-3.10.0-1127.13.1.el7.x86_64 \
kernel-3.10.0-1127.19.1.el7.x86_64

```
或
#### yum-utils工具
如果安装的内核不多于 3 个，yum-utils 工具不会删除任何一个。只有在安装的内核大于 3 个时，才会自动删除旧内核。
```shell
# 安装yum-utils
[root@dev-node-1 ~]# yum install yum-utils
# 删除旧版本
package-cleanup --oldkernels
```