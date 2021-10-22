## env
centos7
docker

## install

```bash
docker pull kylemanna/openvpn

OVPN_DATA="/root/ovpn-data"

#机器的外网ip地址
IP="xxx.xxx.xxx.xxx" 


mkdir ${OVPN_DATA}

# config
docker run -v ${OVPN_DATA}:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u tcp://${IP}
# init 
docker run -v ${OVPN_DATA}:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki

# Enter PEM pass phrase: 输入密码 123456（你是看不见的）
# Verifying - Enter PEM pass phrase: 输入 123456（你是看不见的）
# Common Name (eg: your user, host, or server name) [Easy-RSA CA]:输入名字或直接回车
# Enter pass phrase for /etc/openvpn/pki/private/ca.key:输入 123456

# add user    no pwd
docker run -v ${OVPN_DATA}:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full CLIENTNAME nopass
# add user  with pwd
docker run -v ${OVPN_DATA}:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full CLIENTNAME


# run server

docker run --name openvpn -v ${OVPN_DATA}:/etc/openvpn -d -p 1194:1194 --privileged kylemanna/openvpn

```

## how to add user
```bash
docker run -v ${OVPN_DATA}:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full CLIENTNAME

docker run -v ${OVPN_DATA}:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient CLIENTNAME > ${OVPN_DATA}/CLIENTNAME.ovpn

```
## how to delete user
```bash
docker run -v $OVPN_DATA:/etc/openvpn --rm -it kylemanna/openvpn easyrsa revoke tencent

# enable revoke when restart vpn
docker restart openvpn
```

## how to get visit log
```bash
docker logs openvpn |grep vpn
```