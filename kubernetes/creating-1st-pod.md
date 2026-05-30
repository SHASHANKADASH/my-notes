# Setting up your first pod:
This guide is how we can create our first pod. I will be pulling latest postgres image (required for my project) but you can use any image you like.

## Prerequisites:
1. You must have docker installed and running on your system. If you don't know how to do that yet, then you are at the wrong place 🙂.
2. Kind (kubenetes in docker): [Installation guide](https://kind.sigs.k8s.io/docs/user/quick-start#installing-from-release-binaries): Kind is a tool that will help us to run a local kubernetes cluster. It uses Docker containers as cluster nodes.
3. Kubectl.
    - [Installation guide](https://kubernetes.io/docs/tasks/tools/#kubectl)
    - [Kubectl Guide](./kubectl.md)
## Steps:
### 1. Pull postgres image
```
docker pull postgres:16
docker images // to verify whether you have the postgres image
```
### 2. Create a new cluster with kind
```
kind create cluster --name my-cluster
kubectl cluster-info
kubectl get nodes
```
### 3. Load image into Kind
Since Kind uses its own docker nodes, we need to load the image into Kind.
```
kind load docker-image postgres:16 --name my-cluster

// verify that the image exists
docker exec -it my-cluster-control-plane crictl images // basically you are going inside the the control plane and checking whether images got loaded
```
Note: 
- Once we go inside the *my-cluster-control-plane* node we can not longer use docker commands.
- Because docker is not the container runtime inside kubernetes node.
- Container is the container runtime in kubernetes node.
- *crictl* is the tool designed specifically for inspecting and debugging kubernetes
### 4. 