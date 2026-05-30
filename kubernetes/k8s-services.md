### Services in K8s
- K8s services are resources that map network traffic to the pods in a cluster.
- We need to create a service each time we want to expose a set of pods over the network, whether within your cluster or externally.
- A more technical definition is: *K8s services are API objects that enable network exposure for one or more cluster Pods*.
- Services are integral part of k8s networking model and provide important abstractions of lower level components, which could behave differently b/w different clouds.
### Why are they needed in k8s?
- Services are necessary because of distributed architecture of k8s clusters.
- Apps are routinely deployed  as pods that could have thousands of replicas, spanning hundreds of physical compute nodes.
- When user is interacting with an app, the service routes the request to the one of the available replicas.
- Service sits in front of the pods. All network traffic flows into the service before being redirected to one of the available pods.
- The other apps can then communicate with the service's IP address or DNS name to reliably access the pods that we have exposed.
- DNS for services is enabled automatically through the k8s service discovery system.
- Each service is assigned a DNS A or AAAA record in the format `{service-name}.{namespace-name}.svc.cluster-domain`.
- e.g. A service called *demo* in the default namespace will be accessible within cluster.local cluster at `demo.default.svc.cluster.local`.
- This enables reliable in-cluster networking without having to lookup IP addresses.
### Kubernetes service types:
All k8s services ultimately forward n/w traffic to the pods they represent. There are 5 main types of services in k8s:
#### 1. ClusterIP Services:
- ClusterIP services assign an IP address that can be used to reach the service from within your cluster.
- They don't expose the service externally.
- It is the default service type used when you don't specify an alternative option.
#### 2. NodePort services:
- NodePort services are exposed externally through a specified static port binding on each of our nodes.
- Hence, we can access the service by connecting to the port on any of our cluster's nodes.
- NodePort services are also assigned a cluster IP address that can be used to reach them within the cluster, just like ClusterIP services.
- Use of NodePort services is generally unadvisable. They have functional limitations and can lead to security issues:
   - Anyone who can connect to the port on your nodes can access the service.
   - Each port number can only be used by one NodePort service at a time to prevent conflicts.
   - Every node in your cluster has to listen to the port by default, even if they are not running a pod that's included in the service.
   - No automatic load balancing: clients are served by the node they connect to.
#### 3. LoadBalancer services:
- LB services are exposed outside the cluster using an external load balancer resource.
- This requires a connection to load balancer provider, typically achieved by integrating cluster with cloud env.
- Creating a LoadBalancer service will then automatically provision a new load balancer infra component in your cloud account.
- This functionality is automatically configured when we use a manager kubernetes service such as aws eks.
- Once we have created a LoadBalancer service , we can point public DNS records to the provisioned loadbalancer's IP address.
- This will then direct traffic to our K8s service. Therefore loadbalancers are the service type we should normally use when we need an app to be accessible outside k8s.
#### 4. ExternalName Services
- External service allow to access external resources from within the k8s cluster.
- Unlike the other service types, they don't proxy traffic to the pods.
- When we create external service, we need to set the spec.externalName manifest field to the external address we want to route to (such as `example.com`).
- K8s then adds a CNAME DNS record to your cluster that resolves the service's internal address (such as `my-external-service.app-namespace.svc.cluster.local`) to the external address (`example.com`).
- This allows us to change th external address in the future, without having to reconfigure the workloads that refer to it.
#### 5. Headless services
- These services don't provide load balancing or a cluster IP address.
- They're headless because k8s doesn't automatically route traffic through them.
- This allows us to use DNS lookups to discover the individual IP addresses of any Pods selected by the service.
- A headless service is useful when we want to interface with other service discovery systems without kube-proxy interference.
- To create one we need to set the service's spec.clusterIP field to None value.