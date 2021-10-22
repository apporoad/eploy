### snap first

```bash
yum makecache fast

yum install yum-plugin-copr epel-release -y

yum copr enable ngompa/snapcore-el7 -y

yum install snapd -y

systemctl enable --now snapd.socket

systemctl start snapd
```



## wekan

```bash
snap install wekan

snap set wekan root-url='http://devops.dcjet.com.cn:52000/wekan'

#port
snap set wekan port='11544'

systemctl restart snap.wekan.wekan

#email
sudo snap set wekan mail-url='smtps://user:pass@mailserver.example.com:453'
sudo snap set wekan mail-from='xxxx@xxxxx'

# stop snap refresh
sudo snap set system refresh.metered=hold
 
#nginx proxy
```

```ng

http {
    map $http_upgrade $connection_upgrade {
        default upgrade;
        ''      close;
    }

    server {
        ...

        location /wekan/ {
            proxy_pass http://xxxxxxxxxxxx:11544/wekan/;
            proxy_redirect  default;
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header Host  $host:11544;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
        }
    }
```





## mongo备份

```bash
# 外网访问
snap set wekan mongodb-bind-ip="0.0.0.0"
```

