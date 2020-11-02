---
title: CentOS7.4 update ntp
date: 2018-02-13 14:23:08
categories: Linux
tags: [CentOS7,ntp]
---
CentOS6.8 x86_64升级ntp-4.2.8p9.tar.gz
<!-- more -->

#准备工作
## 安装依赖包
```bash
yum -y install gcc gcc-c++ openssl-devel libstdc++* libcap*
```
## 备份旧版本的配置文件
```bash
cp -ar /etc/ntp /etc/ntp.bak
cp /etc/ntp.conf /etc/ntp.conf.bak
cp /etc/init.d/ntpd /etc/init.d/ntpd.bak
cp /etc/sysconfig/ntpd /etc/sysconfig/ntpd.bak
cp /etc/sysconfig/ntpdate /etc/sysconfig/ntpdate.bak
```
## 卸载原ntpd服务
```bash
yum -y erase ntp ntpdate
```
# 编译安装
## ./configure
```bash
tar zxvf ntp-4.2.8p10.tar.gz
cd ntp-4.2.8p10
./configure --prefix=/usr --bindir=/usr/sbin --sysconfdir=/etc --enable-linuxcaps --with-lineeditlibs=readline --docdir=/usr/share/doc/ntp-4.2.8p9 --enable-all-clocks --enable-parse-clocks --enable-clockctl
make && make install
```
## 创建配置
```bash
cp /etc/init.d/ntpd.bak /etc/init.d/ntpd
cp /etc/sysconfig/ntpd.bak /etc/sysconfig/ntpd
cp /etc/sysconfig/ntpdate.bak /etc/sysconfig/ntpdate
mv /etc/ntp.bak /etc/ntp
cp /etc/ntp.conf.bak /etc/ntp.conf
```
## 修改配置
```bash
vim /etc/ntp.conf
```
## 启动服务
```bash
service ntpd start
```