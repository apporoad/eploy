## server
### step

```bash
yum -y install epel-release
yum install python-pip -y
pip install --upgrade pip
pip install shadowsocks

yum -y install vim wget curl
yum -y install python-setuptools && easy_install pip && pip install --upgrade pip

pip install shadowsocks
```

```bash
vim /etc/shadowsocks.json
```

```json
{ "server": "0.0.0.0", "server_port": 18388, "password": "save11", "method": "aes-256-cfb" }
```

#### 启动服务

```bash
## start 
ssserver -c /etc/shadowsocks.json -d start

## stop
ssserver -c /etc/shadowsocks.json -d stop
```

[details @ here](https://crifan.github.io/scientific_network_summary/website/server_client_mode/ss_server/self_build_ss_server.html)

### bbr

[site](https://www.vultr.com/docs/how-to-deploy-google-bbr-on-centos-7)


## client @ ubuntu

```bash
 sudo apt install shadowsocks
  sudo vim /etc/shadowsocks/config.json 
```

```json
{
"server": " your server",
"server_port": 1111, 
"local_address": "127.0.0.1",
"local_port": 1080,
"password": "xxxxxx",
"timeout": 300,
"method": "aes-128-cfb",
"fast_open": false,
"workers": 1
}
```

```bash
# start ss
sslocal -c /etc/shadowsocks/config.json
```
#### http proxy
```bash
sudo apt-get install privoxy
sudo vim /etc/privoxy/config
```
```
forward-socks5   /               127.0.0.1:1080 .

listen-address localhost:8118
```

```bash
sudo systemctl restart privoxy
#just test it
wget -e "http_proxy=127.0.0.1:8118" http://www.google.com

```
#### pac
参考 https://blog.csdn.net/zhujiayou/article/details/90320324