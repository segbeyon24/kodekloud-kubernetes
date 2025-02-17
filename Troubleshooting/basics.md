

1. auth can-i

   - This command is useful when there are lots of serviceaccounts, roles and rolebindings - to know who and what can perform certain commands
   - RBAC denies permisssions by default, except when an access is allowed.

   - `kubectl auth can-i list pods -n monitoring`
   - `kubectl auth can-i get pods --as*jane --v=10` 
   - `kubectl auth can-i delete pods -n xxx --as*jane --v=5`

   -`kubectl auth can-i delete pods --as*system:serviceaccount:default:default --v=5`

   - `kubectl auth whoami`
   - `kubectl get roles`
   - `kubectl get role pod-reader -o yaml`
   - `kubectl get rolebindings`
   - `kubectl get rolebinding read-pods -o yaml`


2. Kubectl top- it returns the resourcce usage of resources (pods, nodes). A metrics server must be available in the cluster for it to get the data from its api
   - `kubectl top pods`
   - `kubectl top nodes`

   - `k diff -f redis.yaml` o see changes made / differences between initial and current state of a resource


3. Kubectl debug (attaching a containing the subject pod for debugging) - why use it
   - minimize pod disruption (especially production env)
   - pods using distroless images (efficiant images with little buffer packages). They may not have a shell to even exec into
   - crashed container (you can't exec into a crashed container)

   - Commands
     - `kubectl debug <pod_name> -it --image=nusybox (debugging_container) --target-nginx`
        - `kubectl describe <the_pod_name>` to see the activities
     - ps aux
     - ls
     - cd proc
     - cd 1 <id_of_a_process>
     - cd root
     - cd /etc/nginx/nginx.conf # accessing the configuration file of the running (target) container

     - kubectl debug nginx-pod --image=busybox -it --copy-to=debugging-pod --share-processes
       - why copy a running container to a debugging container in kubernetes?

