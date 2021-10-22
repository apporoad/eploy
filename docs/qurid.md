## install @ centos7

```bash
yum install squid -y

vi /etc/squid/squid.conf

```


```
// add before http_access deny all 
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
acl auth_user proxy_auth REQUIRED
http_access allow auth_user

//modify your port
http_port 9090

//add end of file
request_header_access X-Forwarded-For deny all
request_header_access From deny all
request_header_access Via deny all
```

```bash
yum -y install httpd-tools

# now set user and pwd
htpasswd -c /etc/squid/passwd yourUsername

# port

firewall-cmd --zone=public --add-port=9090/tcp --permanent

```




## client @windows
reg add "HKCU\Environment" /f /t REG_SZ /v HTTP_PROXY /d "http://user:pwd@ip:9090"
reg add "HKCU\Environment" /f /t REG_SZ /v HTTPS_PROXY /d "http://user:pwd@ip:9090"
