## Task [Deploy Drupal App on Kubernetes]

We need to deploy a Drupal application on Kubernetes cluster. The Nautilus application development team want to setup a fresh 
Drupal as they will do the installation on their own. Below you can find the requirements, they have shared with us.

1) Configure a persistent volume drupal-mysql-pv with hostPath = /drupal-mysql-data (/drupal-mysql-data directory already exists on the worker 
Node i.e jump host), 5Gi of storage and ReadWriteOnce access mode.

2) Configure one PersistentVolumeClaim named drupal-mysql-pvc with storage request of 3Gi and ReadWriteOnce access mode.

3) Create a deployment drupal-mysql with 1 replica, use mysql:5.7 image. Mount the claimed PVC at /var/lib/mysql.

4) Create a deployment drupal with 1 replica and use drupal:8.6 image.

4) Create a NodePort type service which should be named as drupal-service and nodePort should be 30095.

5) Create a service drupal-mysql-service to expose mysql deployment on port 3306.

6) Set rest of the configration for deployments, services, secrets etc as per your preferences. At the end you should be able to access the 
Drupal installation page by clicking on App button.



## Solution

1. 

2. Create the resources

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: drupal-mysql-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/drupal-mysql-data"
  persistentVolumeReclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: drupal-mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drupal-mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal-mysql
  template:
    metadata:
      labels:
        app: drupal-mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "rootpass"
            - name: MYSQL_DATABASE
              value: "drupal"
            - name: MYSQL_USER
              value: "drupaluser"
            - name: MYSQL_PASSWORD
              value: "drupalpass"
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: drupal-mysql-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: drupal-mysql-service
spec:
  selector:
    app: drupal-mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drupal
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal
  template:
    metadata:
      labels:
        app: drupal
    spec:
      containers:
        - name: drupal
          image: drupal:8.6
          env:
            - name: DRUPAL_DB_HOST
              value: "drupal-mysql-service"
            - name: DRUPAL_DB_USER
              value: "drupaluser"
            - name: DRUPAL_DB_PASSWORD
              value: "drupalpass"
            - name: DRUPAL_DB_NAME
              value: "drupal"
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: drupal-service
spec:
  type: NodePort
  selector:
    app: drupal
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30095

```


3. apply and get


## Production Environment deployment of same app:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: drupal-mysql-secret
  labels:
    app: drupal-mysql
stringData:
  MYSQL_ROOT_PASSWORD: "rootpass"
  MYSQL_DATABASE: "drupal"
  MYSQL_USER: "drupaluser"
  MYSQL_PASSWORD: "drupalpass"
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: drupal-mysql-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: gp2  # Use appropriate storage class for AWS/GCP
  persistentVolumeSource:
    awsElasticBlockStore:  # For AWS EBS
      volumeID: <your-ebs-volume-id>
      fsType: ext4
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: drupal-mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
  storageClassName: gp2  # Match PV storage class
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: drupal-mysql
spec:
  serviceName: "drupal-mysql"
  replicas: 1
  selector:
    matchLabels:
      app: drupal-mysql
  template:
    metadata:
      labels:
        app: drupal-mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_DATABASE
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_USER
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_PASSWORD
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: drupal-mysql-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: drupal-mysql-service
spec:
  selector:
    app: drupal-mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: drupal-mysql-policy
spec:
  podSelector:
    matchLabels:
      app: drupal-mysql
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: drupal
      ports:
        - protocol: TCP
          port: 3306
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: drupal
spec:
  replicas: 1
  selector:
    matchLabels:
      app: drupal
  template:
    metadata:
      labels:
        app: drupal
    spec:
      containers:
        - name: drupal
          image: drupal:8.6
          env:
            - name: DRUPAL_DB_HOST
              value: "drupal-mysql-service"
            - name: DRUPAL_DB_USER
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_USER
            - name: DRUPAL_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_PASSWORD
            - name: DRUPAL_DB_NAME
              valueFrom:
                secretKeyRef:
                  name: drupal-mysql-secret
                  key: MYSQL_DATABASE
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: drupal-service
spec:
  type: NodePort
  selector:
    app: drupal
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30095
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: drupal-ingress
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
    - hosts:
        - drupal.example.com
      secretName: drupal-tls
  rules:
    - host: drupal.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: drupal-service
                port:
                  number: 80
```

- Use Read Replicas for MySQL (converted Deployment to StatefulSet).

- Enable RBAC via restricted access.

- Apply NetworkPolicies to restrict traffic.

- Enable TLS encryption via Let's Encrypt Cert-Manager.

- Set up an Ingress with HTTPS (drupal.example.com).

