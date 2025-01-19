## Task
Create a pod for the dev-team to test a feature


## Solution

Create pod-httpd.yml file

  ```vi pod-httpd.yml```
  
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: pod-httpd
  spec:
    containers:
    - name: pod-httpd
      image: httpd:latest
      ports:
      - containerPort: 80
  ```

- Run the pod
    ```kubectl apply -f pod-httpd.yml```


- Check the pod:
  ```kubectl get pod pod-httpd.yml```
