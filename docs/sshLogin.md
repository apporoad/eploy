## linux

```bash
#client run here
ssh-keygen
#enter enter enter
cd  && cd .ssh && ls

# 注册到服务器端 
ssh-copy-id -i ~/.ssh/id_rsa.pub root@xxx.xxx.xx.xx

# cat @ server
cat ~/.ssh/authorized_keys
```



## config ssh

```bash
cat ~/.ssh/config
```

```yaml
Host 231
	HostName 192.168.12.231
	User root
	Port 22
```

## config windows
```bash
# 1. send pub file  to  /root/.ssh  (or other user's .ssh)
# 2. cat yourPub  >> authorized_keys

mkdir -p /root/.ssh
cd /root/.ssh
touch authorized_keys
chmod 600 authorized_keys
cat yourPub >> authorized_keys


```


## 禁止使用密码登录
```bash
vim /etc/ssh/sshd_config

# PasswordAuthentication no

systemctl restart sshd
```