# Kubernetes basic concepts
## Kubernetes cluster architecture:
*This document outlines the various components you need to have for a complete and working Kubernetes cluster.*
![[kubernetes-cluster-architecture.png|828]]
### Cluster:
- A Kubernetes cluster consists of a control plane plus a set of worker machines, called nodes, that run containerized applications. 
- Every cluster needs at least one worker node in order to run Pods.
### Control Plane
- It is the brain  of kubernetes cluster.
- It acts as the central management layer that orchestrates global decisions. It manages cluster state, schedules workloads and enforces policies
It consists of 5 components:
1. **kube-api-server**
2. **etcd**
3. **kube-controller-manager**
4. **kube-scheduler**
5. **cloud-controller-manager**