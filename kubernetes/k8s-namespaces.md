# Kubernetes Namespaces
- Namespaces provide a way to partition and organize resources within a k8s cluster.
- They can be created using kubectl or yaml config file.
![[k8s-namespaces.png|419]]
- Namespaces can be used for logical groupings.
- For example dev, test, prod etc.
- If a resource is name spaced that means it cannot be created without a namespace.
### Default namespace types in K8s
- When we create a new k8s cluster by default it has following name spaces:
    - **default:** the default k8s namespace is used for resources that don't have a specific namespace specified when created.
    - **kube-node-lease:** This namespace manages *Lease* objects associated with each cluster nodes. Node leases function as heartbeats, allowing control plane to efficiently monitor node availability and failures.
    - **kube-system:** This namespace is strictly reserved for core k8s control plane components. It houses foundational infrastructure like the API server, scheduler and DNS services.
    - **kube-public:** This namespace is readable by all clients, including unauthenticated users. It is used for bootstrapping and cluster wide public data like cluster info or certificates.
```
kubectl get namespaces
```