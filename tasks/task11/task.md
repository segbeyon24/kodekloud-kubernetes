## Task
- A junior DevOps team member encountered difficulties deploying a stack on the Kubernetes cluster. The pod fails to start, presenting errors. Let's troubleshoot and rectify the issue promptly.

- There is a pod named webserver, and the container within it is named httpd-container, its utilizing the httpd:latest image.

- Additionally, there's a sidecar container named sidecar-container using the ubuntu:latest image.

- Identify and address the issue to ensure the pod is in the running state and the application is accessible.



## Solution

```kubectl get pods```
   - the output included `1/2` and `ImagePullOff`

 ```kubectl edit pod webserver```
   - `container.image: httpd:latests` was not running
   - `container.image: side-car` was running

I edited the `latests` tag to `latest`
   - the pod was showed `2/2` and `Running`
   - I verified with 
     ```kubectl edit pod webserver```
   - The 2 images images were running