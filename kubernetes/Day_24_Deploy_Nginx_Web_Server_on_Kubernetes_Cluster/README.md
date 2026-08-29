# Day 24: Deploy Nginx Web Server on Kubernetes Cluster

## Solution

## Objective

Learn how to deploy an **Nginx Web Server** on a Kubernetes cluster and expose it so that users can access the application.

In this hands-on task, we will understand the following flow:

```text
User
  |
  v
Kubernetes Service
  |
  v
Nginx Pod
  |
  v
Nginx Web Server
```

The lab also demonstrates:

* Kubernetes Deployment
* Multiple Pod replicas
* `containerPort`
* Kubernetes Service
* `NodePort`
* Service selectors
* Service endpoints
* Internal and external application access
* Pod self-healing
* Deployment scaling
* Basic Kubernetes troubleshooting commands

---

## Scenario

Suppose you have an Nginx web application that needs to run on Kubernetes.

Instead of manually installing Nginx on individual servers, Kubernetes can:

* Create the Nginx Pods
* Run the Nginx container
* Maintain the desired number of replicas
* Replace failed Pods
* Expose Nginx through a Service
* Route traffic to available Nginx Pods
* Scale the application when required

The final architecture will look like this:

```text
                    Kubernetes Cluster
                           |
                           v
                      Deployment
                           |
              +------------+------------+
              |            |            |
              v            v            v
            Pod 1        Pod 2        Pod 3
              |            |            |
              +------------+------------+
                           |
                           v
                        Service
                           |
                           v
                          User
```

---

# Prerequisites

Before starting the lab, make sure a Kubernetes cluster is running.

Check the Kubernetes nodes:

```bash
kubectl get nodes
```

Expected output:

```text
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   ...   ...
node01         Ready    <none>          ...   ...
```

Both nodes should have a `Ready` status.

---

# Step 1: Create a Directory

Create a working directory for the Day 24 task:

```bash
mkdir day24-nginx
cd day24-nginx
```

---

# Step 2: Create the Deployment YAML

Create a Deployment manifest:

```bash
vi deployment.yaml
```

Add the following configuration:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

Save the file.

---

# Step 3: Understand the Deployment

The Kubernetes `Deployment` manages the Nginx Pods.

### `kind: Deployment`

This tells Kubernetes that we are creating a Deployment resource.

```yaml
kind: Deployment
```

### `replicas: 3`

This tells Kubernetes to maintain three Nginx Pod replicas.

```yaml
replicas: 3
```

The resulting structure will look like:

```text
nginx-deployment
       |
       +---- Pod 1
       |
       +---- Pod 2
       |
       +---- Pod 3
```

If one Pod fails, the Deployment works with the underlying ReplicaSet to create a replacement Pod so that the desired replica count is maintained.

---

# Step 4: Understand `containerPort`

The Deployment contains:

```yaml
ports:
  - containerPort: 80
```

This indicates that the Nginx container is intended to receive application traffic on port `80`.

Nginx normally listens on:

```text
Port 80
```

### Important

`containerPort: 80` does **not** expose the application outside the Pod.

It describes the container port. To provide network access through Kubernetes, we need a **Service**.

The flow is:

```text
Container
   |
   | listens on
   v
Port 80
   |
   v
Kubernetes Service
   |
   v
Users
```

---

# Step 5: Deploy Nginx

Apply the Deployment manifest:

```bash
kubectl apply -f deployment.yaml
```

Expected output:

```text
deployment.apps/nginx-deployment created
```

---

# Step 6: Check the Deployment

Verify the Deployment:

```bash
kubectl get deployment
```

Expected output:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

The important value is:

```text
3/3
```

This means:

* Desired replicas = 3
* Ready replicas = 3
* Available replicas = 3

The Deployment is successfully maintaining three ready Pods.

---

# Step 7: Check the Pods

Run:

```bash
kubectl get pods
```

Expected output will look similar to:

```text
NAME                                  READY   STATUS    RESTARTS   AGE
nginx-deployment-xxxxx-aaaaa          1/1     Running   0          ...
nginx-deployment-xxxxx-bbbbb          1/1     Running   0          ...
nginx-deployment-xxxxx-ccccc          1/1     Running   0          ...
```

All three Nginx Pods should show:

```text
READY     1/1
STATUS    Running
```

Pod names will be different in your cluster.

---

# Step 8: Check the Nginx Application

First, list the Pods:

```bash
kubectl get pods
```

Copy the name of one Nginx Pod.

Then enter the container:

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

Inside the container, check the Nginx version:

```bash
nginx -v
```

Expected output will look similar to:

