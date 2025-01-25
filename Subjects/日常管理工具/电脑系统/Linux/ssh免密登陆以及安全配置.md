---
title: ssh免密登陆以及安全配置
authors: Ethan Lin
year: 2023-04-08 
tags:
  - 日期/2023-04-08 
  - 类型/笔记 
  - 来源/转载 
  - 传播/禁止 
---


# ssh免密登陆以及安全配置









# 来源

> [ssh免密登陆以及安全配置](https://kz16.top/remote/ssh.html#ssh免密登陆以及安全配置)



Author : zbzhen,       Last modification time : Sat Apr 1 16:52:42 2023

# 1. 常用命令

```
# 查看已经登陆和登陆失败的最近30个用户
last -30

lastb -30
```

查看多少人在暴力登陆root

sudo grep "Failed password for root" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | more

# 2. 查看 ECDSA key fingerprint

下面来源于ai回答

## 2.1. 提问

怎么获取本机器上的 ECDSA key fingerprint is SHA256

## 2.2. 回答

您可以使用以下命令获取本机器上的 ECDSA key fingerprint is SHA256：

```
ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub
```

该命令将输出 ECDSA key 的 SHA256 指纹。如果您想获取其他类型的 SSH key 的指纹，可以将命令中的“ecdsa”替换为“rsa”或“ed25519”。

# 3. 免密登陆

推荐本地用linux系统, 可以很方便处理

-   本地终端输入ssh-keygen, 一路回车即可  
    创建记录里, 会出现id_rsa.pub文件的全路径
    
-   `ssh-copy-id -i ~/.ssh/id_rsa.pub yourname@10.10.10.001`  
    或者用这个
    
    `ssh yourname@10.10.10.001 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub`
    
-   下次直接输入`ssh yourname@10.10.10.001`可以不用密码
    

如果是本地是win系统, 则先用某台linux系统整一次免密登陆, 然后执行下面操作

-   本地终端输入ssh-keygen, 一路回车即可  
    创建记录里, 会出现id_rsa.pub文件的全路径
    
-   把本地的id_rsa.pub中的所有内容, 写入到服务器里的~/.ssh/authorized_keys文件中, 就可以实现免密登陆。通过输入以下实现：
	    `sudo echo '【你的公钥文件里面的全部内容】' >> ~/.ssh/authorized_keys`

---

-   相对来说安全
-   但是可能有被一锅端的风险

## 3.1. 禁用密码登陆

vim /etc/ssh/sshd_config

然后把

PasswordAuthentication yes
ChallengeResponseAuthentication yes

修改成

PasswordAuthentication no
ChallengeResponseAuthentication no

修改后, 重启服务

```
sudo systemctl reload sshd
sudo systemctl restart sshd
```

# 4. 安全配置

密码登陆安全性低, 但是非常方便, 为此, 可以设置安全保护

密码带有大小写+数字+字符, 至少二十位

密码试错, 超过3次直接锁定4天

需要安装和配置fail2ban

## 4.1. 安装和配置fail2ban

# 安装
sudo apt install fail2ban

# 配置
sudo vim /etc/fail2ban/jail.conf

# 快捷键i进入编辑模式, Esc退出编辑模式 Shift + z + z保存退出 
# 在[DEFAULT]区设置
# 2次错误则封360000秒 = 100时 
bantime = 360000
findtime = 360000
maxretry = 2

# 重启
sudo service fail2ban restart

## 4.2. 检测与解封锁

设置成功之后用命令查看是否成功

# 输入
sudo fail2ban-client ping

# 输出的是下面命令则表示成功
Server replied: pong

检验监狱状态

sudo fail2ban-client status

查看监狱ip

fail2ban-client status sshd

或者更加全面的和清爽的格式

awk '($(NF-1) = /Ban/){print $NF}' /var/log/fail2ban.log | sort | uniq -c | sort -n

解锁特定封禁ip地址

sudo fail2ban-client set sshd unbanip 192.168.1.1

# 5. 参考

-   [https://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html](https://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
-   [https://www.mobibrw.com/2021/29301](https://www.mobibrw.com/2021/29301)
-   [https://www.mobibrw.com/2018/11231](https://www.mobibrw.com/2018/11231)
-   [https://serverfault.com/questions/841183/how-to-show-all-banned-ip-with-fail2ban](https://serverfault.com/questions/841183/how-to-show-all-banned-ip-with-fail2ban)