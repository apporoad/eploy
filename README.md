# eploy
easy to deploy   

step by step install deployments



### list

1. [tools](./docs/tools.md)
2. cnpm私服  [sinopia](docs/sinopia.md)  node多源切换[nrm](./docs/nrm.md)
3. [fastdfs in centos](./docs/fastdfs/fastdfs.md)  [fastdfs管理](./docs/fastdfs/fastdfs_manger.md)
4. [matomo on centos7](./docs/matomo.md)
5. [mysql](./docs/mysql.md)
6. [redis](./docs/redis.md)
7. [mongodb](./docs/mongodb.md)    [集群](./docs/mongodbCluster.md)
8. [python3 in centos](./docs/python3.md)
9. [docker](./docs/docker.md) 相关
10. [nginx](./docs/nginx.md)
11. [tomcat](./docs/tomcat.md)
12. [gitlab](./docs/gitlab.md)
13. [ssh 免密](./docs/sshLogin.md)
14. [hubot + bearychat](./docs/hubot.md)
15. [jenkins](./docs/jenkins.md)
16. [ceph](./docs/ceph.md)
17. [ansible](./docs/ansible.md)
18. [wekan](./docs/wekan.md) kanboard
19. [rocketchat](./docs/rocketchat.md)
20. [kafka](./docs/kafka.md)
21. [pm2](./docs/pm2.md)
22. [c env](./docs/c.md)  container env of all
23. [ss](./docs/ss.md)
24. [apollo](https://github.com/hyd-raiders/C4.apollo)
25. [sh](./docs/sh.md)
26. [ss @ server](./docs/ss_server.md)
27. [postgres](./docs/pg.md)
28. [qurid](./docs/qurid.md)  proxy
29. [git私服](./docs/git.md)
30. [groovy 常用操作脚本](./docs/groovy.md)
31. [nextcloud 快速安装](./docs/nextcloud.md)
32. [openvpn 快速安装](./docs/openvpn.md)
33. [sentry 安装](./docs/sentry.md)
34. [nexus 安装](./docs/nexus.md)
35. [rabbitmq 安装](./docs/rabbitmq.md)
36. [minikube 安装](./docs/minikube.md)
37. [k8s 使用范例](./docs/k8s.md)
38. [svn 使用](./docs/svn.md)
39. [安全测试工具](./docs/安全测试工具.xlsx)
40. [openvas](./docs/openvas.md)
41. [minidoc 简单文档管理系统](./docs/mindoc.md)
42. [sshfs 文件共享](./docs/sshfs.md)
43. [es elasticsearch相关](./docs/es.md)
44. [uni-app 脚本打包](./docs/uniapp.md)
45. [json自定义编辑](./packs/jsonEdit.zip)

## cmds

### ab test
```bash
$ cat test.json
json='{ "timestamp" : 1484825894873, "test" : "test"}'
ab -c 10 -n 1000 -p  test.json -T application/x-www-form-urlencoded  https://example.com/test
#https://prabuddha.me/apache-bench-ab-post-json-api/
```

### 禁用selinux
```bash
sed -i 's/=enforcing/=disabled/' /etc/selinux/config
```

### 防火墙
```bash
firewall-cmd --zone=public --add-port=9392/tcp --permanent
firewall-cmd --reload
```