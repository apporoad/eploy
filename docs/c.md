#### k8s

[kubspray](https://github.com/hyd-raiders/kubespray.git)   [minikube on dev](https://kubernetes.io/docs/tasks/tools/install-minikube/)

[helm](https://helm.sh/)   [how to install](https://github.com/hyd-raiders/Cx/blob/master/docs/c2.runBasic.md#%E7%B2%BE%E8%8B%B1%E7%8B%BC%E4%BA%BA-%E6%B5%B7%E5%B0%94%E5%A7%86helm)

[harbo](https://github.com/hyd-raiders/Cx/blob/master/docs/c1.buildFirst.md#boss-哈珀-habor-企业级镜像管理)

[rook](https://github.com/rook/rook)   [quick start](https://rook.github.io/docs/rook/v1.1/ceph-quickstart.html)  [helm](https://rook.github.io/docs/rook/v1.1/helm-operator.html)

```bash

cd cluster/examples/kubernetes/ceph
kubectl create -f common.yaml
kubectl create -f operator.yaml

kubectl -n rook-ceph get pod


kubectl create -f cluster-test.yaml

# create storageclass
kubectl create -f csi/rbd/storageclass.yaml

kubectl get storageclass

# test pvc
kubectl create -f ../mysql.yaml

kubectl get pv 
kubectl get pvc
```

[kuboard](https://kuboard.cn/install/install-dashboard.html#%E5%AE%89%E8%A3%85) 界面

[ingress](https://kubernetes.github.io/ingress-nginx/deploy/#using-helm)

```bash
helm repo add stable https://kubernetes-charts.storage.googleapis.com


helm install stable/nginx-ingress \
-n nginx-ingress \
--namespace kube-system  \
--set controller.hostNetwork=true，rbac.create=true \
--set controller.replicaCount=1

# 出错， 把 rbac 删掉，重新执行


kubectl get svc -n kube-system 

# test
kubectl apply -f https://raw.githubusercontent.com/apporoad/aok.js/master/k8s/example/aok.yaml
kubectl apply -f https://raw.githubusercontent.com/apporoad/aok.js/master/k8s/example/ingress-aok.yaml

kubectl get pods | grep aok

# add aok.com into your client's /etc/hosts
# visit like http://aok.com:32406/

# delete test
kubectl delete -f https://raw.githubusercontent.com/apporoad/aok.js/master/k8s/example/aok.yaml
kubectl delete -f https://raw.githubusercontent.com/apporoad/aok.js/master/k8s/example/ingress-aok.yaml

```



[chartMuesum](https://github.com/helm/chartmuseum#helm-chart)  

```bash
kubectl get storageClass

cat > custom.yaml << EOF
env:
  open:
    STORAGE: local
persistence:
  enabled: true
  accessMode: ReadWriteOnce
  size: 1Gi
  storageClass: "rook-ceph-block"
EOF


helm install --name my-chartmuseum -f custom.yaml stable/chartmuseum --set ingress.enabled=true,ingress.hosts[0].name=chartmuseum.domain.com,ingress.annotations."kubernetes\.io/ingress\.class"=nginx


```



[monocular](https://github.com/helm/monocular)

todos



