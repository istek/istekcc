---
title: DB2数据库部署指南
date: 2015-12-05T06:12:10+00:00
layout: post
categories:
  - 我的世界
tags:
  - 数据库
---
前些日子，根据公司安排，割接公司下的一个系统至客户资源池，应用中间件是tongweb，可以说真的不好使，没有weblogic好用，稳定。而数据库则是沿用以前的DB2数据库，而该数据库，我还真没有操作过，也没有用过，更别说什么部署了，以下文档是我同事转给我的，是以前公司部署环境的文档，修改后，发表至这里，希望对某些人有用。服务器系统是`solaris 10`，建议使用之前，将默认shell修改为bash，通过修改.profile完成，以及在`.profile`中增加一些常用的命令alias。
<!--more-->
# 一、硬件检查

## 1. 检查内存
```
/usr/sbin/prtconf | grep -i memory
```

内存至少是1GB

![1](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214647/120415_0430_1_dfnios.png)

## 2. 检查硬盘
```
#df –k
```

硬盘3GB以上。
![2](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214646/120415_0430_2_qj6il0.png)

## 3. 检查swap
```
# swap -l
```

Swap大小如下：

- 内存1GB–2GB,SWAP是内存的1.5倍。
- 内存2GB–8GB,SWAP是内存的1倍,即与内存同大小。
- 内存8GB以上,SWAP是内存的0.75倍。

![3](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214645/120415_0430_3_qui8zq.png)

## 4. 检查tmp
```
# df -k /tmp
```

/tmp大小应大于400MB.

![4](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214645/120415_0430_4_mcjbls.png)

## 5. 检查操作系统是否64位
```
#isainfo -b
```

![5](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214644/120415_0430_5_m5a7re.png)

## 6. 检查操作系统版本
```
#uname –a
```

![6](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214643/120415_0430_6_uhpngw.png)

## 7. 检查文件包有无安装
```
#less /var/sadm/install/contents
```

![7](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449214642/120415_0430_7_j0p68p.png)

![8](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211645/120415_0430_8_mscnud.png)

## 8. 检查补丁安装
```
# patchadd -p
```

（检查系统的补丁情况）

![9](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211644/120415_0430_9_hhwxrz.png)
```
# showrev -p
```

（查看所有已经安装的patch）

![10](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211643/120415_0430_10_ydxsn5.png)

![11](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211643/120415_0430_12_jhi6b2.png)

# 二、 DB2安装要求

![12](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211642/120415_0430_13_i0zzqo.png)

# 三、创建用户组(可选)

xxxx系统使用的操作系统用户统一规定如下：

|用户名（UID）|用户组（GID）|home目录 | 说明 |
|————|————|———-|———|
|db2inst1（300）|bcdl（300）|/opt/db2inst1|JDK、DB2用户|
|cmbbcd（304）|bcdl（300）|/opt/cmbbcd |连接数据库用户|

## 1. 操作步骤

* 以root登录系统
* 创建bcdl用户组
```
groupadd -g 300 bcdl
```

* 创建cmbbcd用户
```
useradd -g bcdl -u 300 -d /opt/cmbbcd -m cmbbcd
```

* 设定bcdl用户密码
```
passwd cmbbcd
```

输入两次用户密码

## 2. 验证方法

* 以cmbbcd用户登录系统
* 输入以下命令确认UID、GID、用户组正确
```
$ id
uid=300(cmbbcd) gid=300(bcdl)
```

# 四、上传安装文件

为方便部署，在开始部署系统前将相关软件上传到solaris主机上，存放目录统一定为`/opt/software`。

1. 以root登录系统
2. 创建`/opt/software`目录
```
mkdir /opt/software
```

* 将software目录赋予bcdl用户
```
chown -R bcdl:bcdl /opt/software
```

* ftp连接solaris主机，以用户bcdl登录，切换目录到`/opt/software`，将后续部署需要的JDK、DB2安装文件上传。

# 五、JDK安装配置（JDK6）

## 1. 安装步骤

