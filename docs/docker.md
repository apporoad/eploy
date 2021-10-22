## 搭建私服

```bash
#Secure Registry 非安全模式docker官网查看

#自己制作证书
mkdir -p /data/certs/ && cd /data/certs
openssl req -newkey rsa:2048 -nodes -sha256 -keyout /data/certs/domain.key -x509 -days 365 -out /data/certs/domain.crt

#Country Name (2 letter code) [AU]:CN
#State or Province Name (full name) [Some-State]:zj
#Locality Name (eg, city) []:sz
#Organization Name (eg, company) [Internet Widgits Pty Ltd]:xxx
#Organizational Unit Name (eg, section) []:xxx
#Common Name (e.g. server FQDN or YOUR name) []:docker.server
#Email Address []:xxx@xxx.com

mkdir -p /data/registry
docker pull registry:2

docker run -d -p 5000:5000 --restart=always --name registry \
  -v /data/registry:/var/lib/registry \
  -v /data/certs:/certs \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  registry:2






```

## 访问配置及测试

```bash
sudo mkdir -p /etc/docker/certs.d/docker.server:5000
sudo cp /data/certs/domain.crt /etc/docker/certs.d/docker.server:5000/ca.crt
sudo service docker restart

# 根据实际ip
echo "xx.xxx.x.x  docker.server" >>/etc/hosts

docker pull busybox

docker tag busybox docker.server:5000/busybox

docker push docker.server:5000/busybox

```

