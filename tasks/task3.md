## Task
The Nautilus DevOps team is planning to deploy some micro services on 
Kubernetes platform. The team has already set up a Kubernetes cluster 
and now they want to set up some namespaces, deployments etc. Based on 
the current requirements, the team has shared some details as below:

Create a namespace named dev and deploy a POD within it. Name the pod 
dev-nginx-pod and use the nginx image with the latest tag. Ensure to 
specify the tag as nginx:latest.


## Solution

1. create namespace: "kubectl create namespace dev"

```vi dev-nginx-pod.yml```


```bash
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod 
  namespace: dev
  labels:
    app: dev-nginx-pod
spec:  
  containers:
  - name: dev-nginx-pod   
    image: nginx:latest
    ports:  
    - containerPort: 80
```

```kubectl apply -f dev-nginx-pod```

```kubectl get pods -n dev```
