# Dry Run

> kubectl kustomize multilevel/

OR

> kustomize build multilevel/


<details>
  <summary>YAML Output</summary>

```yaml
apiVersion: v1
kind: Namespace
metadata:
  labels:
    app: mysql
    tool: kustomize
  name: mysql-demo
---
apiVersion: v1
kind: Namespace
metadata:
  labels:
    app: wordpress
    tool: kustomize
  name: wordpress-demo
---
apiVersion: v1
data:
  endpoint: api.example.com/users
kind: ConfigMap
metadata:
  labels:
    base: multilevel
    tool: kustomize
  name: poc-endpoint-configmap-demo
---
apiVersion: v1
data:
  password: YWRtaW4=
kind: Secret
metadata:
  labels:
    app: mysql
    tool: kustomize
  name: poc-database-creds-demo
  namespace: mysql-demo
type: Opaque
---
apiVersion: v1
data:
  username: YWRtaW4=
kind: Secret
metadata:
  labels:
    base: multilevel
    tool: kustomize
  name: poc-common-creds-demo
type: Opaque
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: mysql
    tool: kustomize
  name: poc-mysql-demo
  namespace: mysql-demo
spec:
  ports:
  - port: 3306
  selector:
    app: mysql
    tool: kustomize
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: wordpress
    tool: kustomize
  name: poc-wordpress-demo
  namespace: wordpress-demo
spec:
  ports:
  - port: 80
  selector:
    app: wordpress
    tool: kustomize
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: mysql
    tool: kustomize
  name: poc-mysql-demo
  namespace: mysql-demo
spec:
  selector:
    matchLabels:
      app: mysql
      tool: kustomize
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
        tool: kustomize
    spec:
      containers:
      - env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              key: password
              name: mysql-pass
        image: mysql:5.6
        name: mysql
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - mountPath: /var/lib/mysql
          name: mysql-persistent-storage
      volumes:
      - emptyDir: {}
        name: mysql-persistent-storage
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wordpress
    tool: kustomize
  name: poc-wordpress-demo
  namespace: wordpress-demo
spec:
  selector:
    matchLabels:
      app: wordpress
      tool: kustomize
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tool: kustomize
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        ports:
        - containerPort: 80
          name: wordpress
        volumeMounts:
        - mountPath: /var/www/html
          name: wordpress-persistent-storage
      volumes:
      - emptyDir: {}
        name: wordpress-persistent-storage
```
</details>

# Apply

> kubectl kustomize multilevel | kubectl apply -f

OR

> kustomize build multilevel | kubectl apply -f
