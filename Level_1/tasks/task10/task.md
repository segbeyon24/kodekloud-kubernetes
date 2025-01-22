## Task

- Create a pod called time-check in the xfusion namespace. The pod should contain a container named time-check, utilizing the busybox image with the latest tag (specify as busybox:latest).

- Create a config map named time-config with the data TIME_FREQ=10 in the same namespace.

- Configure the time-check container to execute the command: while true; do date; sleep $TIME_FREQ;done. Ensure the result is written /opt/dba/time/time-check.log. Also, add an environmental variable TIME_FREQ in the container, fetching its value from the config map TIME_FREQ key.

- Create a volume log-volume and mount it at /opt/dba/time within the container.


## Solution

1. Create xfusion namespace, if it does not exist yet
   check: ```kubectl get namespaces```
   create: ```kubectl create ns xfusion```

3. run the yml file in this directory:
   ```kubectl apply -f config-time-check.yml```

4. Check the ConfigMap and Pod
   ```kubectl get configmap time-config -n xfusion```
   ```kubectl get pods -n xfusion```

5. Inspect the logs: Once the pod is running, you can exec into the 
   container and view the log file:
   ```kubectl exec -n xfusion time-check -- cat /opt/dba/time/time-check.log```
