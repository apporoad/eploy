### install

```bash
sudo yum install ansible

# 建议采用 源码安装 官网

mkdir -p /data/
cd /data
git clone https://github.com/ansible/ansible.git --recursive

# install ansible
cd ansible
git checkout stable-2.7
source ./hacking/env-setup

#  install
yum -y install python-setuptools
#easy_install Jinja2

easy_install pip
pip install paramiko PyYAML Jinja2 httplib2 six
```

