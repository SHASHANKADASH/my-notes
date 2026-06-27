# Deployment in kubernetes
In the [creating-first-pod](./creating-1st-pod) section we learnt how we can setup our pod. If you check the pod.yml again, you will see that there is an identifier `kind` and it is set to `kind: pod`.
So, what is kind?
- **kind**: This attribute specifies the exact resource or object we are creating. It can be many different values like Pod, Deployment, Service, ConfigMap, Secret etc. 
In this section we will focus on `kind: Deployment`.

## Introduction
*Note: For this exercise we won't be using postgres image anymore as postgres is stateful. Also we will be doing scaling as well and postgres is usually not scaled via deployments*
### Why do we need deployments?
There are few problems with pods:
- If pod dies, we need to recreate it (for a java application for example if jvm crashes, or OOM, container exits etc.)
- If I need 3 copies of it , I need to create 3 pods
- If I need a new version, I need to delete old pod and create a new one
This doesn't scale well in production, that' why we need deployments.
### Problems solved by deployment
- Let's say the postgres-service pod that we created earlier, crashes due to some reason.
- Without deployment, nobody recreates it, application will be down and it will require manual intervention.
### What happens when you create a deployment
1. **ReplicaSet Creation**: The deployment immediately creates a ReplicaSet. This object is responsible for ensuring the exact number of Pod replicas we provided in pod.yml configs are running at all times.
2. **Pod Initialization**: The replicaSet generates the exact number of pod objects specified in the pod.yml.
- **Deployment** is responsible for:
    - Version Management
    - Scaling
    - Rollouts
    - Rollbacks
- **ReplicaSet** is responsible for:
    - Maintaining desired pod count

## Steps to create deployment
### 1. Delete existing pod:
- We can first delete the pod we created in the [creating-first-pod](./creating-1st-pod) section.
```
kubectl delete pod postgres-service
```
- Verify with `kubectl get pods` that no pod exists.
### 2. Create deployment YAML
```
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 1

  selector:
    matchLabels:
      app: nginx-service

  template:
    metadata:
      labels:
        app: nginx-service

    spec:
      containers:
        - name: nginx-service
          image: nginx:alpine
          imagePullPolicy: IfNotPresent

          ports:
            - containerPort: 8080
```
### 3. Understanding the yaml:
#### apiVersion: app/v1
- Pods use v1
- Deployments belong to apps API group
- hence it is apps/v1
#### kind: Deployment
- Tells k8s to create deployment object
#### metadata.name
- It is the deployment object identity.
- Later on we can use it for querying. `kubectl get deployment nginx-deployment`
#### spec
- This section specifies the desired state of deployment object.
#### spec.replicas
- It tells k8s to always keep specified number of pods alive.
#### spec.selector.matchLabels
- Using the information provided here the deployment knows which pods belong to it.
- When we set `app: nginx-service` under matchLabels. We are telling deployment manage all pods with label as `app: nginx-service`.
#### spec.template
- This is like pod blueprint.
- These are the instructions for deployment on how to create pods.
- Deployment does not contain pods, it contains instructions on how to create pods.
#### spec.template.metadata.labels
This will apply the label `app: nginx-service` to each pod.
#### spec.template.spec
- Everything below this is pod definition, which we have already discussed in [creating-first-pod](./creating-1st-pod).
### 4. Apply the deployment
- Now in a separate terminal window run the command `kubectl get deployments -w`. This will help us to watch the pod life cycle when we create a pod in the next step.
- Next apply the above pod.yml using kubectl
```
kubectl apply -f kubernetes/deployment.yml 
```
- You should see:
![[screen-2026-06-04_22-52-34.png]]
- Now try changing the replicas to 3 and again use apply command.
- You will see that *kubernetes immediately reacts and spins up more pods to match desired state of 3*.
![[screen-2026-06-04_22-53-57.png]]
- Run `kubectl get rs`. We will see the replicaSet information.
![[screen-2026-06-05_00-04-25.png]]
- Naming convention for pods: `deployment-name-{replica-set-hash}-{uniqe-pod-id}`
### 5. Self healing experiment
- Currently we have 3 pods. Now let's delete one pod
```
kubectl delete pods nginx-deployment-7445c8fb45-5w4p4
```
- If we watch the pods on another terminal we will see:
![[screen-2026-06-05_00-09-44.png]]
- We can see that when we terminated a pod, a new one was immediately created by k8s.
- Sequence of events: 
    1. Pod deleted
    2. ReplicaSet notices. It's job was to keep 3 pods running at all times. Desired = 3, Actual = 2
    3. Creates replacement pod.
- Similar to this when we earlier set replicaSet to 3 and applied with kubectl, ReplicaSet sees the difference in desired and actual and scales up.
- Instead of scaling by updating yaml file we can also do:
```
kubectl scale deployment fraud-service-deployment --replicas=5
```
### 5. Rolling Update
