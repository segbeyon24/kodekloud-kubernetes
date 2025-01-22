## Task 1
We've successfully deployed a pod named httpd-app-t1q3. We require some data to be copied from the jump_host to this specific Pod. Further details are outlined below:


Copy file /home/thor/welcome.txt to pod httpd-app-t1q3 Pod under location /opt.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

Solution
```bash
kubectl cp welcome.txt httpd-app-t1q3:/opt/welcome.txt
```




## task 2

The Nautilus DevOps team is actively engaged in practicing pods and services deployment on the Kubernetes platform, preparing for the migration of numerous applications to this environment. Recently, a team member has been assigned the task of creating a pod with specific requirements:


a. Create a pod named pod-nginx-t1q1 utilizing the nginx image, ensuring the use of the latest tag, denoted as nginx:latest (remember to mention tag name while defining the image).

b. Set the app label to nginx_app_t1q1, and name the container as nginx-container-t1q1.

This task aims to establish a new pod adhering to defined image specifications and container naming conventions.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

- Solution

```bash
apiVersion: v1
kind: Pod
metadata:
  name: pod-nginx-t1q1
  labels:
    app: nginx_app_t1q1
spec:
  containers:
  - name: nginx-container-t1q1
    image: nginx:latest
```
  - apply and get




## Task 3

The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform, as they are planning to migrate most of their applications on Kubernetes. Recently one of the team members has been assigned a task to create a deployment as per details mentioned below:


Create a deployment named nginx-t2q1 to deploy the application nginx-t2q1 using the image nginx:latest (remember to mention the tag as well)

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

Deployment 'nginx-t2q1' exists

Deployment 'nginx-t2q1' is using 'nginx' image



- Solution

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-t2q1
spec:
  replicas: 1 
  selector:
    matchLabels:
      app: nginx-t2q1
  template:
    metadata:
      labels:
        app: nginx-t2q1
    spec:
      containers:
      - name: nginx 
        image: nginx:latest
```

  - apply and get



## Task 4

The Nautilus devops team found that one of the applications that is deployed on the cluster is having some performance issues, they want to make some changes so that it can handle some more traffic. As per new updates some new changes need to be made in this existing setup. So update the deployment as per details mentioned below:


The deployment name is blue-app-t2q5, change its replicas count from 1 to 3.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

Deployment 'blue-app-t2q5' exists

Pods are running

Deployment 'blue-app-t2q5' replicas count is '3'



## Task 5

The Nautilus DevOps team is actively creating jobs within the Kubernetes cluster. While they are in the process of finalizing actual scripts/commands, they are presently structuring templates and testing the jobs using placeholder commands. Below are the specifications for creating a job template:


Create a job named countdown-nautilus-t3q2.

The spec template should be named as countdown-nautilus-t3q2 (under metadata), and the container should be named as container-countdown-nautilus-t3q2.

Use image fedora with latest tag only and remember to mention tag i.e fedora:latest, and restart policy should be Never.

Use command sleep 5.



- Solution

```bash
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-nautilus-t3q2
spec:
  template:
    metadata:
      name: countdown-nautilus-t3q2
    spec:
      restartPolicy: Never
      containers:
      - name: container-countdown-nautilus-t3q2
        image: fedora:latest
        command: ["sleep", "5"]
```

  - apply and get



## Task 6

The Nautilus DevOps team is in the process of developing scripts to be executed on various schedules. Currently, they are provisioning cron jobs within the Kubernetes cluster with placeholder commands (to be substituted with actual scripts). Below are the specifications for creating a cronjob:


a. Create a cronjob named nautilus-t3q1.

b. Set Its schedule to something like */5 * * * *, you set any schedule for now.

c. Container name should be cron-nautilus-t3q1.

d. Use nginx image with latest tag only and remember to mention the tag i.e nginx:latest.

e. Run a dummy command echo Welcome to xfusioncorp!.

f. Ensure restart policy is OnFailure.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

Cronjob 'nautilus-t3q1' exists

Image used is 'nginx:latest'

Container name is 'cron-nautilus-t3q1'

Restart policy is set as 'OnFailure'


- Solution

```bash
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nautilus-t3q1
spec:
  schedule: "*/5 * * * *" 
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
          - name: cron-nautilus-t3q1
            image: nginx:latest
            command: ["sh", "-c", "echo Welcome to xfusioncorp!"]
```

  - apply and get



## Task 7

The Nautilus DevOps team successfully deployed a Redis application on the Kubernetes cluster last week. However, this morning, modifications were being made to the existing setup by one of our team members. Unfortunately, errors occurred during these changes, causing the application to go offline.


The deployment named redis-deployment-t4q2 is currently experiencing issues, resulting in the pods being unavailable. Your task is to troubleshoot this issue and restore the deployment to ensure the pods are in a running state again.

Please proceed with diagnosing the problem and implementing the necessary fixes to bring the redis-deployment-t4q2 back to a functional state.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

Ensure:
'redis-deployment-t4q2' deployment exists

Pods are in running state

Redis is responding fine



- Solution

  - fix typo errors in image name and configmap





## Task 8

Last week, our team successfully deployed a Nginx and PHP-FPM based setup on the Kubernetes cluster, functioning seamlessly. However, this morning, a team member made changes that resulted in disruptions, rendering the setup inoperable.


The pod named nginx-phpfpm-t4q3 and the associated config map named nginx-config-t4q3 are experiencing issues following recent modifications. Your task is to diagnose the issue and restore functionality to the setup.

Once the issue is resolved, copy the /home/thor/index.php file from the jump-host to the nginx-container, placing it within the document root /var/www/html. Subsequently, ensure the website becomes accessible via the Website button located in the top bar.

Please proceed with troubleshooting the identified issues and implementing the necessary fixes. Afterward, complete the file transfer and verification of website accessibility. Also, feel free to re-create the pod if needed.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

Ensure:
ConfigMap 'nginx-config-t4q3' exists
Pod 'nginx-phpfpm-t4q3' exists
Pods are running
Website is up and accessible


- Solution



## Task 9 

Last week, our team successfully deployed a Nginx and PHP-FPM based setup on the Kubernetes cluster, functioning seamlessly. However, this morning, a team member made changes that resulted in disruptions, rendering the setup inoperable.


The pod named nginx-phpfpm-t4q3 and the associated config map named nginx-config-t4q3 are experiencing issues following recent modifications. Your task is to diagnose the issue and restore functionality to the setup.

Once the issue is resolved, copy the /home/thor/index.php file from the jump-host to the nginx-container, placing it within the document root /var/www/html. Subsequently, ensure the website becomes accessible via the Website button located in the top bar.

Please proceed with troubleshooting the identified issues and implementing the necessary fixes. Afterward, complete the file transfer and verification of website accessibility. Also, feel free to re-create the pod if needed.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

ConfigMap 'nginx-config-t4q3' exists

Pod 'nginx-phpfpm-t4q3' exists

Pods are running

Website is up and accessible



## Task 10

During an investigating it was found that one of the applications on the Kubernetes cluster is having some issues, the team discovered that the service was configured with an incorrect target port. We need to update the service as follows:


Update service-t5q4 service to use target port 80.

Service 'service-t5q4' targetPort is '80'

Website is up and accessible