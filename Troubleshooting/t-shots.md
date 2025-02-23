## CrashLoopbackoff

- Errors
  - Exit code 127: The application attempt to access a non-existent file
  - Error OOM: Pod is out-of-memory
  - Exit code 137: Error is outside of the container


## Pending Pods
- Node taint
  - Taint: workloadequalsmachinelearning:NoSchedule
    
  - edit your pod in deployment file to have:
    - spec.toleratiions:
      - key: workloads
        operator: Equal
        value: machine-learning

  - Unscheduled pod, due to high resource request: adjust the request (not the limit) to the available resources in the cluster or increase the resources in the cluster (such as nodes)


## Missing Pods
- Namespace quota
  - check for events in the namespace `kubectl get events -n xxx`
  - describe the namespace
  - `kubectl edit resourcequota -n <namespace> pod-quota` <get the name 'pod-quota' when you describe the namspace>
  - increase the pod quota or reduce the replicas of the deployments
  - then `kubectl rolout restart deployment -n xxx <deployment_name>`
 
- Using a serviceaccount that is not in your namespace in your deployment/pod


## Schrodinger deployment
- Watchout for selector-labels match/mismatch between different services


## Create container error


## When configmaps and secrets are altered, the corresponding pods are supposed to restart to reflect the change(s). Install a package like reloaderto automate this process
   - https://github.com/stakater/Reloader
   - Resource won't terminate after running `kubectl delete ...`
   - `--force`
   - alternatively, edit the resoure and remove finalizers (item(s) under finalize
  
  

## Argument list too long
   - Perhaps your namespace has lots of services linked together, making your env_variables and config_variables too long for 1 container
   - Fix: Deployment > spec.spec (just above containers) > add `enableServiceLinks: false`, it is true by default
     - `spec:
          enableServiceLinks: false
          containers:
          ...`
     - Recommendation: use dns plugins for resolving and accessing services
    
- Identifying leaks in RBAC

- Some Notes
  - ClusterRoleBindings cannot reference Roles.
  - Creating the RoleBinding in a namespace other than the subjects’ allows the subject to have roles in other namespaces.
  - RoleBindings connect ClusterRoles, but they only give access to the namespace defined in the binding.


## Leaky Network policies
- 
