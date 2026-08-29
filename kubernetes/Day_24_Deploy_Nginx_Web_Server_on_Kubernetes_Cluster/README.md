# Day 24: Deploy Nginx Web Server on Kubernetes Cluster

## Solution

## Objective

Learn how to deploy an **Nginx Web Server** on a Kubernetes cluster and expose it so that users can access the application.

Today we will understand this flow:

```text
User
  ↓
Kubernetes Service
  ↓
Nginx Pod
  ↓
Nginx Web Server
Scenario

Suppose you have an Nginx web application that needs to run on Kubernetes.

Instead of manually installing Nginx on a server, Kubernetes will:

Create the Nginx Pods
Run the Nginx containers
Maintain the desired number of replicas
Expose Nginx through a Service
Allow users to access the web server

Our final architecture will look like:

                    Kubernetes Cluster
                           │
                           ▼
                      Deployment
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
           Pod 1         Pod 2         Pod 3
             │             │             │
             └─────────────┼─────────────┘
                           │
                           ▼
                        Service
                           │
                           ▼
                          User
Prerequisites

Check that your Kubernetes cluster is running:

kubectl get nodes

Expected output:

NAME           STATUS   ROLES
controlplane   Ready    control-plane
node01         Ready    <none>
Step 1: Create a Directory

Create a directory for the Day 24 Kubernetes files:

mkdir day24-nginx
cd day24-nginx
Step 2: Create Deployment YAML

Create the Deployment manifest:

vi deployment.yaml

Add the following configuration:

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
Step 3: Understand the Deployment
kind: Deployment

The Deployment manages our Nginx Pods.

replicas: 3

This tells Kubernetes:

Keep 3 Nginx Pods running.

So Kubernetes creates:

nginx-deployment
       │
       ├── Pod 1
       ├── Pod 2
       └── Pod 3

The Deployment continuously works to maintain the desired replica count.

Step 4: Understand containerPort

The Deployment contains:

ports:
  - containerPort: 80

This indicates that the Nginx container is intended to receive application traffic on port 80.

Nginx normally listens on:

Port 80
Important

containerPort: 80 by itself does not expose Nginx to users outside the Pod.

A Kubernetes Service is used to provide network access to the Pods.

Step 5: Deploy Nginx

Apply the Deployment:

kubectl apply -f deployment.yaml

Expected output:

deployment.apps/nginx-deployment created
Step 6: Check the Deployment

Run:

kubectl get deployment

Expected output:

NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3

OAOAOAThe important part is:
OAOAOA
3/3
OAOAOA
This means:

Desired replicas = 3
OAOAOAReady replicas = 3
Available replicas = 3

OAOAOAAll three Nginx Pods are ready.

Step 7: Check the Pods

Run:

kubectl get pods

Example output:

NAME                              READY   STATUS
OAOAOAnginx-deployment-xxxxx-aaaaa      1/1     Running
OAOAOAnginx-deployment-xxxxx-bbbbb      1/1     Running
nginx-deployment-xxxxx-ccccc      1/1     Running
OAOAOA
All three Pods should have:

STATUS: Running

and:

OAOAOAREADY: 1/1
OAOAOAStep 8: Check the Nginx Application

First, list the Pods:
OAOAOA
OAOAOAkubectl get pods

Copy the name of one of the Nginx Pods.

Then enter the container:

kubectl exec -it <pod-name> -- /bin/bash

Inside the container, check the Nginx version:

OAOAOAnginx -v
OAOAOA
OAOAOAExpected output will be similar to:

nginx version: nginx/1.27.x

OAOAOAYou can also inspect the Nginx configuration:

cat /etc/nginx/nginx.conf

Exit the container:

exit
Step 9: Create a Kubernetes Service

At this point, Nginx is running inside the Pods.

However, users need a way to access the application.

Create the Service manifest:

vi service.yaml

Add the following configuration:

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

Save the file.

Step 10: Understand the Service

The Service provides network access to the Nginx Pods.

The traffic flow is:

User
  │
  ▼
NodePort 30080
  │
  ▼
nginx-service
  │
  ▼
Service selects:
app: nginx
  │
  ├── Pod 1
  ├── Pod 2
  └── Pod 3

The Service uses the Pod labels to determine which Pods should receive traffic.

Step 11: Understand the Service Selector

Our Deployment creates Pods with the following label:

labels:
  app: nginx

The Service contains:

selector:
  app: nginx

This means:

Find Pods having the label app: nginx and send traffic to them.

This connection is extremely important.

Service selector
      ↓
  app: nginx
      ↓
   Pod label
      ↓
  app: nginx

If the Service selector does not match the Pod labels, the Service will not have the intended Pod endpoints.

Step 12: Understand the Service Ports

Our Service contains:

ports:
  - port: 80
    targetPort: 80
    nodePort: 30080

These are three different concepts:

NodePort
   ↓
 30080
   ↓
 port
   ↓
  80
   ↓
targetPort
   ↓
  80
   ↓
Nginx container
nodePort: 30080

The port exposed on the Kubernetes Node.

Users can access the application using:

<Node-IP>:30080
port: 80

The port exposed by the Kubernetes Service.

targetPort: 80

The port where the application is listening inside the Pod.

In this example:

NodePort   = 30080
Service     = 80
Container   = 80
Step 13: Create the Service

Apply the Service configuration:

kubectl apply -f service.yaml

Expected output:

service/nginx-service created
Step 14: Check the Service

Run:

kubectl get service

Example output:

NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)
nginx-service   NodePort   10.x.x.x       <none>        80:30080/TCP

The important part is:

80:30080/TCP

This means:

Service port = 80
NodePort      = 30080
Step 15: Check Service Endpoints

Run:

kubectl get endpoints nginx-service

You should see the IP addresses of the Nginx Pods.

Example:

NAME            ENDPOINTS
nginx-service   192.168.1.10:80,192.168.1.11:80,192.168.1.12:80

This confirms that the Service has found the Nginx Pods.

The endpoint IPs correspond to the Pods selected by:

selector:
  app: nginx
Step 16: Access Nginx

If your Kubernetes cluster is running in a VM or lab environment, find the Node IP:

kubectl get nodes -o wide

Then access Nginx using:

http://<Node-IP>:30080

For example:

http://172.30.2.2:30080

You should see the default Nginx page:

Welcome to nginx!
Step 17: Test Using curl

If you can access the Node from a terminal, run:

curl http://<Node-IP>:30080

You should receive the Nginx HTML response.

You can also test the Service from inside the Kubernetes cluster using the Service DNS name:

curl http://nginx-service

This demonstrates the difference between external and internal access.

External Access
User
  ↓
Node-IP:30080
  ↓
nginx-service
  ↓
Nginx Pod
Internal Cluster Access
Pod
  ↓
nginx-service:80
  ↓
Nginx Pod
Step 18: Check Everything

Run the following commands:

kubectl get deployment
kubectl get pods
kubectl get service
kubectl get endpoints nginx-service

You should have:

Deployment → 3/3 Ready
Pods       → 3 Running
Service    → NodePort
Endpoints  → 3 Pod IPs
Complete Architecture

The complete request flow looks like:

                         User
                           │
                           │
                           ▼
                    Node IP:30080
                           │
                           ▼
                    nginx-service
                       NodePort
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
            Pod 1        Pod 2        Pod 3
              │            │            │
              ▼            ▼            ▼
           Nginx:80     Nginx:80     Nginx:80
Important Concept: Why Deployment + Service?
Deployment

A Deployment manages the Pods.

Deployment
    ↓
Creates/manages Pods
    ↓
Keeps 3 replicas running

The Deployment provides:

Desired replica management
Self-healing
Scaling
Controlled application updates
Rollback capabilities
Service

A Service provides network access to the Pods.

Service
    ↓
Finds Pods using labels
    ↓
Routes traffic to Pods

The Service provides:

A stable network endpoint
Service discovery
Traffic routing to selected Pods
Access to Pods without depending directly on Pod IP addresses
Deployment vs Service
Kubernetes Resource	Main Responsibility
Deployment	Manages application Pods
Service	Provides network access to Pods

In simple terms:

Deployment = manages application instances

Service = provides network access to application instances
Step 19: Test Self-Healing

One of the important benefits of Kubernetes is self-healing.

Delete one Nginx Pod:

kubectl delete pod <pod-name>

Immediately check the Pods:

kubectl get pods

You should see Kubernetes create a replacement Pod.

Why?

Because the Deployment specifies:

replicas: 3

The Deployment continuously tries to maintain the desired state.

The process looks like:

3 Pods
  ↓
Delete 1 Pod
  ↓
2 Pods
  ↓
[ODeployment detects the difference
  ↓
Creates a replacement Pod
  ↓
3 Pods

This demonstrates Kubernetes' self-healing behavior.

Step 20: Scale Nginx

Suppose traffic increases and we need more Nginx replicas.

Scale the Deployment from 3 to 5 replicas:

kubectl scale deployment nginx-deployment --replicas=5

Check the Pods:

kubectl get pods

You should now have:

5 Nginx Pods

The Service automatically routes traffic to the available Pods selected by:

selector:
  app: nginx

The architecture now becomes:

                   nginx-service
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
        Pod 1         Pod 2         Pod 3
          │             │             │
          ├─────────────┼─────────────┤
          │             │             │
        Pod 4         Pod 5

The Service does not need to be manually updated when the number of replicas changes, as long as the Pods continue to match its selector.

Step 21: Clean Up

Delete the Service:

kubectl delete service nginx-service

Delete the Deployment:

kubectl delete deployment nginx-deployment

Alternatively, delete the resources using the YAML files:

kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
Useful Commands
Command	Purpose
kubectl apply -f deployment.yaml	Create or update the Nginx Deployment
kubectl get deployment	Check Deployment status
kubectl get pods	Check Nginx Pods
kubectl describe deployment nginx-deployment	View detailed Deployment information
kubectl exec -it <pod> -- /bin/bash	Enter the Nginx container
kubectl apply -f service.yaml	Create or update the Service
kubectl get service	Check Service status
kubectl get endpoints nginx-service	Check Service endpoints
kubectl get nodes -o wide	Find Node IP addresses
kubectl scale deployment nginx-deployment --replicas=5	Scale Nginx to 5 replicas
kubectl delete pod <pod-name>	Test Kubernetes self-healing
kubectl delete -f deployment.yaml	Delete the Deployment
kubectl delete -f service.yaml	Delete the Service
Expected Outcome

After completing this task:

Nginx Deployment is created.
3 Nginx Pods are running.
Nginx is verified inside the container.
Kubernetes Service is created.
Service connects to Nginx Pods using labels.
Nginx is exposed through NodePort 30080.
Nginx web page is accessed through the Node IP.
Service endpoints are verified.
Pod self-healing is tested.
Deployment scaling is tested.
Nginx is successfully exposed to users through Kubernetes.
Interview Questions
Q1. How would you deploy Nginx on Kubernetes?
Answer

I would create a Kubernetes Deployment using the Nginx image, specify the desired number of replicas, and expose the Deployment using a Kubernetes Service.

Example:

apiVersion: apps/v1
kind: Deployment

spec:
  replicas: 3

  template:
    spec:
      containers:
        - name: nginx
          image: nginx:latest

Then I would create a Service to provide network access to the Pods.

Q2. Why do we use a Deployment instead of creating a Pod directly?
Answer

A Deployment manages the desired number of Pods and provides:

Self-healing
Scaling
Controlled updates
Rollbacks
Desired-state management

For example:

replicas: 3

The Deployment ensures that Kubernetes attempts to maintain three Pods.

Q3. What is the purpose of containerPort: 80?
Answer

containerPort: 80 indicates that the container is intended to receive traffic on port 80.

It does not by itself expose the application outside the Pod.

For external or stable network access, we use a Kubernetes Service.

Q4. Why do we need a Service?
Answer

Pods are temporary and their IP addresses can change.
[I
A Service provides a stable network endpoint and routes traffic to the appropriate Pods.

The Service can continue providing access even when individual Pods are replaced.

Q5. How does the Service know which Pods to send traffic to?
Answer

The Service uses a label selector.

For example:

selector:
  app: nginx

The Pods must have the matching label:

labels:
  app: nginx

The relationship is:

Service selector
      ↓
  app: nginx
      ↓
Pod label
      ↓
  app: nginx

If the selector and labels do not match, the Service will not select those Pods.

Q6. What is the difference between port, targetPort, and nodePort?
Answer
Port	Purpose
port	Port exposed by the Kubernetes Service
targetPort	Port where the application is listening inside the Pod
nodePort	Port exposed on the Kubernetes Node

For our example:

port: 80
targetPort: 80
nodePort: 30080

The traffic flow is:

Node IP:30080
     ↓
Service port 80
     ↓
Pod targetPort 80
     ↓
Nginx
Q7. What happens if an Nginx Pod crashes?
Answer

The Deployment detects that the desired replica count is no longer satisfied and Kubernetes creates a replacement Pod.

For example:

Desired = 3 Pods
Current = 2 Pods
       ↓
Deployment detects difference
       ↓
Creates replacement Pod
       ↓
Current = 3 Pods
Q8. How do you scale Nginx from 3 to 5 replicas?

Run:

kubectl scale deployment nginx-deployment --replicas=5

Then verify:

kubectl get pods

The Deployment will create additional Pods until five replicas are running.

Key Takeaways
A Deployment manages the desired number of Nginx Pods.
replicas: 3 tells Kubernetes to maintain three Pods.
containerPort: 80 does not expose the application outside the Pod.
A Service provides stable network access to Pods.
Service selectors must match Pod labels.
port, targetPort, and nodePort have different purposes.
A NodePort Service can expose the application through the Node IP.
Service endpoints help verify whether the Service has discovered the expected Pods.
Deployments provide self-healing when Pods are deleted or fail.
Scaling the Deployment increases the number of application instances while the Service continues routing traffic to matching Pods.
Final Architecture
                           USER
                            │
                            │
                            ▼
                    Node IP : 30080
                            │
                            ▼
                    ┌───────────────┐
                    │ nginx-service │
                    │    NodePort   │
                    └───────┬───────┘
                            │
                     Selector:
                     app: nginx
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
       ┌───────┐         ┌───────┐         ┌───────┐
       │ Pod 1 │         │ Pod 2 │         │ Pod 3 │
       │Nginx  │         │Nginx  │         │Nginx  │
       │ :80   │         │ :80   │         │ :80   │
       └───────┘         └───────┘         └───────┘

Deployment manages the Pods.

Service provides network access to the Pods.

Labels connect the Service to the correct Pods.

NodePort provides external access to the application.