```text
nginx version: nginx/1.27.x
```

You can also inspect the Nginx configuration:

```bash
cat /etc/nginx/nginx.conf
```

After checking the configuration, exit the container:

```bash
exit
```

---

# Step 9: Create a Kubernetes Service

At this point, Nginx is running inside the Pods.

However, users need a stable way to access the application.

Create a Service manifest:

```bash
vi service.yaml
```

Add:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

Save the file.

---

# Step 10: Understand the Service

The Kubernetes Service provides network access to the Nginx Pods.

The request flow is:

```text
User
 |
 v
NodePort 30080
 |
 v
nginx-service
 |
 v
Service Selector
 |
 | app: nginx
 |
 +--------+--------+
 |        |        |
 v        v        v
Pod 1    Pod 2    Pod 3
 |        |        |
 +--------+--------+
          |
          v
       Nginx:80
```

The Service provides a stable endpoint while the individual Pod IP addresses can change.

---

# Step 11: Understand the Service Selector

The Deployment creates Pods with the following label:

```yaml
labels:
  app: nginx
```

The Service uses:

```yaml
selector:
  app: nginx
```

This means the Service looks for Pods having:

```text
app=nginx
```

The relationship is:

```text
Service selector
      |
      v
app: nginx
      |
      v
Pod label
      |
      v
app: nginx
```

If the Service selector does not match the Pod labels, the Service will not have the intended Pod endpoints.

This is one of the most important concepts to understand when troubleshooting Kubernetes Services.

---

# Step 12: Understand the Service Ports

The Service contains three port-related fields:

```yaml
ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

These represent different parts of the traffic path.

```text
NodePort
   |
   | 30080
   v
Service
   |
   | port 80
   v
targetPort 80
   |
   v
Nginx Container
```

## `nodePort`

```yaml
nodePort: 30080
```

This is the port exposed on the Kubernetes node.

Users can access the application using:

```text
http://<Node-IP>:30080
```

## `port`

```yaml
port: 80
```

This is the port exposed by the Kubernetes Service.

## `targetPort`

```yaml
targetPort: 80
```

This is the port on which the application is listening inside the selected Pod.

Therefore:

```text
nodePort:   30080
port:       80
targetPort: 80
```

---

# Step 13: Create the Service

Apply the Service manifest:

```bash
kubectl apply -f service.yaml
```

Expected output:

```text
service/nginx-service created
```

---

# Step 14: Check the Service

Run:

```bash
kubectl get service
```

Expected output will look similar to:

```text
NAME            TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)
nginx-service   NodePort   10.x.x.x        <none>        80:30080/TCP
```

The important part is:

```text
80:30080/TCP
```

This means:

```text
Service port = 80
NodePort     = 30080
```

The Service forwards traffic toward the selected Pods on the configured `targetPort`.

---

# Step 15: Check Service Endpoints

Check the endpoints associated with the Service:

```bash
kubectl get endpoints nginx-service
```

Expected output may look similar to:

```text
NAME            ENDPOINTS
nginx-service   192.168.1.10:80,192.168.1.11:80,192.168.1.12:80
```

These addresses represent the Pods selected by the Service.

This is useful when troubleshooting Service connectivity.

If the Service exists but there are no endpoints, check:

1. Pod labels
2. Service selector
3. Pod status
4. Pod readiness
5. Service configuration

---

# Step 16: Access Nginx

If the Kubernetes cluster is running in a VM or lab environment, first find the Node IP:

```bash
kubectl get nodes -o wide
```

Identify the Node IP address.

Then access:

```text
http://<Node-IP>:30080
```

For example:

```text
http://172.30.2.2:30080
```

You should see the default Nginx page:

```text
Welcome to nginx!
```

The exact Node IP will depend on your Kubernetes environment.

---

# Step 17: Test Using `curl`

You can also test the application using `curl`.

From a terminal that can reach the Node:

```bash
curl http://<Node-IP>:30080
```

You should receive the Nginx HTML response.

For example:

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```

---

## Test Nginx Through the Service

From inside the Kubernetes cluster, you can access the Service by its DNS name:

```bash
curl http://nginx-service
```

This demonstrates two different access patterns.

### External Access

```text
Client
  |
  v
Node-IP:30080
  |
  v
nginx-service
  |
  v
Nginx Pod
```

### Internal Cluster Access

```text
Pod
 |
 v
nginx-service:80
 |
 v
Nginx Pod
```

The Service provides a stable endpoint for communication with the application.

---

# Step 18: Check Everything

Run the following commands:

```bash
kubectl get deployment
```

```bash
kubectl get pods
```

