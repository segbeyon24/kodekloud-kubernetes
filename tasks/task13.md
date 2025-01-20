NB: A ReplicationController (RC) is an older Kubernetes object used to ensure that a specified number of pod replicas are running at all times. However, ReplicaSets have largely replaced ReplicationControllers in modern Kubernetes because they provide more features and flexibility.

## Task
The Nautilus DevOps team is establishing a ReplicationController to deploy multiple pods for hosting applications that require a highly available infrastructure. Follow the specifications below to create 
the ReplicationController:

- Create a ReplicationController using the nginx image, preferably with latest tag, and name it nginx-replicationcontroller.

- Assign labels app as nginx_app, and type as front-end. Ensure the container is named nginx-container and set the replica count to 3.

- All pods should be running state post-deployment.

- Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.


## Solution

```bash
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-replicationcontroller
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 3
  selector:
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
- Run 
  ```kubectl apply -f <nginx-replicationcontroller>```

- Check
  ```kubectl get all```




## In Production
Replicasets are deployed using deployments, an example in this context would be:

```bash
apiVersion: v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx_app
    type: frontend
spec:
  replicas: 3
    selector:
      matchLabels:
        app: nginx_app
  templates:
    metadata:
      labels:
        app: nginx_app
        type: frontend  
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80

```
Scaling the deployment <mydeploy.yml>:
 1. Manually:
  ```kubectl scale deployment <mydeploy.yml> --replicas=<5>```
  For 5 pods. To edit it, get the deployment and edit the replicas
  ```kubectl get deployment <mydeploy> -o yaml > nginx-deployment.yaml```
     `spec:
        replicas: 5
        `
  ```kubectl apply -f <mydeploy.yml>

 2. Automatic Scaling with Horizontal Pod Autoscaler (HPA)
    - Ensure the Kubernetes Metrics Server is installed and running in your cluster. 
      You can install it using the following guide:
      https://github.com/kubernetes-sigs/metrics-server

    - this a sample:
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        resources:
          requests:
            cpu: "200m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "256Mi"
        ports:
        - containerPort: 80
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-deployment-hpa
  labels:
    app: nginx
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

 - Testing the Autoscaling:
   - Simulate High Load: Connect to a pod and generate CPU load (e.g.using `stress` or a busy loop in a script)
    ```bash
      kubectl exec -it <pod-name> -- /bin/sh
	  while true; do :; done```

   - Monitor HPA Behavior: Watch the HPA and pod scaling in action:
     ```kubectl get hpa nginx-deployment-hpa -w```
     
     Check the number of pods:
     ```kubectl get pods -l app=nginx```

     Reduce Load: Stop the load generation and observe the HPA reducing the number of replicas





## issues

- Error

    `kubectl apply -f nginx-replicationcontroller.yml`
    The ReplicationController "nginx-replicationcontroller" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"nginx_app"}: `selector` does not match template `labels`

 - Solution:
   - Ensure the selector and spec.template.metadata.labels are the same
   - Add the type metadata.labels.type: frontend