1. 以root用户登录系统
2. 进入JDK安装文件所在目录`/opt/software`:`cd /opt/software`
3. 将JDK安装文件复制到`/opt/bcdl`:`cp jdk*.sh /opt/bcdl`
4. 进入/opt/bcdl目录，为JDK安装文件增加执行权限:`cd /opt/bcdl chmod +x jdk*.sh`
5. 将JDK安装文件赋予bcdl用户 :`chown bcdl:bcdl jdk*.sh`
6. 切换到bcdl用户:`su – bcdl`
7. 执行以下命令安装JDK :`cd /opt/bcdl ./jdk-6u7-solaris-sparc.sh`
8. 按多次空格确认协议，直至出现以下界面，输入yes并回车
![13](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211641/120415_0430_14_nx8k6t.png)
9. 安装开始后出现以下信息表示安装结束
![14](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211640/120415_0430_15_pl5ww4.png)

## 2.部署验证

1. 进入JDK安装目录
```
cd /opt/bcdl/jdk1.6.0_07/bin
```

2. 输入以下命令
```
./java -version
```

显示以下信息

![15](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211639/120415_0430_16_wkdvzm.png)

## 3.程序卸载（非必要）

jdk程序的卸载只要删除安装目录下的所有文件即可.

命令如下:
```
# cd /opt/bcdl //进入bcdl文件夹
# rm -Rf jdk1.6.0_07 //删除JDK
```

# 六、DB2数据库安装配置

## 1. DB2数据库安装