```bash
kubectl get service
```

```bash
kubectl get endpoints nginx-service
```

Expected state:

```text
Deployment  -> 3/3 Ready
Pods        -> 3 Running
Service     -> NodePort
Endpoints   -> 3 Pod IPs
```

---

# Complete Architecture

The complete request path is:

```text
                         User
                           |
                           |
                           v
                    Node IP:30080
                           |
                           v
                    nginx-service
                       NodePort
                           |
                 +---------+---------+
                 |         |         |
                 v         v         v
               Pod 1     Pod 2     Pod 3
                 |         |         |
                 v         v         v
              Nginx:80  Nginx:80  Nginx:80
```

---

# Important Concept: Deployment + Service

A common Kubernetes design is to use a **Deployment** together with a **Service**.

## Deployment

The Deployment manages the application Pods.

```text
Deployment
    |
    v
Creates and manages Pods
    |
    v
Maintains desired replicas
    |
    v
3 Nginx Pods
```

## Service

The Service provides network access to the Pods.

```text
Service
    |
    v
Uses label selector
    |
    v
Finds matching Pods
    |
    v
Routes traffic to Pods
```

Therefore:

```text
Deployment = manages application instances

Service = provides network access to application instances
```

---

# Step 19: Test Kubernetes Self-Healing

One of the important features of a Deployment is self-healing.

First, check the Pods:

```bash
kubectl get pods
```

Delete one Nginx Pod:

```bash
kubectl delete pod <pod-name>
```

Immediately check the Pods again:

```bash
kubectl get pods
```

You should see Kubernetes create a replacement Pod.

The process looks like:

```text
3 Pods Running
      |
      v
Delete 1 Pod
      |
      v
2 Pods Running
      |
      v
Deployment detects desired state mismatch
      |
      v
Replacement Pod created
      |
      v
3 Pods Running
```

Why does this happen?

Because the Deployment specifies:

```yaml
replicas: 3
```

Kubernetes continuously works toward the desired state.

If only two Pods are running, Kubernetes creates another Pod to return to three replicas.

---

# Step 20: Scale Nginx

Suppose application traffic increases and you need more Nginx replicas.

Scale the Deployment from 3 to 5 replicas:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Check the Pods:

```bash
kubectl get pods
```

You should now have five Nginx Pods.

The Service automatically sends traffic to the available Pods matching:

```yaml
selector:
  app: nginx
```

The flow becomes:

```text
                    nginx-service
                          |
          +---------------+---------------+
          |       |       |       |       |
          v       v       v       v       v
        Pod 1   Pod 2   Pod 3   Pod 4   Pod 5
```

---

# Step 21: Clean Up

To delete the Service:

```bash
kubectl delete service nginx-service
```

To delete the Deployment:

```bash
kubectl delete deployment nginx-deployment
```

Alternatively, delete the resources using their YAML files:

```bash
kubectl delete -f deployment.yaml
```

```bash
kubectl delete -f service.yaml
```

---

# Useful Commands

| Command                                                  | Purpose                                         |
| -------------------------------------------------------- | ----------------------------------------------- |
| `kubectl get nodes`                                      | Check Kubernetes cluster nodes                  |
| `kubectl apply -f deployment.yaml`                       | Create or update the Nginx Deployment           |
| `kubectl get deployment`                                 | Check Deployment status                         |
| `kubectl get pods`                                       | Check Nginx Pods                                |
| `kubectl describe deployment nginx-deployment`           | View detailed Deployment information            |
| `kubectl exec -it <pod-name> -- /bin/bash`               | Enter the Nginx container                       |
| `kubectl apply -f service.yaml`                          | Create or update the Service                    |
| `kubectl get service`                                    | Check Service status                            |
| `kubectl get endpoints nginx-service`                    | Check Pod endpoints associated with the Service |
| `kubectl get nodes -o wide`                              | Find Node IP addresses                          |
| `kubectl scale deployment nginx-deployment --replicas=5` | Scale Nginx to five replicas                    |
| `kubectl delete pod <pod-name>`                          | Test Pod self-healing                           |
| `kubectl delete service nginx-service`                   | Delete the Nginx Service                        |
| `kubectl delete deployment nginx-deployment`             | Delete the Nginx Deployment                     |

---

# Expected Outcome

After completing this lab, you should have successfully:

