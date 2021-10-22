```bash

find ./ -name  "test"  -exec sed  -i 's#somehost:[^\"]*#somehost:192\.168\.12\.23333#g' {} \;

```


## 截取版本号
```bash
echo "./xxxx-0.0.1-SNAPSHOT.jar" | sed '1!d' | sed -e 's/-/ /g' | awk '{print $2}'
```