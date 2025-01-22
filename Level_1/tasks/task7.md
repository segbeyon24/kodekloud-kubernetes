
## Deploy Replica Set in Kubernetes Cluster

## Solution
- Create nginx-replicaset.yml file:

```bash
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx_app
      type: front-end
  template:
    metadata:
      labels:
        app: nginx_app
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
```


```kubectl apply -f nginx-replicaset.yaml```

```kubectl get rs```

```kubectl get pods --selector app=nginx_app,type=front-end```


