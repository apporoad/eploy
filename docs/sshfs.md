### just do it

```bash
yum -y install epel-release # 安装epel 源码
yum -y install fuse-sshfs # 安装sshfs


# 找目标目录
mkdir -p /data/backup_all
chmod -R 777 /data

## 每台服务器执行
mkdir -p /data/backup
chmod -R 777 /data

## 先做好免登录
sshfs $user@xxx.xxx.xxx.xxx:/data/backup_all /data/backup -o allow_other  #$user为用户，一般为root，回车后还要输入密码

```

### test it

```bash

touch /data/backup/test

```