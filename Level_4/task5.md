## Solution

```yaml
## BACKEND
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-devops
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
          - containerPort: 6379

---
# backend-service
apiVersion: v1
kind: Service
metadata:
  name: redis-master
spec:
  selector:
    app: redis-master
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379


---
# redis-slave deployment

apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-devops
        image: gcr.io/google_samples/gb-redisslave:v3
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
          - containerPort: 6379
        env:
          - name: GET_HOSTS_FROM
            value: dns

---
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
spec:
  selector:
    app: redis-slave
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379

      
---
## FRONTEND

apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-devops
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
          - containerPort: 80
        env:
          - name: GET_HOSTS_FROM
            value: dns

---
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30009
  type: NodePort
```
