## 采用conda方式
[anaconda](https://www.anaconda.com/) 
```
sh xxxxx
#环境变量配置

conda init 

exit

# 重新登陆即ok

```

## centos
```bash

#依赖
sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel  cc gcc libffi-devel

#download
mkdir -p /data/pack
cd /data/pack
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tar.xz

tar -xf Python-3.7.3.tar.xz

cd Python-3.7.3

#编译安装
./configure --prefix=/home/python3
make && make install



sudo ln -s /home/python3/bin/python3 /usr/bin/python

sudo mv /usr/bin/python /usr/bin/python2

#pip
sudo ln -s /home/python3/bin/pip3 /usr/bin/pip

```

## 特殊处理

```bash
vim  /usr/bin/yum
# #!/usr/bin/python2.7


vim /usr/libexec/urlgrabber-ext-down
# #!/usr/bin/python2.7
```



## pip 镜像

```bash
~/.pip/pip.conf 
```

```config
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
```

