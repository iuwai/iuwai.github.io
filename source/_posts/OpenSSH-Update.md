---
title: OpenSSH Update
date: 2018-02-09 15:37:17
categories: Linux
tags: ["安全加固",OpenSSH]
---
由于公司安全需要，OpenSSH要加固，需升级至当前最新版本OpenSSH_7.6p1  
环境：CentOS6.8 x64
<!-- more -->

# 准备工作
## 下载相关软件包
OpenSSH需要依赖ZLIB和OpenSSL，因此需要从官网下载三者的源码包。需要注意的是：OpenSSH最新版7.4p1依赖的OpenSSL版本为1.0.2k，而不是其最新版1.1.0e（使用此版会升级失败）,ZLIB可以使用最新版1.2.11。CentOS6.8自带的zlib版本为1.2.3，也可不进行升级。 三者源码下载地址：
```bash
http://www.zlib.net/
http://www.openssl.org/
http://www.openssh.org/
```
或者使用我准备好的包：
https://pan.baidu.com/s/1PzuK3FGVPLZuWvVvqJgaIQ

## 查看系统当前软件版本
```bash
rpm -q zlib
openssl version
ssh -V
```

## 安装telnet服务并启用
因升级OpenSSH过程中需要卸载现有OpenSSH, 因此为了保持服务器的远程连接可用，需要启用telnet服务作为替代，如升级出现问题，也可通过telnet登录服务器进行回退。

### A、安装telnet服务
```bash
yum -y install telnet-server*
```

### B、启用telnet
```bash
先关闭防火墙，否则telnet可能无法连接
service iptables stop
chkconfig iptables off
vi /etc/xinetd.d/telnet                 #将其中disable字段的yes改为no以启用telnet服务
mv /etc/securetty /etc/securetty.old    #允许root用户通过telnet登录
service xinetd start                    #启动telnet服务
chkconfig xinetd on                     #使telnet服务开机启动，避免升级过程中服务器意外重启后无法远程登录系统
telnet [ip]                             #新开启一个远程终端以telnet登录验证是否成功启用
```

## 安装编译所需工具包
```bash
yum -y install gcc pam-devel zlib-devel
```

# 正式升级
## 升级ZLIB
### A、解压zlib_1.2.11源码并编译

```bash
tar -zxvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
CFLAGS='-mstackrealign -fPIC -O3' ./configure --prefix=/usr     //如果不加【CFLAGS='-mstackrealign -fPIC -O3'】这一段配置后面安装OpenSSH时会报错。
make
make install
```

### B、卸载当前zlib
注意：此步骤必须在步骤A执行完毕后再执行，否则先卸载zlib后，/lib64/目录下的zlib相关库文件会被删除，步骤A编译zlib会失败。（补救措施：从其他相同系统的服务器上复制/lib64、/usr/lib和/usr/lib64目录下的libcrypto.so.10、libssl.so.10、libz.so.1、libz.so.1.2.3四个文件到相应目录即可。可通过whereis、locate或find命令找到这些文件的位置）

```bash
rpm -e --nodeps zlib
```

