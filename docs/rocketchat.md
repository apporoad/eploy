## install by helm

[link](https://github.com/helm/charts/tree/master/stable/rocketchat)

```bash
# create pv   4个
mkdir /home/pv1
mkdir /home/pv2
mkdir /home/pv3
mkdir /home/pv4

chmod 777 -R /home

vim /data/pv1.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: hostpath1
  labels:
    type: local
spec:
  capacity:
    storage: 8Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/home/pv1"

```





```bash
kubectl apply -f /data/pv.yaml
kubectl apply -f /data/pv2.yaml

# ps   helm 需要使用 微软源，或者google源

#svn co https://github.com/helm/charts/trunk/stable/rocketchat

#helm install --name rocketchat stable/rocketchat
helm install --name rocketchat  stable/rocketchat --set mongodb.mongodbPassword=$(echo -n $(openssl rand -base64 32)),mongodb.mongodbRootPassword=$(echo -n $(openssl rand -base64 32))


kubectl get pvc

kubectl get pods
```

