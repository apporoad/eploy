# jenkins

## install

略



## 注意点

1. 插件建议手动安装

2. 需要使用thinBackup进行备份

3. gitlab 通过 webhook 触发构建（dev  fat）

4.  uat  pro环境 通过 url方式带参数 构建，同时打上tag

5. 构建可以通过hubot来调用构建

   like ： curl -X POST "http://ci:111fbfce7572acaacd14ecbf013a22ea7b@192.168.12.231:28080/jenkins/job/xdo-pms-api(dev)/build?token=xdo-pms-api"

