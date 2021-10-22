## ss @ server

实在话，这个实在是无奈之举

所以策略是临时启动，不用时关闭



## centos7

```bash
sudo yum install epel-release -y
sudo yum install python-pip -y
pip --version

pip install shadowsocks

cat << EOF > /etc/ss.json
{
    "server": "your ip",
    "server_port": 8388,
    "local_address":"127.0.0.1",
    "local_port": 1080,
    "password": "your ss passwd", 
    "timeout":300,
    "method": "aes-128-cfb"
}
EOF

sslocal -c /etc/ss.json -d start


#privoxy
sudo yum -y install privoxy
cp /etc/privoxy/config /etc/privoxy/config.bak


echo 'forward-socks5 / 127.0.0.1:1080 .' >> /etc/privoxy/config


systemctl start privoxy
systemctl status privoxy


#pac
pip install --user gfwlist2privoxy

cd /tmp
wget https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
~/.local/bin/gfwlist2privoxy -i gfwlist.txt -f gfwlist.action -p 127.0.0.1:1080 -t socks5
sudo cp gfwlist.action  /etc/privoxy/

echo 'actionsfile gfwlist.action' >>  /etc/privoxy/config

systemctl restart privoxy
```



## 常用骚操作们

```bash
proxy="http://127.0.0.1:8118"
export https_proxy=$proxy
export http_proxy=$proxy
#export ftp_proxy=$proxy
export no_proxy="localhost, 127.0.0.1, ::1, ip.cn, chinaz.com"

# check
curl google.com

# start 
sslocal -c /etc/ss.json -d start
# stop
sslocal -c /etc/ss.json -d stop

```

#### 启动代理

```bash
sslocal -c /etc/ss.json -d start
systemctl restart privoxy


proxy="http://127.0.0.1:8118"
export https_proxy=$proxy
export http_proxy=$proxy


```

#### 关闭代理

```bash
sslocal -c /etc/ss.json -d stop
systemctl stop privoxy


proxy="http://127.0.0.1:8118"
export https_proxy=
export http_proxy=
```



### alias

```bash
alias fan='https_proxy="http://127.0.0.1:8118" && http_proxy="http://127.0.0.1:8118"'
alias unfan='https_proxy=  && http_proxy= '

```



## ssr

### install
```bash
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh
chmod +x shadowsocksR.sh
./shadowsocksR.sh 2>&1 | tee shadowsocksR.log

```

### op
```bash
#启动：
/etc/init.d/shadowsocks start
#停止：
/etc/init.d/shadowsocks stop
#重启：
/etc/init.d/shadowsocks restart
#状态：
/etc/init.d/shadowsocks status
#配置文件路径：
/etc/shadowsocks.json
#日志文件路径：
/var/log/shadowsocks.log
#代码安装目录：
/usr/local/shadowsocks
```
