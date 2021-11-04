

### 快照

1. 创新共享目录
[简单共享目录](./sshfs.md)

2. 创建备份目录
```bash
curl -XPUT 'xxx.xxx.xxx.xxx:9200/_snapshot/backup' -H 'Content-Type: application/json'  -d '{"type": "fs", "settings": {"location": "/data/backup" }}'
```

3. 备份命令
```bash
# 备份
curl -XPUT 'xxx.xxx.xxx.xxx:9200/_snapshot/backup/bak_1'

```

### 恢复命令
```bash

curl -XPOST 'xxx.xxx.xxx.xxx:9200/_snapshot/backup/bak_1/_restore'
```

### 定时快照

```bash
#!/bin/bash
data1=`date "+%Y%m%d"`
data2="http://0.0.0.0:9200/_snapshot/backup/snapshot_${data1}"

echo '-----------'
echo $data1
echo $data2
echo '----------'

curl -XPUT $data2

deldata1=`date -d "7 day ago" +"%Y%m%d"` //保留7天
deldata2="http://0.0.0.0:9200/_snapshot/backup/snapshot_${deldata1}"

curl -XDELETE $deldata2

echo "------------"

echo $deldata1
echo $deldata2
echo "-------"
```