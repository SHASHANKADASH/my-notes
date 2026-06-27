# Service in Kubernetes

## What is a service?
- A service in k8s is a networking abstraction that defines a logical set of pods and a permanent policy by which to access them.
- K8s pods are ephemeral(short lived) , they can restart, scale or crash due to which IP address keeps on changing.
- Service solves this problem.
- Suppose in our ngnix-deployment we have 3 pods (replicas=3):
```
nginx-deployment

Pod A -> 10.244.0.5
Pod B -> 10.244.0.6
Pod C -> 10.244.0.7
```
- Now let's say another application want to call nginx. Can it use 10.244.0.5?. No, as pods are ephemeral. 
- As soon as you delete pod A, it scales back up with different IP.
- Client needs a stable endpoint, if it using the old point then it the IP changes it won't be able to access the application.
- A service groups Pods using labels and provides a stable way to reach them.
- K8s continuously tracks pods matching the service selector and updates the backend endpoints.
- With service:
```
Client
   ↓
Service
   ↓
Pod A
Pod B
Pod C
```
### Types of service:
1. ClusterIP : For internal communication
2. NodePort: Expose outside cluster
3. LoadBalancer: Cloud load balancer
4. ExternalName: External DNS alias
5. Headless: Direct pod discovery
- We will focus on clusterIP for now as all other types are based off it. 
- ClusterIP is the default service type.
## Steps
### 1. Verify the current deployment:
- We should already have a deployment with replicas 3 . Refer: [deployment-in-kubernetes](./deployment-in-kubernetes)
- Run the command `kubectl get pods -o wide` to verify and see IP details.
![[screen-2026-06-09_23-06-05.png]]
### 2. Examine the labels
- Run : `kubectl get pods --show-labels`
![[screen-2026-06-09_23-10-05.png]]
- Service use labels to find the pods, not pod names.
- *Labels are the mechanism K8s uses for selecting groups of objects.*
### 3. Creating Service
- As with pods and deployments, we need to create a yaml file.
```
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx-service

  ports:
    - port: 80
      targetPort: 80

  type: ClusterIP
```
- To create the service we can run:
```
kubectl apply -f kubernetes/nginx-clusterip-service.yml
```
### 4. Understanding the fields
- **metadata.name** - Name of the service. This creates a DNS *nginx-service* inside the cluster.
- **spec.selector** - This tells the service which pods it needs to manage. The value *app=nginx* means that every pods with label *app=nginx* belong to the service.
- **spec.ports.port** - This defines the port of the service. Clients will call *nginx-service:80* instead of hardcoded IP of the pod.
- **spec.ports.targetPort** - This defines to which port of the pod the traffic should route to.
### 5. Observing the service details
- Run the command `kubectl get svc` to see IP details.
![[Pasted image 20260614000634.png]]
- This is a virtual IP provided by k8s. Client connect to this stable IP while k8s forwards traffic to matching Pods.
- Next, we can describe the service using the commands `kubectl describe svc nginx-service`.
![[Pasted image 20260614001000.png]]
- In the above image we can see:
    - *Selector* : app=nginx
    - *Endpoints*: 10.244.0.5:80,10.244.0.6:80,10.244.0.7:80
- The service discovered the pods through labels and built a backend endpoint list.
- K8s maintains these endpoints mappings automatically.
### 6. Dynamic endpoint update
- If we delete a pod from our deployment using `kubectl delete pods nginx-deployment-7445c8fb45-4lkx4`.
- Then, describe the service `kubectl describe svc nginx-service`.
- We will see that endpoints have changed automatically.
- This shows that Service tracks labels, not Pod identities.
### 7. DNS Discovery
- Inside K8s every service gets DNS.
- We can run a pod with busybox container for testing this
```
kubectl run test-client --image=busybox:1.36 -it --rm -- sh
```
- Now inside the pod run nslookup. `nslookup nginx-service`
- We should get something like:
```
Name:   nginx-service.default.svc.cluster.local
Address: 10.96.231.94
```
- A service gets a stable DNS name and clusterIP specifically so clients don't need to know Pod IPs.
- From the container if we do `wget -qO- http://nginx-service` or `wget -qO- http://10.96.231.94` we should see the nginx welcome html returned.

*Note: Requests sent to the service are distributed across backend Pods. ClusterIP services provide built in load balancing among healthy pods.*
### Service vs Deployment
| Deployment   | Service           |
| ------------ | ----------------- |
| Manages Pods | Exposes Pods      |
| Scaling      | Networking        |
| Rollouts     | Service Discovery |
| Self-healing | Load Balancing    |
