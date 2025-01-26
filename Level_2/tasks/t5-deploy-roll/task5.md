## Task

- Rolling Updates And Rolling Back Deployments in Kubernetes

Create a namespace xfusion. 

Create a deployment called httpd-deploy under this new namespace, 
It should have one container called httpd, use httpd:2.4.28 image and 3 replicas. 
The deployment should use RollingUpdate strategy with maxSurge=1, and 
maxUnavailable=2. 

Also create a NodePort type service named httpd-service and expose 
the deployment on nodePort: 30008.

Now upgrade the deployment to version httpd:2.4.43 using a rolling update.

Now, rollit back it back to the previous release.



## Solution

1. Apply and describe the deploy
```bash
kubectl apply -f httpd-deployment.yml
kubectl describe deploy -n xfusion httpd-deployment
```


2. Upgrade container version to 2.4.43

  - Use Rolling update setimage sub-command
```bash
kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 -n xfusion
```

OR

  - Go and edit the container manually
```bash
kubectl edit deploy -n xfusion httpd-deployment
```


3. Roll back to the previous version
```bash
kubectl rollout undo deploy -n xfusion httpd-deployment
```
  - Confirm the roll-back:
```bash
kubectl describe deploy -n xfusion httpd-deployment
```

4. Get logs



## Question
- What is difference/implication (if any) between editing deployments and using rolling setimage command? 