### docker

```bash
mkdir -p /data/tomcat/
docker pull tomcat

wget -P /data/tomcat/  http://mirrors.jenkins.io/war-stable/latest/jenkins.war 

docker run -d -p 28080:8080 --restart=always -v /data/tomcat/:/usr/local/tomcat/webapps/ --name jenkins tomcat

```


