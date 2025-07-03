---
layout: default
title: Learn Resources When you have time
date:   2021-09-11 20:28:05 -0400
categories: english
nav_order: 101

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Command lines used in Kubernetes

1. Delte all pods : `kubectl delete pods --all`
2. Show Labels in pods: `kubectl get pods --show-labels`
3. Label deployment with multiple labels: `kubectl label deployments bandicoot-prod env=prod color=red`
4. Create a deployment with replicas: `kubectl create deployment bandicoot-prod  --image=gcr.io/kuar-demo/kuard-amd64:green  --replicas=2  --port=8080`
5. Show deployments labels: `kubectl get deployments --show-labels`
6. Check how a kubernetes object changes over time for a specific service: `kubectl get endpoints <service-name> --watch`

# [Why expose the deployment to service](https://www.google.com/search?q=kubectl+expose+deployment+meaning&rlz=1C1UEAD_enUS1114US1114&oq=kubectl+expose+deployment+meaning&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIHCAEQIRigATIHCAIQIRigATIHCAMQIRigATIHCAQQIRigATIHCAUQIRigATIHCAYQIRifBdIBCDQxNTdqMGo0qAIAsAIB&sourceid=chrome&ie=UTF-8)

kubectl expose deployment is a Kubernetes command that creates a new Kubernetes Service to expose an existing Deployment. It essentially makes your application, managed by the Deployment, accessible from outside the Kubernetes cluster or within the cluster via a stable endpoint. 
Here's a breakdown:

### Deployment:
A Kubernetes Deployment manages a set of identical Pods (replicas) and handles updates, rollouts, and scaling of those pods. 
### Service:
A Kubernetes Service provides a stable IP address and DNS name for accessing a set of Pods, even as those Pods are created and destroyed. 

`kubectl expose:`

This command takes an existing Deployment and creates a Service that routes traffic to the Pods managed by that Deployment. 

### Why use it?
Without a Service, accessing your application would require knowing the IP addresses of individual Pods, which are ephemeral and can change. The Service provides a consistent way to reach your application. 
### How it works:
The kubectl expose command automatically creates a Service object with selectors that match the labels of the Deployment. This ensures that the Service routes traffic to the correct Pods. 
In simpler terms: Imagine you have a web application running on multiple servers (represented by Pods in Kubernetes). kubectl expose deployment lets you create a "front door" (the Service) that users can use to access your application, even if the underlying servers (Pods) are restarted or replaced. 
#### Example:
To expose a Deployment named "my-app" on port 80, you would use the command: 
``` bash
kubectl expose deployment my-app --port=80 --target-port=8080
```

This command creates a Service that listens on port 80 and forwards traffic to the Pods in the "my-app" Deployment on port 8080 (where your application is likely listening). 

# [What is the difference between a pod and a deployment?](https://stackoverflow.com/questions/41325087/what-is-the-difference-between-a-pod-and-a-deployment#comment83175967_47729498)

From the link and show one anwser:

#### Kubernetes has three Object Types you should know about:

- Pods - runs one or more closely related containers
- Services - sets up networking in a Kubernetes cluster
- Deployment - Maintains a set of identical pods, ensuring that they have the correct config and that the right number of them exist.
- 
### Pods:

- Runs a single set of containers
- Good for one-off dev purposes
- Rarely used directly in production

### Deployment:

- Runs a set of identical pods
- Monitors the state of each pod, updating as necessary
- Good for dev
- Good for production

And I would agree with other answers, forget about Pods and just use Deployment. Why? Look at the second bullet point, it monitors the state of each pod, updating as necessary.

So, instead of struggling with error messages such as this one:

`Forbidden: pod updates may not change fields other than spec.containers[*].image`

So just refactor or completely recreate your Pod into a Deployment that creates a pod to do what you need done. With Deployment you can change any piece of configuration you want to and you need not worry about seeing that error message.

# [Use a web browser to connect to the app via the Service](https://kubernetes.io/docs/tasks/access-application-cluster/service-access-application-cluster/#:~:text=Use%20the%20node%20address%20and%20node%20port%20to,%3Cnode-port%3E%20is%20the%20NodePort%20value%20for%20your%20service.)

`curl http://<public-node-ip>:<node-port>`

<public-node-ip> can be get by using the command `kubectl describe pods hello-pod`

```bash
(base) ➜ kubectl describe pods hello-pod
Name:             hello-pod
Namespace:        default
Priority:         0
Service Account:  default
Node:             k3d-mycluster-server-0/172.31.0.2 
Start Time:       Fri, 04 Oct 2024 10:39:48 -0400
Labels:           version=v1
                  zone=prod
Annotations:      <none>
Status:           Running
IP:               10.42.0.9
...
```
Here the public-node-ip is :172.31.0.2


<node-port> can be get using the command `kubectl get services svc-sidecar`

```
(base) ➜ kubectl get services svc-sidecar
NAME          TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
svc-sidecar   LoadBalancer   10.43.61.33   <pending>     80:30943/TCP   31m
```

Here the node-port is : 30943

Then in your terminal use the following command to access the app in that Pod:

`curl http://172.31.0.2:30943`
