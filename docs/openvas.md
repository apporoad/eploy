
### step by step
```bash

docker pull mikesplain/openvas

docker run -d -p 9323:443 --name openvas mikesplain/openvas

curl -v https://127.0.0.1:9323
# admin/admin

```


### 问题们
open the config file /etc/default/openvas-gsa and add you host header to ‘ALLOW_HEADER_HOST=you host’