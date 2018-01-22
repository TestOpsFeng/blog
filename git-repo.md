---
title: 在CentOS6下搭建git服务器
---
由于一个比赛需要用到git，我团队的两个成员不熟悉使用，所以自己搭个git服务器方便学习。刚好前段时间买了个vps，只是用来搭个梯子比较浪费，就折腾一下在vps上搭建git。下面介绍一下自己的搭建过程。
### 1. 先连接vps ### 
我用的是Xshell客户端来连接，可以试一下无密码登陆。这里要说一下，ssh-copy-id是在电脑终端输入的，不是Xshell。（我还一直找原因 [捂脸]）
### 2.安装git ###
>[root@4245 ~]# yum install -y git

### 3.创建仓库 ###
>[root@4245 ~]# adduser git

这里注意一下，不要在跟目录创建仓库，切换用户再进行创建：
>[root@4245 ~]# su git
[git@4245 ~]$ cd
[git@4245 ~]# git init –bare mycode.git

### 4.生成SSH KEY ###
在客户端生成ssh key：
>$ ssh-keygen -t rsa

默认的RSA长度是2048位．如果你非常注重安全，那么可以指定4096位的长度．
>$ ssh-keygen -b 4096 -t rsa

### 5.把公钥添加到服务器 ###
进入到git用户根目录，然后:
>[git@4245 ~]$ mkdir .ssh
[git@4245 ~]$ chown 700 .ssh

权限一定要700,别的都会导致失败
>[git@4245 ~]$ cd .ssh
[git@4245 .ssh]$ vim authorized_keys

把公钥的内容就是C:\Users\username.ssh\id_rsa.pub的内容复制进入authorized_keys，然后写入并退出就行了。记录一下vim的操作，按键盘 i 进行insert模式，写完之后按Esc退出insert模式，然后 : ，w , q ，可以在底端看到:wq，然后回车就保存并退出了。最后,要把权限修改成600.
>[git@4245 .ssh]$ chmod 600 authorized_keys

**注意**：这里的权限也一定要600。
### 6.打开RSA认证 ###
在/etc/ssh/sshd_config中将RSA认证打开：
>vim /etc/ssh/sshd_config

然后找到下面属性并修改成
>RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

然后在终端进行克隆，就可以成功免密码把仓库下载下载。
>git clone git@xx.xx.xx.xx:/home/git/mycode.git

### 7.添加开发者 ###
拿到要授权的开发者公钥，把公钥添加到git/.ssh/authorized_keys就可以了。
### 8.禁止git用户登陆服务器 ###
目前所有（获得授权的）开发者用户都能以系统用户 git 的身份登录服务器从而获得一个普通 shell。 如果你想对此加以限制，则需要修改 passwd 文件中（git 用户所对应）的 shell 值。
可以通过编辑/etc/passwd文件完成
>[root@4245 .ssh]# vim /etc/passwd

把最后一行：
>git:x:xxx:xxx::/home/git:/bin/bash

也可以参考[官方文档](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E9%85%8D%E7%BD%AE%E6%9C%8D%E5%8A%A1%E5%99%A8)。然后再尝试用git用户登陆就会提示：
>fatal: What do you think I am? A shell?

### 总结 ###
比较坑人就是那个权限问题。去看了官方文档之后，试了下除了规定的权限外别的权限都会导致失败。

## 参考资料 ##
[在VPS上搭建Git服务器](http://liujinlongxa.com/2016/08/07/%E5%9C%A8VPS%E4%B8%8A%E6%90%AD%E5%BB%BAGit%E6%9C%8D%E5%8A%A1%E5%99%A8/)

[在CentOS下搭建自己的Git服务器](http://blog.csdn.net/wave_1102/article/details/47779401)

[服务器上的-Git-配置服务器](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E9%85%8D%E7%BD%AE%E6%9C%8D%E5%8A%A1%E5%99%A8)