* Created an Nginx Deployment.
* Created three Nginx Pod replicas.
* Verified that all Pods are running.
* Verified the Nginx installation inside a Pod.
* Understood the purpose of `containerPort: 80`.
* Created a Kubernetes NodePort Service.
* Connected the Service to Nginx Pods using labels.
* Understood the difference between `port`, `targetPort`, and `nodePort`.
* Verified the Service endpoints.
* Accessed the Nginx web server through the Node IP.
* Tested Nginx using `curl`.
* Tested Kubernetes Pod self-healing.
* Scaled the Deployment from three to five replicas.
* Understood how a Service routes traffic to matching Pods.

---

# Interview Questions

## Q1. How would you deploy Nginx on Kubernetes?

### Answer

I would create a Kubernetes Deployment using the Nginx container image, define the desired number of replicas, and expose the Deployment through a Kubernetes Service.

For example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3
```

Then I would create a Service to provide network access to the Nginx Pods.

---

## Q2. Why do we use a Deployment instead of creating a Pod directly?

### Answer

A Deployment provides higher-level application management.

It can:

* Maintain the desired number of replicas.
* Replace failed Pods.
* Support scaling.
* Support controlled application updates.
* Support rollbacks.

For example:

```yaml
spec:
  replicas: 3
```

This tells Kubernetes to maintain three instances of the application.

---

## Q3. What is the purpose of `containerPort: 80`?

### Answer

`containerPort: 80` indicates that the container is intended to receive application traffic on port `80`.

It does **not** expose the application outside the Pod by itself.

To provide network access, we can use a Kubernetes Service.

```yaml
ports:
  - containerPort: 80
```

---

## Q4. Why do we need a Kubernetes Service?

### Answer

Pods are temporary resources and their IP addresses can change.

A Service provides a stable network endpoint and routes traffic to the appropriate Pods.

For example:

```text
Client
  |
  v
Service
  |
  v
Nginx Pods
```

The Service also uses selectors to determine which Pods should receive traffic.

---

## Q5. How does the Service know which Pods to send traffic to?

### Answer

The Service uses a label selector.

The Service contains:

```yaml
selector:
  app: nginx
```

The Pods contain:

```yaml
labels:
  app: nginx
```

Because the labels match, the Service can identify the Pods and route traffic to them.

---

## Q6. What is the difference between `port`, `targetPort`, and `nodePort`?

### Answer

They represent different ports in the Kubernetes networking path.

```text
nodePort
   |
   v
port
   |
   v
targetPort
   |
   v
Application
```

### `nodePort`

The port exposed on the Kubernetes node.

```yaml
nodePort: 30080
```

### `port`

The port exposed by the Kubernetes Service.

```yaml
port: 80
```

### `targetPort`

The application port inside the selected Pod.

```yaml
targetPort: 80
```

For this lab:

```text
nodePort   = 30080
port       = 80
targetPort = 80
```

---

## Q7. What happens if an Nginx Pod crashes?

### Answer

The Deployment detects that the actual number of running replicas no longer matches the desired replica count.

Because the Deployment specifies:

```yaml
replicas: 3
```

Kubernetes creates a replacement Pod to return the application to the desired state.

---

## Q8. How do you scale Nginx from 3 to 5 replicas?

### Answer

Use:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Then verify:

```bash
kubectl get pods
```

The Deployment should create additional Pods until five replicas are running.

---

# Key Takeaways

1. **Deployment manages application Pods.**
2. **A Service provides stable network access to Pods.**
3. `containerPort` does not expose an application outside the Pod.
4. Service selectors connect Services to Pods through labels.
5. `port`, `targetPort`, and `nodePort` serve different purposes.
6. A NodePort Service can expose an application through a Kubernetes node.
7. Service endpoints help verify whether the Service has discovered the intended Pods.
8. Deployments provide self-healing by maintaining the desired replica count.
9. Deployments can be scaled without manually creating individual Pods.
10. Kubernetes troubleshooting requires checking the complete path from the client to the application.

---

# Final Architecture Summary

```text
                         USER
                           |
                           |
                           v
                  Node IP : 30080
                           |
                           v
                   +---------------+
                   | nginx-service |
                   |   NodePort    |
                   +---------------+
                           |
                    Selector:
                     app=nginx
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          Pod 1          Pod 2          Pod 3
             |             |             |
             v             v             v
          Nginx:80      Nginx:80      Nginx:80
```

The complete application flow is:

```text
User
  |
  v
Node IP:30080
  |
  v
Kubernetes Service
  |
  | Selector: app=nginx
  |
  +--------+--------+
  |        |        |
  v        v        v
 Pod 1    Pod 2    Pod 3
  |        |        |
  +--------+--------+
           |
           v
       Nginx:80
```

This lab demonstrates the basic Kubernetes application deployment pattern:

```text
Deployment
    |
    v
Pods
    |
    v
Service
    |
    v
Application Access
```