### C、安装之前编译好的zlib
```bash
# 在zlib编译目录执行如下命令
make install
```
### D、共享库注册
zlib安装完成后，会在/usr/lib目录中生产zlib相关库文件，需要将这些共享库文件注册到系统中。
<font color="#FF6600">建议用这种方式:</font>
```bash
ln -s  /usr/lib/libz.so.1 libz.so.1.2.11
ln -s  /usr/lib/libz.so libz.so.1.2.11
ln -s  /usr/lib/libz.so.1 /lib/libz.so.1
rm -f /usr/lib64/libz.so                                  //若没有删除,则openssl编译失败.
ln -s  /usr/lib/libz.so /usr/lib64/libz.so
ln -s  /usr/lib64/libz.so ../../lib64/libz.so.1.2.11   //64位系统必须修改这个
ldconfig
```
<font color="#FF6600">**或者**</font>采用如下方式也可：
```bash
echo '/usr/lib' >> /etc/ld.so.conf
ldconfig                    #更新共享库cache
```
## 升级OpenSSL
官方升级文档：http://www.linuxfromscratch.org/blfs/view/cvs/postlfs/openssl.html
### A、备份当前openssl
```bash
find / -name openssl
/etc/pki/ca-trust/extracted/openssl
/usr/bin/openssl
/usr/lib64/openssl
/usr/include/openssl

mv /usr/lib64/openssl /usr/lib64/openssl.old
mv /usr/bin/openssl  /usr/bin/openssl.old
mv /etc/pki/ca-trust/extracted/openssl  /etc/pki/ca-trust/extracted/openssl.old
mv /usr/include/openssl /usr/include/openssl.old
```
如下两个库文件必须先备份，因系统内部分工具（如yum、wget等）依赖此库，而新版OpenSSL不包含这两个库
```bash
cp /usr/lib64/libcrypto.so.10  /usr/lib64/libcrypto.so.10.old
cp /usr/lib64/libssl.so.10  /usr/lib64/libssl.so.10.old
```
### B、卸载当前openssl
```bash
rpm -qa | grep openssl
openssl-1.0.1e-42.el6.x86_64

rpm -e --nodeps openssl-1.0.1e-42.el6.x86_64
rpm -qa | grep openssl
#或者直接执行此命令：
rpm -qa |grep openssl|xargs -i rpm -e --nodeps {}
```
### C、解压openssl_1.0.2k源码并编译安装
```bash
tar -zxvf openssl-1.0.2k.tar.gz
cd openssl-1.0.2k
./config --prefix=/usr --openssldir=/etc/ssl --shared zlib    #必须加上--shared，否则编译时会找不到新安装的openssl的库而报错
make
make test                            #必须执行这一步结果为pass才能继续，否则即使安装完成，ssh也无法使用
make install
openssl version -a                   #查看是否升级成功
```
make test正确输出：
```bash
*---- START OF RECORD ----
** Record Content-type: 22
** Record Version: fefd
** Record Epoch: 1
** Record Sequence: 000000000000
** Record Length: 64
**---- START OF HANDSHAKE MESSAGE FRAGMENT ----
**---- HANDSHAKE MESSAGE FRAGMENT ENCRYPTED ----
*---- END OF RECORD ----
---- END OF PACKET ----
PASS
test_bad_dtls
../util/shlib_wrap.sh ./bad_dtls_test
make[1]: Leaving directory `/tmp/openssl-1.0.2m/test'
OPENSSL_CONF=apps/openssl.cnf util/opensslwrap.sh version -a
OpenSSL 1.0.2m  2 Nov 2017
built on: reproducible build, date unspecified
platform: linux-x86_64
options:  bn(64,64) rc4(16x,int) des(idx,cisc,16,int) idea(int) blowfish(idx)
compiler: gcc -I. -I.. -I../include  -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -Wa,--noexecstack -m64 -DL_ENDIAN -O3 -Wall -DOPENSSL_IA32_SSE2 -            
DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DRC4_ASM -DSHA1_ASM -DSHA256_ASM -    
DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM -DECP_NISTZ256_ASM
OPENSSLDIR: "/etc/ssl"
```
### D、恢复共享库
由于OpenSSL_1.0.2k不提供libcrypto.so.10和libssl.so.10这两个库，而yum、wget等工具又依赖此库，因此需要将先前备份的这两个库进行恢复，其他的可视情况考虑是否恢复。
```bash
mv /usr/lib64/libcrypto.so.10.old  /usr/lib64/libcrypto.so.10
mv /usr/lib64/libssl.so.10.old  /usr/lib64/libssl.so.10
```
返回值：
```bash
mv  /usr/lib64/libcrypto.so.10.old  /usr/lib64/libcrypto.so.10
mv: overwrite `/usr/lib64/libcrypto.so.10'? y
mv  /usr/lib64/libssl.so.10.old  /usr/lib64/libssl.so.10
mv: overwrite `/usr/lib64/libssl.so.10'? y
```
## 升级OpenSSH
官方升级文档：http://www.linuxfromscratch.org/blfs/view/svn/postlfs/openssh.html
### A、备份当前openssh
```bash
mv /etc/ssh /etc/ssh.old
```
### B、卸载当前openssh
```bash
rpm -qa | grep openssh
openssh-clients-5.3p1-111.el6.x86_64
openssh-server-5.3p1-111.el6.x86_64
openssh-5.3p1-111.el6.x86_64
openssh-askpass-5.3p1-111.el6.x86_64

