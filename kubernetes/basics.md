# Kubernetes basic concepts
## Kubernetes cluster architecture:
*This document outlines the various components you need to have for a complete and working Kubernetes cluster.*
![[kubernetes-cluster-architecture.png|840]]
### Cluster:
- A Kubernetes cluster consists of a control plane plus a set of worker machines, called nodes, that run containerized applications. 
- Every cluster needs at least one worker node in order to run Pods.
### Control Plane
- It is the brain  of kubernetes cluster.
- It acts as the central management layer that orchestrates global decisions. It manages cluster state, schedules workloads and enforces policies
It consists of 5 components:
1. **kube-api-server** : 
    - The kube-api-server is the central management hub of a kubernetes cluster. 
    - It acts as the primary *front-end* of the control plane.
    - Exposes kubernetes API so that users, external tools, and internal component can communicate.
    - The main implementation of a kubernetes API server is kube-apiserver.
    - kube-apiserver is designed to scale horizontally.
2. **etcd** : 
    - ectd is a strongly consistent, distributed key-value store that provides a reliable way to store data that needs to be accessed by a distributed system or cluster of machines.
    - It is kubernetes' backing store for all cluster data.
    - It serves as the single source of truth for the entire cluster,storing all data, configuration settings, secrets, and metadata required for kubernetes to function.
    - Everything that happens in the cluster (e.g. pod deployments, scaling events, node IPs, and config updates) is recorded in etcd.
    - The kubernetes API server (kube-api-server) is the only component in the control plane that directly interacts with etcd. The other control plane components and users interact through the API server.
    - **Raft consensus algorithm**: etcd uses the raft protocol to elect leader and replicate the data safely across all nodes, ensuring that if an individual machine fails, the cluster data state remains accessible and data is not lost.
    - **Reactive watches**: K8s components uses etcd's *watch* feature to be notified instantly whenever a change happens in the key-value store, prompting the system react (e.g. spinning up a new Pods to meet a deployment requirement).
    - **Why etcd is critical?** : 
        - Fault tolerance: Because it is distributed, etcd gracefully handles machine failures. It typically runs in an odd numbered cluster to maintain quorum.
        - Catastrophic failure risk: Since all the cluster data is stored here, if the etcd data-store is completely lost and un-backed up, the cluster essentially loses its memory entirely. Because of this regular etcd backups are mandatory in production.
3. **kube-scheduler**
    - This component is responsible for assigning pods to nodes within a cluster.
    - It ensures that workloads are distributed efficiently while meeting resource requirements, policy constraints and organization goals.
    - **How the K8s scheduler works?**
        - 
4. **kube-controller-manager**
    - The k8s controller manager is a daemon that runs multiple controllers within a single binary.
    - Each controller manages a specific type of resource in k8s cluster, such as, nodes, pods, endpoints, and replication controllers.
    - These controllers continuously monitor the cluster's current state and take corrective actions to reconcile it with the desired state defined by the user.
    - For example if a pod crashes or is deleted , the controller manager will ensure that a replacement pod is created to maintain specified replica count.
    - **Key Controllers in K8s controller manager:**
        1. Node controller : Monitors the health and availability of nodes in cluster. Handles node related events.
        2. Replication controller: Ensures the desired number of pod replicas are running at all times. Auto scales replicas up and down based on the replica count.
        3. Endpoint controller: Populates the *Endpoints* resource with info about which pods are backing a specific service.
        4. Service account and token controller: Manages default service accounts and their associated API tokens.
        5. Persistent volume controller: Oversees the binding of Persistent Volumes (PVs) to persistent volume chains (PVCs).
        6. Job controller: Manages the completion of batch jobs, ensuring all the tasks within the job are executed.
    - **How the controller manager works?**
        1. Reconciliation loop: Each controller operates on a recon loop, compares current and desired state of the cluster and takes action to reconcile difference if discrepancies are found.
        2. Leader Election: In high availability setups, multiple controller manager instances may run, but only one acts as leader which is chosen using leader election mechanisms. This instance takes control.
        3. Pluggable architecture: Kubernetes allows custom controllers to be added to the controller managers or run separately as custom controllers tailored to specific needs.
    - **Why controller manager matters?**
      1. Reliability: Ensures that cluster remains in the desired state, even during failures.
      2. Scalability: Automatically handles the scaling of resources, ensuring workloads can adapt to changing demands.
      3. Automation: Does automatic pod scaling , volume binding and service endpoint updates reducing manual intervention.
      4. Flexibility: Supports the integration of custom controllers, allowing orgs to extend k8s functionality for specific use cases.
5. **cloud-controller-manager**