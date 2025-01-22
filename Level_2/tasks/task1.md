
## Task

Volume name should be volume-share of type emptyDir.

After creating the pod, exec into the first container i.e volume-container-devops-1, and just for testing create a file news.txt with any content under the mounted path of first container i.e /tmp/news.

The file news.txt should be present under the mounted path /tmp/cluster on the second container volume-container-devops-2 as well, since they are using a shared volume.


Solution

1. Create the pod

```vi volume-share-devops```

```bash
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-devops
spec:
  volumes:
  - name: volume-share
    emptyDir: {}

  containers:

  - name: volume-container-devops-1
    image: ubuntu:latest
    volumeMounts:
    - name: volume-share
      mountPath: /tmp/news
    command: ["sleep", "infinity"]

  - name: volume-container-devops-2
    image: ubuntu:latest
    volumeMounts:
    - name: volume-share
      mountPath: /tmp/cluster
    command: ["sleep", "infinity"]
```

```bash
kubectl apply -f volume-share-devops.yaml
```
```bash
kubectl get pods
```


2. Access the first container
```bash
kubectl exec -it volume-share-devops -c volume-container-devops-1 -- /bin/bash
```

3. Create a file in the mount-path of the first container
```bash
cd /tmp/news
echo "This is the first news received by this volume" > news.txt
```

4. Confirm that the content is also in the mount-path of container 2 (since they both share the same volume)
```bash
kubectl exec -it volume-share-devops -c volume-container-devops-2 --/bin/bash
cat /tmp/cluster/news.txt
```


