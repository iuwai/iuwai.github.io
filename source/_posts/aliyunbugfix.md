---
title: aliyunbugfix
date: 2020-10-26 16:08:44
categories:
tags:
---
阿里云漏洞修复记录
<!-- more -->
## RHSA-2019:0368-高危: systemd 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-6454 | 7  | bus_process_object()中的输入验证不足导致PID1崩溃 |
### 修复命令
```shell
yum update systemd 
yum update systemd-libs 
yum update systemd-sysv 
```

## RHSA-2019:1228-高危: wget 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-5953 | 8.8  | 缓冲区溢出漏洞 |
### 修复命令
```shell
yum update wget
```

## RHSA-2020:2894-重要: dbus 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2020-12049 | 5.5  | D-BUS安全漏洞 |
### 修复命令
```shell
yum update dbus 
yum update dbus-libs 
```

## RHSA-2020:2337-重要: git 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2020-11008 | 7.5  | 受影响的Git版本存在一个漏洞，可以诱骗Git向攻击者控制的主机发送私有凭据 |
### 修复命令
```shell
yum update git 
yum update perl-Git 
```

## RHSA-2020:1021-中危: GNOME security,bug fix,和 enhancement update
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-3820 | 4.8  | 部分锁屏旁路 |
### 修复命令
```shell
yum update shared-mime-info 
yum update gtk-update-icon-cache 
```

## RHSA-2020:1050-中危: cups 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2018-4180 | 6.7  | 由于不安全的环境变量处理，本地权限升级到root |
| CVE-2018-4181 | 5.5  | 本地攻击者对cupsd.conf的操作导致以root身份读取任意文件的次数有限 |
| CVE-2018-4700 | --  | 可预测的会话cookie打破CSRF保护 |
### 修复命令
```shell
yum update cups-client 
yum update cups-libs 
```

## RHSA-2020:0540-重要: sudo 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-18634 | 7.8  | Sudo缓冲区错误漏洞 |
### 修复命令
```shell
yum update sudo
```

## RHSA-2020:1021-中危: GNOME security,bug fix,和 enhancement update
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-3820 | 4.8  | 部分锁屏旁路 |
### 修复命令
```shell
yum update gtk-update-icon-cache 
yum update shared-mime-info 
```

## RHSA-2020:1131-中危: python 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2018-20852 | 5.3  | Cookie域检查返回不正确的结果 |
| CVE-2019-16056 | 7.5  | email.utils.parseaddr错误地解析电子邮件地址 |
### 修复命令
```shell
yum update python 
yum update python-devel 
yum update python-libs 
```

## RHSA-2020:0374-重要: 内核 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-11599 | 7  | 修复mmget_not_zero（）/ get_task_mm（）和核心转储之间的竞争条件 |
| CVE-2019-14816 | 7.8  | 拒绝服务漏洞 |
| CVE-2019-14895 | 9.8  | 在drivers/net/wireless/ slots / mwifiex_process_country_ie()函数中基于堆的缓冲区溢出 |
| CVE-2019-14898 | 7  | CVE-2019-11599中mmget_not_zero()/get_task_mm()和核心转储之间的竞争条件的未完成修复 |
| CVE-2019-14901 | 9.8  | marvell/mwifiex/tdls.c中的堆溢出 |
| CVE-2019-17133 | 9.8  | net/wireless/wext-sme.c中的cfg80211_mgd_wext_giwessid中的缓冲区溢出 |
### 修复命令
```shell
yum update kernel
```