rpm -e --nodeps openssh-5.3p1-111.el6.x86_64
rpm -e --nodeps openssh-server-5.3p1-111.el6.x86_64
rpm -e --nodeps openssh-clients-5.3p1-111.el6.x86_64
rpm -e --nodeps openssh-askpass-5.3p1-111.el6.x86_64
rpm -qa | grep openssh
#或者直接执行此命令：
rpm -qa |grep openssh|xargs -i rpm -e --nodeps {}
```
### C、openssh安装前环境配置
```bash
install  -v -m700 -d /var/lib/sshd
chown  -v root:sys /var/lib/sshd
groupadd -g 50 sshd
useradd  -c 'sshd PrivSep' -d /var/lib/sshd -g sshd -s /bin/false -u 50 sshd
```
### D、解压openssh_7.4p1源码并编译安装
```bash
tar -zxvf openssh-7.4p1.tar.gz
cd openssh-7.4p1
./configure --prefix=/usr  --sysconfdir=/etc/ssh  --with-md5-passwords  --with-pam  --with-zlib --with-openssl-includes=/usr --with-privsep-path=/var/lib/sshd
make
make install
```
### E、openssh安装后环境配置
```bash
# 在openssh编译目录执行如下命令
install -v -m755    contrib/ssh-copy-id /usr/bin
install -v -m644    contrib/ssh-copy-id.1 /usr/share/man/man1
install -v -m755 -d /usr/share/doc/openssh-7.4p1
install -v -m644    INSTALL LICENCE OVERVIEW README* /usr/share/doc/openssh-7.4p1
ssh -V              #验证是否升级成功
```
### F、启用OpenSSH服务
```bash
# 在openssh编译目录执行如下目录
echo 'X11Forwarding yes' >> /etc/ssh/sshd_config
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config   #允许root用户通过ssh登录
cp -p contrib/redhat/sshd.init /etc/init.d/sshd
chmod +x /etc/init.d/sshd
chkconfig  --add  sshd
chkconfig  sshd  on
chkconfig  --list  sshd
service sshd restart
```
注意：如果升级操作一直是在ssh远程会话中进行的，上述sshd服务重启命令可能导致会话断开并无法使用ssh再行登入（即ssh未能成功重启），此时需要通过telnet登入再执行sshd服务重启命令。

### G、修改文件/etc/ssh/sshd_config
由于加固后的ssh（ssh6.7以上）屏蔽了不安全算法，需要在/etc/ssh/sshd_config最后加上如下信息，不然Python模块paromiko，无法连接加固后的机器：
```bash
KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
```

## 善后工作
新开启远程终端以ssh [ip]登录系统，确认一切正常升级成功后，只需关闭telnet服务以保证系统安全性即可。
```bash
mv /etc/securetty.old /etc/securetty
chkconfig  xinetd off
service xinetd stop
```
如有必要，可重新开启防火墙
```bash
service iptables start
chkconfig iptables on
```
如需还原之前的ssh配置信息，可直接删除升级后的配置信息，恢复备份。
ssh大版本升级尽量不要删除新生成的ssh文件，将原文件与新生成文件都保留一份。
原版本：
```bash
ssh -V
OpenSSH_5.3p1, OpenSSL 1.0.1e-fips 11 Feb 2013
```
升级至：
```bash
OpenSSH_7.4p1, OpenSSL 1.0.2n  7 Dec 2017
```
<font color="#FF6600">后如果执行**以下操作**，会导致**无法登陆**。</font>
<font color="#FF6600" size="16">**正确做法：**使用此次安装好的文件/etc/ssh，不要用原文件覆盖！不然ssh无法使用。</font>
```bash
#！！！！！！千万不要执行如下命令！！！！！！
#rm -rf /etc/ssh
#mv /etc/ssh.old /etc/ssh
```

## 遇到的错误-1：
```bash
make[2]: Entering directory `/tmp/openssl-1.0.2m'
[ -z "" ] || gcc -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -Wa,--noexecstack -m64 -DL_ENDIAN -O3 -Wall -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DRC4_ASM -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM -DECP_NISTZ256_ASM -Iinclude \
            -DFINGERPRINT_PREMAIN_DSO_LOAD -o fips_premain_dso  \
            fips_premain.c fipscanister.o \
            libcrypto.a -ldl -lz
make[3]: Entering directory `/tmp/openssl-1.0.2m'
make[4]: Entering directory `/tmp/openssl-1.0.2m'
/usr/bin/ld: /usr/lib/gcc/x86_64-redhat-linux/4.4.7/../../../../lib64/libz.a(deflate.o): relocation R_X86_64_32S against `_length_code' can not be used when making a shared object; recompile with -fPIC
/usr/lib/gcc/x86_64-redhat-linux/4.4.7/../../../../lib64/libz.a: could not read symbols: Bad value
collect2: ld returned 1 exit status
make[4]: *** [link_a.gnu] Error 1
make[4]: Leaving directory `/tmp/openssl-1.0.2m'
make[3]: *** [do_linux-shared] Error 2
make[3]: Leaving directory `/tmp/openssl-1.0.2m'
make[2]: *** [libcrypto.so.1.0.0] Error 2
make[2]: Leaving directory `/tmp/openssl-1.0.2m'
make[1]: *** [shared] Error 2
make[1]: Leaving directory `/tmp/openssl-1.0.2m/crypto'
make: *** [build_crypto] Error 1
```
解决办法：zlib编译时添加configure前面的配置：
```bash
CFLAGS='-mstackrealign -fPIC -O3' ./configure --prefix=/usr
```
## 遇到的报错-2：
```bash
make[2]: Entering directory `/tmp/openssl-1.0.2m'
[ -z "" ] || gcc -fPIC -DOPENSSL_PIC -DZLIB -DOPENSSL_THREADS -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -Wa,--noexecstack -m64 -DL_ENDIAN -O3 -Wall -DOPENSSL_IA32_SSE2 -DOPENSSL_BN_ASM_MONT -        DOPENSSL_BN_ASM_MONT5 -DOPENSSL_BN_ASM_GF2m -DRC4_ASM -DSHA1_ASM -DSHA256_ASM -DSHA512_ASM -DMD5_ASM -    DAES_ASM -DVPAES_ASM -DBSAES_ASM -DWHIRLPOOL_ASM -DGHASH_ASM -DECP_NISTZ256_ASM -Iinclude \
            -DFINGERPRINT_PREMAIN_DSO_LOAD -o fips_premain_dso  \
            fips_premain.c fipscanister.o \
            libcrypto.a -ldl -lz
make[3]: Entering directory `/tmp/openssl-1.0.2m'
make[4]: Entering directory `/tmp/openssl-1.0.2m'
/usr/bin/ld: error while loading shared libraries: libz.so.1: cannot open shared object file: No such file or directory
collect2: ld returned 127 exit status
make[4]: *** [link_a.gnu] Error 1
make[4]: Leaving directory `/tmp/openssl-1.0.2m'
make[3]: *** [do_linux-shared] Error 2
make[3]: Leaving directory `/tmp/openssl-1.0.2m'
make[2]: *** [libcrypto.so.1.0.0] Error 2
make[2]: Leaving directory `/tmp/openssl-1.0.2m'
make[1]: *** [shared] Error 2
make[1]: Leaving directory `/tmp/openssl-1.0.2m/crypto'
make: *** [build_crypto] Error 1
```
解决办法：
仔细检查提示路径下的libz.so文件是否正确。
## 遇到的报错-3：
现象：
用Python写的脚本调用到paramiko模块，模块ssh连接加固后的机器失败，报无法连接，但secureCRT可以连接。
运行paromiko连接远程服务器失败：FAIL : SSHException: Incompatible ssh peer (no acceptable kex algorithm)  
原因：
因为ssh6.7以上屏蔽不安全算法导致。
解决：
在/etc/ssh/sshd_config最后加上：
```bash
KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
```
重启sshd服务，问题解决。