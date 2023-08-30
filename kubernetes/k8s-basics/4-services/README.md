# Kubernetes Basics - Services

### This lab will cover the following topics:

* Kubernetes Service

<br/>
<br/>

---
---

## Lab 1 - Services

A Service is an abstract way to expose an application running on a set of Pods as a network service.

With Kubernetes, it isn't necessary to modify an application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP address, known as an Endpoint, a DNS name to resolve a collection of Pods, and a means to load balance traffic across those Pods.

The 5 types of Services are:

* ClusterIP
* NodePort
* LoadBalancer
* ExternalName
* Headless

This lab will explore the first 2 types of Services. The LoadBalancer, ExternalName, and Headless Service types require additional resource provisioning which are outside the scope of this lab.

This lab will provision a Deployment with 2 Pods in the cluster which creates a special `whoami` container. This container displays information about itself and the Pod host, and will help to demonstrate Kubernetes Service load balancing. Additionally, the Pods will be scaled to demonstrate how Kubernetes Services automatically load balance traffic to new Pods.

> **NOTE:**<br/>Pods can have multiple Services deployed in front of them. This allows for multiple types of traffic to be routed to the same Pod.

> **NOTE:**<br/>Some Services may stack on top of one another. For example, a LoadBalancer Service relies on a NodePort Service to function, and in turn the NodePort Service requires a ClusterIP Service to function.

<br/>

---

<br/>

### Useful Links

* [Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/)

<br/>

---

<br/>

### Change the Working Directory

Change to the Services directory:<br/>
`cd /ab/labs/kubernetes/k8s-basics/4-services` {{ execute }}

<br/>

---

<br/>

### Create the Deployment

Run the whoami Deployment in the `training-lab` Namespace:<br/>
`kubectl apply -f whoami-deployment.yml` {{ execute }}

<br/>

---

<br/>

### Review the ClusterIP Service Manifest

Open and review the ClusterIP Service manifest:<br/>
`/ab/labs/kubernetes/k8s-basics/4-services/clusterip-service.yml` {{ open }}

<br/>

---

<br/>

### Create the ClusterIP Service

Apply the ClusterIP Service manifest:<br/>
`kubectl apply -f clusterip-service.yml` {{ execute }}

> **NOTE:**<br/>The ClusterIP Service is an internal-only cluster resource. It is not accessible from outside the Kubernets cluster.

> **NOTE:**<br/>Pods do not retain IP addresses on creation/deletion, the ClusterIP Service is used to provide a static endpoint for other Pods to communicate with.

<br/>

---

<br/>

### Verify the Deployment and Service

Review the `training-lab` Namespace to verify 2 `whoami` Pods and a ClusterIP Service are running:<br/>
`kubectl get all -n training-lab` {{ execute }}

<br/>

---

<br/>

### Create Netshoot Pod

The `Netshoot` Pod is a troubleshooting container that can be used to test connectivity to other Pods and Services. This Pod will be used to test connectivity to the `whoami-clusterip-service` Service.

Open a second terminal in split screen next to the first and run the below command to create and connect to the Netshoot Pod:<br/>
`kubectl run netshoot -it --rm --namespace=training-lab --image nicolaka/netshoot -- /bin/bash` {{ execute }}

> **NOTE:**<br/>When you see `netshoot:~#`, you have been dropped into the shell of the Netshoot Pod.

<br/>

---

<br/>

### Query the ClusterIP Service

In the `Netshoot` Pod, query the `whoami-clusterip-service` to see how it divides traffic to our Pods:<br/>
`for ((i=1;i<=10;i++)); do curl -0 -v whoami-clusterip-service 2>&1; done | grep Hostname | awk -F " " {' print $2 '} | sort | uniq -c | column -N "Count,Hostname" -t` {{ execute }}

#### What's Happening Here?
1. In the `Netshoot` Pod, a shell `for` loop is executed 10 times.
2. Each time the loop is executed, the `curl` command is run against the `whoami-clusterip-service`.
3. The output of the `curl` command is piped to `grep` to filter out the `Hostname` line.
4. The output of `grep` is piped to `awk` to split the line on spaces and print the second field.
5. The output of `awk` is piped to `sort` to sort the output.
6. The output of `sort` is piped to `uniq` to remove duplicate lines.
7. The output of `uniq` is piped to `column` to format the output into a table.

> **NOTE:**<br/>Run the command a few times. There should be a roughly 50/50 split between our 2 running Pods.

<br/>

---

<br/>

### Scale the Deployment

Open a new `terminal` in split screen next to the first and run the below command to scale the Deployment to 10 replicas:<br/>
`kubectl scale --replicas=10 -f whoami-deployment.yml` {{ execute }}

### Test with Netshoot Again

Once the Deployment has scaled, run the curl command again in the left terminal to see how the Service automatically load balances traffic to the new Pods:<br/>
`for ((i=1;i<=10;i++)); do curl -0 -v whoami-clusterip-service 2>&1; done | grep Hostname | awk -F " " {' print $2 '} | sort | uniq -c | column -N "Count,Hostname" -t` {{ execute }}

<br/>

---

<br/>

### Cleanup

Exit the right terminal window:<br/>
`exit` {{ execute }}

In the `Netshoot` Pod, exit the shell:<br/>
`exit` {{ execute }}

<br/>

---

<br/>

### Review the NodePort Service Manifest

Open and review the NodePort Service manifest:<br/>
`/ab/labs/kubernetes/k8s-basics/4-services/nodeport-service.yml` {{ open }}

> **NOTE:**<br/>By default, the NodePort Service will automatically select a port in the range 30000-32767, unless the port is manually specified in the manifest file. This example manually specifies port 30007.

<br/>

---

<br/>

### Create the NodePort Service

Apply the NodePort Service manifest:<br/>
`kubectl apply -f nodeport-service.yml` {{ execute }}

> **NOTE:**<br/>This will create a NodePort Service that exposes the `whoami` Pods on the host port 30007.

<br/>

---

<br/>

### Visit the Exposed Pod Web Page

Click the link below to visit the exposed Pod web page:<br/>
http://LABSERVERNAME:30007

> **NOTE:**<br/>Use the browser refresh button to reload the page and see the content change. Re-visiting the URL alone will not work due to the browser cache.

<br/>

---

<br/>

### Cleanup

Run the below command to delete the Deployment and Services:<br/>
`kubectl delete -f /ab/labs/kubernetes/k8s-basics/4-services` {{ execute }}

> **NOTE:**<br/>Running the `apply` or `delete` commands against a directory will apply/delete all manifests in that directory.

<br/>
<br/>

---
---

**Congrats! You have completed the Services labs. You may now proceed with the rest of the course.**