# Config Maps
## Config Maps
- ConfigMaps is a way to separate environment specific configurations from container images.
- ConfigMaps externalize non sensitive configuration from the application image.
- The same image can be deployed across environments while configuration changes independently.
## What should be stored in ConfigMaps?
```
database host
service URLs
feature flags
log levels
environment names
Kafka bootstrap servers
```
## How can a Pod consume a ConfigMap?
Three common ways:
- Environment Variables
- Volume Mounts
- Application reads Kubernetes API directly

In the upcoming steps section we will be configuring config maps and see how then can be consumed.
## Steps
### 1. Create ConfigMap
- Create the below yaml file.
```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: nginx-config

data:
  APP_NAME: Nginx Test App
  ENVIRONMENT: local
  LOG_LEVEL: INFO
```
- As always, we need to apply the file with `kubectl apply -f nginx-config.yaml`.
- We can verify with `kubectl get configmaps`.
- Inspect the details with `kubectl describe configmap nginx-config`.
![[screen-2026-06-27_12-51-47.png]]
- ConfigMaps are just K8s objects.
- At this point the ConfigMap exists but Pod is not aware about it.
- Deployment and ConfigMap are separate objects in k8s. We need to connect them.
### 2. Modify deployment file
- In the deployment yaml file we can use the ConfigMap we defined in the previous step.
```yaml
    spec:
      containers:
        - name: nginx-service
          image: nginx:mainline-alpine
          imagePullPolicy: IfNotPresent
          env:
            - name: APP_NAME
              valueFrom:
                configMapKeyRef:
                  name: nginx-config
                  key: APP_NAME
           
```
- Apply the changes: `kubectl apply -f deployment.yaml`
- Grab one of the pods, and run `kubectl exec -it <pod-name> -- sh`
- Then run `env | grep APP_NAME`. We should see:
![[screen-2026-06-27_17-39-18.png]]
*Note: Here we have pulled the env variable in our deployment. The same can also be done directly in pod.yaml*
### 3. Import entire config map
- We can import the entire config by using envFrom instead of env:
```yaml
    spec:
      containers:
        - name: nginx-service
          #image: nginx:alpine
          image: nginx:mainline-alpine
          imagePullPolicy: IfNotPresent
          envFrom:
            - configMapRef:
                name: nginx-config # imports the whole config map
           
```
- Now if we apply it again and run `kubectl exec -it <pod-name> -- env`, we can find all three env variables which we created in our config map.
*Note: Here we can also see that whenever we are changing our deployments.yaml definition, k8s is doing rolling update and spinning up new pods. Hence, in the above command old pod name won't work.*
>*Q: What happens if ConfigMap changes?
 A: Environment variable values are populated when the container starts. Updating the ConfigMap does not automatically update env vars in a running container; a restart is required.*
- Updating configMap and doing kubectl apply does nothing to the running pods. We must apply changes to deployment or pods as well.
### 4. Mount config maps as files
- Consider the below updated config file:
*Note: The pipe symbol (`|`) in YAML denotes a **literal block scalar**, which tells the parser that any indented lines following the symbol should be treated as a **multi-line string that preserves all newlines and spaces exactly as written** *
```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: nginx-config

data:
  application.properties: |
    app.name=Nginx Test App
    log.level=INFO
    environment=local
```
- The string under data can be mounted into containers as files.
- In our deployment, we can mount it under volumes like:
```yaml
    spec:
      containers:
        - name: nginx-service
          image: nginx:mainline-alpine
          imagePullPolicy: IfNotPresent
          
          volumeMounts:
            - name: config-volume
              mountPath: /etc/config

          ports:
            - containerPort: 8080
      volumes:
        - name: config-volume
          configMap:
            name: nginx-config
```
- After applying the above updates using kubectl apply, we can see the above configs mounted as files in /etc/config.
- Run `kubectl exec -it <pod> -- sh`.
- Once inside the pod, navigate to /etc/config. Then run:
![[screen-2026-06-27_19-35-53.png]]
- Most production systems mount ConfigMaps as files rather that env vars.
- *Important: Kubernetes automatically refreshes ConfigMap-backed volumes (eventually), but environment vars require pod restart.*