### docker 方式启动

#### 简单复制集

```bash
rm -rf /data/dockerMongo/

#创建数据rs
docker run  --name mongoCluster1 --restart=always -v /data/dockerMongo/db1:/data/db:z -p 20001:27017 -d mongo -replSet rs0
docker run --name mongoCluster2 --restart=always -v /data/dockerMongo/db2:/data/db:z -p 20002:27017 -d mongo -replSet rs0
docker run --name mongoCluster3 --restart=always -v /data/dockerMongo/db3:/data/db:z -p 20003:27017 -d mongo -replSet rs0

#check
docker ps | grep mongo

#获取所有mongo ip
docker inspect $(docker ps | grep mongoCluster | awk 'NR > 0 {print $1}') | grep \"IPAddress

# 登录
docker exec -it $(docker ps | grep mongoCluster1 | awk 'NR > 0 {print $1}') mongo

# 创建复制集 ip 为宿主机ip
rs.initiate({ _id: "rs0", members: [{ _id : 0, host : "192.168.12.235:20001" }]})
#这边add 其他几台mongo的ip和地址
rs.add("192.168.12.235:20002")
rs.add("192.168.12.235:20003")

rs.config()
rs.status()


docker exec -it $(docker ps | grep mongoCluster2 | awk 'NR > 0 {print $1}') mongo
rs.slaveOk();
rs.status()

docker exec -it $(docker ps | grep mongoCluster3 | awk 'NR > 0 {print $1}') mongo
rs.slaveOk();
rs.status()
```

### 分片

暂时不用