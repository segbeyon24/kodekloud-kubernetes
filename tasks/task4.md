## Task
Set Resources limits on Kubernetes Pod


 - You can configure resource quotas to limit the total amount of 
   resources that a namespace can consume. Kubernetes enforces quotas 
   for objects in particular namespace when there is a ResourceQuota in 
   that namespace.

 - For example, if you assign specific namespaces to different teams, 
   you can add ResourceQuotas into those namespaces. Setting resource 
   quotas helps to prevent one team from using so much of any resource 
   that this over-use affects other teams.

 - You should also consider what access you grant to that namespace: 
   "full" write access to a namespace allows someone with that access 
   to remove any resource, including a configured ResourceQuota.

 - Your container might get terminated because it is resource-starved. 
   Check whether a container is being killed because it is hitting a 
   resource limit, call "kubectl describe pod [name]" on the Pod of 
   interest


## Task
Limit the compute resource of the pod used by the dev team


## Solution

1. Create pod with and limit resource usage
```bash
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-pod
    image: httpd:latest
    resources:
      limits:
        cpu: "100m"
        memory: "20Mi"
      requests:
        cpu: "100m"
        memory: "15Mi"
```

2. ```kubectl describe nodes <node_name>```
This returns lots of information on the, including Pods are taking 
up space on the node.

3. If a Pod requests more than its allocared memory, that Pod will not 
fit on the node.
By looking at the “Pods” section (in the output of (2) above, 
you would see which Pods are taking up space on the node.

4. The amount of resources available to Pods is less than the node 
capacity because system daemons use a portion of the available 
resources. Within the Kubernetes API, each Node has a 
.status.allocatable field (see NodeStatus for details).


5. The .status.allocatable field describes the amount of resources 
that are available to Pods on that node (for example: 15 virtual CPUs 
and 7538 MiB of memory). For more information on node allocatable 
resources in Kubernetes, see 
Reserve Compute Resources for System Daemons: 
`https://kubernetes.io/docs/tasks/administer-cluster/reserve-compute-resources/`


- Rmember to review code 3 times before submission.



## References
- https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
