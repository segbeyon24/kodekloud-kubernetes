## Task [Deploy My SQL on Kubernetes]

A new MySQL server needs to be deployed on Kubernetes cluster. The Nautilus DevOps team was working on to gather the requirements. 
Recently they were able to finalize the requirements and shared them with the team members to start working on it. Below you can find the details:

1.) Create a PersistentVolume mysql-pv, its capacity should be 250Mi, set other parameters as per your preference.

2.) Create a PersistentVolumeClaim to request this PersistentVolume storage. Name it as mysql-pv-claim and request a 250Mi of storage. 
Set other parameters as per your preference.

3.) Create a deployment named mysql-deployment, use any mysql image as per your preference. Mount the PersistentVolume at mount path /var/lib/mysql.

4.) Create a NodePort type service named mysql and set nodePort to 30007.

5.) Create a secret named mysql-root-pass having a key pair value, where key is password and its value is YUIidhb667, 
create another secret named mysql-user-pass having some key pair values, where frist key is username and its value is kodekloud_top, 
second key is password and value is 8FmzjvFU6S, create one more secret named mysql-db-url, key name is database and value is kodekloud_db1

6.) Define some Environment variables within the container:

a) name: MYSQL_ROOT_PASSWORD, should pick value from secretKeyRef name: mysql-root-pass and key: password

b) name: MYSQL_DATABASE, should pick value from secretKeyRef name: mysql-db-url and key: database

c) name: MYSQL_USER, should pick value from secretKeyRef name: mysql-user-pass key key: username

d) name: MYSQL_PASSWORD, should pick value from secretKeyRef name: mysql-user-pass and key: password



## Solution

```yaml
# persistentVolume
apiVersion: v1
kind: persistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  persistentVolumeClaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: "/mnt/data/mysql"

---
# persistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metada:
  name: mysql-pv-claim
spec:
  accessModes: 
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
  storageClassName: manual

---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: myaql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sql-app
  template:
    metadata:
      labels:
        app: mysql-app
    volumes:
      - name: mysql-volume
        persistentVolumeClaim:
          claimName: mysql-pv-claim
    containers:
    - name: mysql-container
      image: mysql:8.0
      volumeMounts:
        - name: mysql-volume
          mountPath: var/lib/mysql
      ports:
        - containerPort: 3306
      env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
            name: mysql-root-pass
            key: password

        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database

        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username

        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password

---
# Service
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql-app
  tyoe: NodePort
  ports:
    protocol: TCP
    port: 3306
    targetPort: 3306
    nodePort: 30007

---
# Secrets
apiVersion: v1
kind: Secret
metadata:
  name: mysql-root-pass
type: Opaque
data:
  password: WVVJaWRoYjY2Nw==  # Base64 encoded value of "YUIidhb667"
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-user-pass
type: Opaque
data:
  username: a29kZWtsb3VkX3RvcA==  # Base64 encoded value of "kodekloud_top"
  password: OEZtenpqdkZVNlM=  # Base64 encoded value of "8FmzjvFU6S"
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-url
type: Opaque
data:
  database: a29kZWtsb3VkX2RiMQ==  # Base64 encoded value of "kodekloud_db1"
```

- apply and get


