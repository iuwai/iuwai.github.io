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

## RHSA-2020:1113-中危: bash 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-9924 | 7.8 | Bash任意命令执行安全漏洞 |
### 修复命令
```shell
yum update bash
```

## RHSA-2020:1011-中危: expat 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2015-2716 | 7.5 | Mozilla Thunderbird XML内容解析缓冲区溢出漏洞 |
### 修复命令
```shell
yum update expat
```

## RHSA-2020:2344-重要: bind 安全更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2020-8616 | 8.6 | 恶意行为者故意利用这种缺乏有效限制执行次数的行为 |
| CVE-2020-8617 | 7.5 | 使用特制的消息，攻击者可能会导致BIND服务器进入不一致状态 |
### 修复命令
```shell
yum update bind-libs-lite
yum update bind-license
yum update bind-export-libs
```

## RHBA-2019:2599: krb5 bug修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
### 修复命令
```shell
yum update krb5-devel
yum update krb5-libs
yum update libkadm5
```

## RHSA-2018:3157-中危: curl 和 nss-pem 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2018-1000007 | 9.8 | libcurl信息泄露漏洞 |
| CVE-2018-1000120 | 9.8 | curl缓冲区错误拒绝服务漏洞 |
| CVE-2018-1000122 | 9.1 | curl缓冲区错误漏洞 |
### 修复命令
```shell
yum update curl
yum update libcurl
yum update nss-pem
```

## RHSA-2019:2079-中危: Xorg 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2018-14599 | 9.8 | 拒绝服务漏洞 |
| CVE-2018-14600 | 9.8 | 越界访问拒绝服务代码执行漏洞 |
| CVE-2018-15857 | 7.8 | ExprAppendMultiKeysymList中的无效释放导致崩溃 |
### 修复命令
```shell
yum update libX11
yum update libX11-common
```

## RHSA-2018:3092-中危: glibc 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2017-16997 | 7.8 | GNU C Library'elf/dl-load.c'本地权限提升漏洞 |
| CVE-2018-11236 | 7.8 | glibc基于堆的缓冲区溢出导致任意代码执行 |
| CVE-2018-6485 | 9.8 | GNU C Library'memalign'函数整数溢出漏洞 |
### 修复命令
```shell
yum update glibc
yum update glibc-common
yum update glibc-devel
yum update glibc-headers
yum update nscd
```

## RHSA-2020:1000-中危: rsyslog security,bug fix,和 enhancement update
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-17041 | 9.8 | 在contrib/pmaixforward from/pmaixforward from.c中的基于堆的溢出 |
| CVE-2019-17042 | 9.8 | 在contrib/pmcisconames/pmcisconames.c中基于堆的溢出 |
### 修复命令
```shell
yum update rsyslog
```

## RHSA-2020:0374-重要: 内核 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-11599 | 7 | 修复mmget_not_zero()/get_task_mm()和核心转储之间的竞争条件 |
| CVE-2019-14895 | 9.8 | 在drivers/net/wireless/slots/mwifiex_process_country_le()函数中基于堆的缓冲区溢出 |
| CVE-2019-14901 | 9.8 | marvell/mwifiex/tdls.c中的堆溢出 |
| CVE-2019-17133 | 9.8 | net/wireless/wext-sme.c中的cfg80211_mgd_wext_giwessid中的缓冲区溢出 |
### 修复命令
```shell
yum update kernel
yum update kernel-devel
yum update kernel-tools
yum update kernel-tools-libs
yum update python-perf
yum update kernel-headers
yum update kernel-debug-devel
yum update perf
```

## RHSA-2019:2030-中危: python 安全和BUG修复更新
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2019-9948 | 9.1 | local_file允许远程攻击者绕过保护机制 |
| CVE-2019-5010 | 7.5 | 使用特制的X509证书进行NULL指针取消引用 |
| CVE-2018-14647 | 5.3 | Python拒绝服务漏洞（CNDV-2018-20081） |
### 修复命令
```shell
yum update python
yum update python-devel
yum update python-libs
```

## RHSA-2019:2197-低危: elfutils security,bug fix,和 enhancement update
### 漏洞详情
| 漏洞编号 | 影响分 | 漏洞公告 |
| :------: | :------ | :------ |
| CVE-2018-16402 | 9.8 | elfutils拒绝服务漏洞 |
| CVE-2018-16403 | 5.5 | Libdw/dwar_getabbrev.c和libwd/dwar_hasattr.c中基于堆的缓冲区读取过度会导致崩溃 |
| CVE-2018-7665 | 5.5 | elf32_elateton.c中函数elf32_xlatetom中基于堆的缓冲区重读 |
### 修复命令
```shell
yum update elfutils-default-yama-scope 
yum update elfutils-libelf 
yum update elfutils-libs 
yum update elfutils 
```