## 安装

```bash
mkdir -p /data/svn
mkdir -p /data/svn/repo
mkdir -p /data/svn/config

touch /data/svn/config/passwd
touch /data/svn/config/subversion-access-control

cd /data/svn/

chmod 777 -R /data/svn

vim /data/svn/config/subversion-access-control
```

```config
[groups]

[/]
*=r

```
repo：存放svn版本库  
config: 存放svn配置  
passwd：svn中用户配置  
subversion-access-control：svn的访问权限配置  


```bash
cd /data/svn

vim docker-compose.yml
```

```yaml
svn:
      image: elleflorio/svn-server
      restart: always # 容器退出时重启策略，always表示总是重启
      container_name: svn-server
      volumes:
        - /data/svn/repo/:/home/svn/ # 挂载存放版本的文件夹
        - /data/svn/config/:/etc/subversion/ # 挂载存放svn配置的文件夹(passwd、subversion-access-control)
        - /etc/localtime:/etc/localtime:ro # 将主机的时间同步到容器
      ports:
        - "13690:80" # 端口映射,主机:容器
        - "3690:3690" # 端口映射,主机:容器
```

```bash
docker-compose up -d
```

### 访问并配置
visit http://localhost:13690/svn/  

visit http://localhost:13690/svnadmin/  

1. Subversion 授权文件  :  /etc/subversion/subversion-access-control
2. 用户身份验证文件 (SVNUserFile) :  /etc/subversion/passwd
3. 代码仓库的父目录 (SVNParentPath) : /home/svn
4. 'svn.exe' 或 'svn'可执行文件： /usr/bin/svn
5. 'svnadmin.exe' 或 'svnadmin' 可执行文件： /usr/bin/svnadmin

### other
略