1. 启动Xmanager连接solaris主机，以root用户登录
2. 右键点击桌面，在菜单中选择Hosts 》 Terminal Console打开命令行窗口
3. 进入DB2安装文件所在目录`/opt/software`:`cd /opt/software`
4. 解压DB2压缩包:`tar xvf db2_v95_sun64_server.tar`
5. 解压后进入server目录:`cd server`
6. 启动DB2安装程序（**务必用root用户启动安装程序**）:`./db2setup`
7. 选择”安装新产品(p)”，如下图
![16](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211639/120415_0430_17_owdyma.png)
选择安装”DB2 Enterprise Server Edition Version 9.5″版本
8. 进入安装向导，点击”下一步”；
![17](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211638/120415_0430_18_vnfkzt.png)
9. 接受软件许可证协议，点击”下一步”；
![18](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211637/120415_0430_19_awfkd3.png)
10. 选择典型安装类型(Typical)，点击”下一步”；
![19](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211635/120415_0430_20_o5poib.png)
11. 选择”在此计算机安装DB2企业服务器版本9.5并将设置保存在响应文件中”，点击”下一步”；
![20](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211634/120415_0430_21_zhwms9.png)
12. 选择安装的路径，安装目录统一定为`/opt/IBM/db2/V9.5`；
![21](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211631/120415_0430_22_jjlapy.png)
13. 设定DB2管理服务器的用户：取消`Use default UID`和`Use default GID`前的选择，修改`UID`和`GID`为`301`，设置`dasusr1`密码，选择”下一步”（指定`UID`和GID`是为了对DB2作集群，DB2集群时需要保证各机器上相同用户及组拥有相同的`UID`和`GID\`，对15步和16步的操作原因与此相同）。
![21](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211633/120415_0430_23_xzbsd4.png)
14. 选择”创建DB2实例”，选择下一步；
![22](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211632/120415_0430_24_xlgkts.png)
15. 设定DB2实例的用户：取消`Use default UID`和`Use default GID`前的选择，给db2实例的用户设置密码，设置`UID`和`GID`为`302`，点击”下一步”
![23](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211630/120415_0430_25_oahep1.png)
16. 设定DB2用户自定义函数的用户：取消`Use default UID`和`Use default GID`前的选择，设置`UID`和`GID`为`303`，点击”下一步”
![24](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211629/120415_0430_26_ufc3ek.png)
17. 选择”在此计算机上不准备DB2工具目录”，点击”下一步”
![25](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211628/120415_0430_27_nd2ips.png)
18. 选择”不将DB2服务器设置为此时发送通知”，点击”下一步”
![26](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211542/120415_0617_DB228_kcnhju.png)
19. 总结信息，确认各项参数是否正确（用户、UID、GID等）。点击”完成”
![27](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211542/120415_0617_DB229_knabxx.png)
20. 安装进度提示
![28](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211541/120415_0617_DB230_cyjjl5.png)
21. DB2软件安装完成，将”Log File”文本框内容进行备份，以备后续使用。
![29](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211539/120415_0617_DB231_sn1yk4.png)
![30](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211539/120415_0617_DB232_w1kqtj.png)
22. 为便于后续操作，将db2inst1用户的语言环境设为中文`/opt/software`,将光标移动到文件最后，增加以下内容，保存并退出vi`/opt/software`

## 2. 操作系统参数调整

**此步骤请务必慎重操作！**

1. 以root用户登录
2. 输入以下命令获取DB2建议的操作系统参数值cd /opt/IBM/db2/V9.5/bin ./db2osconf
3. 执行命令后会显示建议参数值（实际环境参数值可能与图中有差异，以实际环境显示为准）
![31](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211538/120415_0617_DB233_ojmd6f.png)
4. 编辑系统参数文件
`vi /etc/system`将光标移动到文件最后，将上图中set开头的行复制到文件中（实际环境参数值可能与图中有差异，以实际环境显示为准）
![32](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211537/120415_0617_DB234_zi7w40.png)保存并退出vi
5. 重新启动操作系统
```
cd /opt/software
```
## 3. 启动数据库相关服务

1. 以root用户登录
2. 启动DB2管理服务器
`. /export/home/dasusr1/das/dasprofile db2admin start`看到以下信息表示DB2管理服务器启动成功
![33](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211536/120415_0617_DB235_jcq05m.png)如看到以下信息表示DB2管理服务器已经在运行中
3. 切换到db2inst1
`/opt/bcdl`
启动db2数据库实例
`cp jdk*.sh /opt/bcdl`
看到以下信息表示DB2数据库实例启动成功
![34](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211535/120415_0617_DB237_fdg0ft.png)
如看到以下信息表示DB2数据库实例已经在运行中
![35](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211533/120415_0617_DB238_xojxxj.png)

## 4. 备份数据库
```
cd /opt/bcdl chmod +x jdk*.sh
```

## 5. 还原数据库

用UE编辑`creatab.sql`，修改”数据库连接名称”、”表的schame”等信息。另外，还需要编辑`DB2MOVE`文件夹下的`db2move.lst`文件，修改”表的schame”等信息。将修改过的这两个文件上传至新服务器:
```
chown bcdl:bcdl jdk*.sh
```
## 6. 更新license

1. 以root用户登录系统
2. 准备好db2 license文件，执行以下命令导入license
```
su – bcdl
```

执行后如无异常，会显示以下结果
![36](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211326/120415_0617_DB239_dqpykf.png)

1. 显示当前部署的license信息
```
cd /opt/bcdl ./jdk-6u7-solaris-sparc.sh
```
如下图所示，请确认license类型和过期时间
![37](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1449211324/sdfasd1_bnofsx.png)

## 7. 部署验证

1. 测试是否可以成功连接bcdl数据库，用db2inst1用户登录solaris，执行以下命令：
```
$ db2 connect to bcdl user cmbbcd using cmbbcd
```
2. 查询用户表，测试是否可以查出记录
```
$ db2 select * from cmcs3_user
```
## 8. 程序卸载

1. 以root用户登录
2. 初始化环境变量
```
. /export/home/dasusr1/das/dasprofile
```
3. 停止db2管理服务器
```
db2admin stop
```
4. 删除db2管理服务器
```
/opt/IBM/db2/V9.5/instance/dasdrop
```
看到以下信息表示删除成功
`DBI1070I Program dasdrop completed successfully.`
5. 切换到db2inst1用户
```
su – db2inst1 停止db2服务
$ db2stop force
$ db2 terminate 退回root用户
$ exit
```
6. 删除db2实例
```
/opt/IBM/db2/V9.5/instance/db2idrop db2inst1
```
看到以下信息表示删除成功
`DBI1070I Program db2idrop completed successfully.`
7. 删除db2应用
```
/opt/IBM/db2/V9.5/install/db2_deinstall -a
```
看到以下信息表示成功
`The execution completed successfully.`
8. 删除db2相关用户
```
cd /opt/bcdl/jdk1.6.0_07/bin
groupdel dasadm1
groupdel db2iadm1
groupdel db2fadm1
```

## 9. 常见问题

1. 检查实例的诊断文件`db2diag.log`文件（默认放在DB2安装目录下），可定位错误。
2. 查DB2联机帮助,系统会给出基本解决方法。 `$ db2 ? sql0001`
3. 查看IBM的信息中心

http://publib.boulder.ibm.com/infocenter/db2luw/v9r5/index.jsp?topic=/com.ibm.db2.luw.admin.config.doc/doc/r0006012.html 请下载各操作系统下DB2V9.5的补丁，可用补丁直接安装，不用原安装文件。
下载：[DB2 Server Fix Pack](http://www-1.ibm.com/support/docview.wss?rs=71&uid=swg21288088)

