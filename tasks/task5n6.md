
## Task 5
Execute Rolling Updates in Kubernetes

Solution
 - find details of the deployment to be updated
   - "kubectl get deploy"

 - Update the container image, to the updated version
   - "kubectl edit deploy [name]"

 - Check to see if the pods are running fine
   - "kubectl get pods" and/or "kubectl describe deploy [name]"


## Task 6
Revert Deployment to Previous Version in Kubernetes

Solution
 - get information on the deployments and previous deployments
   - "kubectl get deploy"
   - "kubectl rollout history deployment [name]"

 - Revert to the previous deployment
   - "kubectl rollout undo deploy [name]" 
   - "kubectl describe deployment [name]" 
   - the container.image should have reverted to the previous
      version 

 - NB: To Revert back to a specific deployment:
       - "kubectl rollout history deployment [name]"
       -  record the the number of the deployment to be reverted to
       - "kubectl rollout undo deployment [name] --to-revision=[rev.no]"



## Overview of Rolling Updates in Kubernetes

A rolling update is a deployment strategy where updates to an 
application or system are applied incrementally, one piece at a time, 
without taking the entire system offline.
This approach minimizes downtime and ensures continuity of service, 
which is crucial for applications requiring high availability.

- How It Works:

  1. Prepare the Update:
     - New code, configurations, or features are developed and packaged.


  2. Begin Update on a Subset:
     - The update is applied to a small subset of instances or servers 
       in the system (e.g., 1 or 2 out of 10).

  3. Monitor Performance:
     - Each updated instance is monitored for stability, errors, or regressions.


  4. Gradual Expansion:
     - Once the updated subset is confirmed stable, the process is repeated for additional subsets until all instances are updated.


  5. Completion:
     - The update is fully deployed across the entire system.


- Key Advantages:
  - Minimized Downtime: Services remain available throughout the update.

  - Risk Reduction: Errors or issues can be detected early, limiting their impact to only a small part of the system.
 
  - Flexible Rollbacks: If a problem is detected, updates can be rolled back on the affected subset without impacting the entire system.


- Considerations:
  - Infrastructure Support: 
    - Rolling updates often require load balancers and orchestrators (e.g., Kubernetes, AWS ECS).

  - Compatibility: 
    - Both the old and new versions must coexist temporarily, which may require backward compatibility.

  - Monitoring: 
    - Effective monitoring tools are critical to detect issues promptly
