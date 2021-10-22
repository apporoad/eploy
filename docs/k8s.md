## kustomize 方式
查看 k8s/kustomize 例子



## 使用命令

### 启动base
```bash
cd k8s/kustomize

cd base

kubectl apply -k .

```

### 集成ingress
```bash

cd k8s/kustomize

kubectl apply -f ingress/demo1-ingress.yaml

```

### 常用命令
```bash
kubectl get all
kubectl get pods -A


kubectl run busybox --image=busybox --restart=Never -i --tty

kubectl apply -k xxxx

kubectl apply -k xxxx --dry-run

kubectl kustomize xxxx

```

### 常用yaml
#### configmap
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: xxx-api-configmap
data:
  host: "xxx-api"
  author: "xiaolu"
```

#### deploy
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: xxx-api-deploy
  labels:
    app: xxx-api
    version: v1
    namespace: default
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: xxx-api
  template:
    metadata:
      labels:
        app: xxx-api
    spec:
      containers:
        - name: xxx-api
          image: xxxxxxx:8081/xxx:0.0.1-SNAPSHOT
          ports:
            - containerPort: 8888
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /xxx/healthy
              port: 8888
            initialDelaySeconds: 30
            timeoutSeconds: 30
          imagePullPolicy: Always
          envFrom:
            - configMapRef:
                name: xxx-api-configmap
      imagePullSecrets:
        - name: yourSecrets
      tolerations:
        - key: node-role.kubernetes.io/master
          effect: NoSchedule
      volumes:
        - name: conf
          configMap:
            name: xxx-api-configmap
```


#### service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: xxx-api
  namespace: default
  labels:
    app: xxx-api
    version: v1
spec:
  ports:
    - port: 8888
      targetPort: 8888
  selector:
    app: xxx-api
  type: ClusterIP
```

#### kustomization
```yaml
namespace: fat
commonLabels:
  variant: fat
  org: xxx
commonAnnotations:
  note: Hello, here is fat!
bases:
  - ../../../base
patchesStrategicMerge:
  - configMap.yaml
  - deployment.yaml
  - service.yaml
```