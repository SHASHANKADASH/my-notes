# Kubectl
- It is the primary CLI tool for interacting with kubernetes clusters.
- It's essentially the bridge that connects the user to k8s, allowing the user to manage and control their cluster from the command line.

## Kubectl commands
### Deploying and managing applications
- Kubectl let's us create, update and delete the k8s objects like pods, deployments, and services.
- The below command can be used to deploy the application using yaml config.
```
kubectl apply -f my-app.yaml
```
### Inspecting Resources
- Using kubectl we can view the state and configurations of various kubernetes resources, including pods, nodes, services, namespaces.
```
kubectl get pods
```
### Debugging and troubleshooting
- Kubectl offers several commands for diagnosing issues in the cluster. You can check logs, view resource configurations, and even connect to running containers for deeper troubleshooting.
```
kubectl logs my-pod kubectl exec -it my-pod -- /bin/bash
```
### Scaling and updating deployments
- Using kubectl we can scale applications up or down by adjusting the number of replicas and update configurations or images on the fly.
```
kubectl scale deployment my-app --replicas=5 
kubectl set image deployment/my-app my-app=nginx:1.18
```
### Accessing cluster information
- Kubectl provides details about k8s cluster itself, including nodes, namespaces, and component health.
```
kubectl cluster-info
```