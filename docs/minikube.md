## install on centos7
1. install docker fisrt
2. add user
```bash
useradd op
passwd op
newgrp docker
chmod 666 /var/run/docker.sock
usermod -aG docker op
```
3. install
```bash
yum -y update
yum -y install epel-release
yum -y install libvirt qemu-kvm virt-install virt-top libguestfs-tools bridge-utils
systemctl start libvirtd
systemctl enable libvirtd
systemctl status libvirtd

sudo usermod -a -G libvirt $(whoami)
sudo usermod -a -G libvirt op

sudo vi /etc/libvirt/libvirtd.conf

```

```conf
unix_sock_group = "libvirt"
unix_sock_rw_perms = "0770"
```

```bash
systemctl restart libvirtd.service
```
### install kube
```bash
wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube

minikube version

```
### install kubectl
```bash

curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl  /usr/local/bin/
kubectl version --client -o json
```

### start
```bash

su - op

minikube start
```