## 安装

```bash
npm install -g sinopia
```

## 启动

```bash
sinopia
```

## 配置

修改配置文件
```bash
find / -name sinopia
#cd..
vim config.yaml
```

```yaml

uplinks:
  npmjs:
    url: http://registry.npm.taobao.org/
packages:
  '@*/*':
    # scoped packages
    access: $all
    publish: $authenticated
    # 添加代理配置
    proxy: npmjs

#配置端口，并外网可访问
listen: 0.0.0.0:4873  

```

## 服务化

```bash
npm install -g pm2

pm2 start `which sinopia`
```





## 用户客户端安装及配置

```bash

npm set registry http://yourDNS:4873

#设置代理
npm config set proxy=http://127.0.0.1:4873
# https
npm config set https-proxy http://server:4873

# 取消代理
npm config delete proxy
npm config delete https-proxy

```

## 特殊处理

找到sinopia全局安装包中的 up-storage.js 文件, **不是config.yaml所在目录,是在全局安装包的目录下**,修改up-storage.js中的encode为

```js
var encode = function(url){
        return encodeURIComponent(url).replace(/^%40/,"@")
}
```

