---
title: aliyunbugfix
date: 2020-10-26 16:08:44
categories:
tags:
---
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