## Task

The Nautilus DevOps team is setting up recurring tasks on different 
schedules. Currently, they're developing scripts to be executed 
periodically. To kickstart the process, they're creating cron jobs in 
the Kubernetes cluster with placeholder commands.


## Solution

`vi nautilus.yaml`

```bash
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nautilus
spec:
  schedule: "*/11 * * * *" # Runs every 11 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-nautilus
            image: httpd:latest
            command:
            - /bin/sh
            - -c
            - echo "Welcome to xfusioncorp!"
          restartPolicy: OnFailure
```


```kubectl apply -f nautilus.yaml```

```kubectl get cronjob nautilus```

```kubectl get jobs```


