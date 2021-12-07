## docker-compose 方式安装

```bash
mkdir -p /data/nexus
cd /data/nexus
vim docker-compose.yml

```

```yml
version: '3'
services:
  nexus:
      restart: always
      image: sonatype/nexus3:3.7.1
      volumes:
        - /data/nexus:/nexus-data
      ports:
        - "8082:8081"
        - "8888:8888"
        - "8889:8889"
```
其中8888为 docker 镜像的HTTP connector ， 可以指定其他的

```bash
mkdir -p /home/nexus

chown -R 200 /home/nexus


docker-compose up -d

## admin password

cat /home/nexus/admin.password
```

## 手动安装


## config repo
https://blog.csdn.net/nklinsirui/article/details/90299350


## 连接测试
1. 建立docker repo hosted 方式， 指定http 接口 为 8888
2. 配置安全域名
```bash
 vim /etc/docker/daemon.json
```
如,当然域名也可以
```json
{
"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"],
"insecure-registries": ["ip:8888"]
}
```
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

3. 登录并上传镜像
```bash
docker login -u admin -p admin123 ip:8888

docker pull hello-world

docker tag hello-world:latest ip:8888/test:1

docker push ip:8888/test:1

```

4. 拉取镜像
建议用group方式先拉私仓，再拉公仓
```bash

docker pull ip:8889/test:1

```