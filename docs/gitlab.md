##  docker

```bash
docker pull gitlab/gitlab-ce:rc


mkdir -p /home/gitlab/config
mkdir -p /home/gitlab/logs
mkdir -p /home/gitlab/data

sudo docker run --detach \
  --publish 20443:20443 --publish 20080:20080 --publish 20022:20080 \
  --name gitlab \
  --restart always \
  --volume /home/gitlab/config:/etc/gitlab \
  --volume /home/gitlab/logs:/var/log/gitlab \
  --volume /home/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:rc
  
  # 修改配置
  vim /home/gitlab/config/gitlab.rb
  #external_url 'http://192.168.12.231:20080'
  #nginx['listen_addresses'] = ['*']
  #nginx['listen_port'] = 20080
  # unicorn['listen'] = '127.0.0.1'
  # unicorn['port'] = 8080
  vim /home/gitlab/data/gitlab-rails/etc/gitlab.yml
  #找到关键字 * ## Web server settings * 
#将host的值改成映射的外部主机ip地址和端口，这里会显示在gitlab克隆地址

docker exec -ti gitlab /bin/bash
```

