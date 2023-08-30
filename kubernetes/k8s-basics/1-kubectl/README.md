# Kubernetes Basics - Kubectl

### This lab will cover the following topics:

* Kubernetes CLI (kubectl)
* Kubectl Kubeconfig
* K3d

<br/>
<br/>

---
---

## Lab Environment Setup

Before starting the Kubernetes labs, it is important to ensure the required ports are available on the lab server.

Run the following command to release ports 80/443 so to allow Kubernetes ingress on those ports.

`sudo systemctl stop haproxy` {{ execute }}

<br/>
<br/>

---
---

## Lab 1 - Local Kubernetes Cluster Using K3d

AlphaBravo Kubernetes labs make use of K3d, which is Rancher K3s running in Docker. It will appear as if there are multiple Kubernetes control-plane and worker nodes, and the environment will perform as a standard K3s Kubernetes cluster. However, the entire Kubernetes cluster is running in Docker containers on this lab server.

Cool, huh?

<br/>

---

<br/>

### Useful Links

* [K3s](https://rancher.com/docs/k3s/latest/en/)
* [K3d](https://k3d.io/)
* [Kubectl Documentation](https://kubernetes.io/docs/reference/kubectl/overview/)
* [Official Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
* [Kubectl Kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)
<!-- * [AlphaBravo Kubernetes Cheat Sheet](https://gitlab.com/alphabravocompany/ab-cheat-sheets/-/blob/master/kubernetes-cheat-sheet.md) -->

<br/>

---

<br/>

### Bring the Kubernetes Lab Cluster Online

Run the following command to bring the Kubernetes cluster online:

`sudo mkdir -p /ab/k3svol && k3d cluster create lab-cluster --volume /ab/k3dvol:/var/lib/rancher/k3s/storage@all --api-port 16443 --servers 1 --agents 3 -p 80:80@loadbalancer -p 443:443@loadbalancer -p "30000-30010:30000-30010@server:0"` {{ execute }}

#### What's Happening Here?
The above command is performing the following actions:
1. Creating a k3s volume directory.
2. Launching a K3s cluster using K3d using the volume directory
3. Setting the Kubernetes API port to 16443.
4. Defining (1) Kubernetes control-plane node.
5. Defining (3) Kubernetes worker nodes.
6. Exposing ports 80/443 for ingress to the cluster.
7. Exposing ports 30000-30010 for NodePort services.

<br/>

---

<br/>

### Set the Cluster Context

Once the cluster create command completes, the `kubectl` context needs to be switched to the new cluster. The following command will switch to the new cluster's context:

`kubectl config use-context k3d-lab-cluster` {{ execute }}

> **NOTE:**<br/>A Kubernetes context is like a user profile on a computer. It stores the settings for connecting to a specific Kubernetes cluster. Just like you might switch between user profiles on a computer, you can switch between contexts to interact with different Kubernetes clusters.

<br/>

---

<br/>

### Verify the Cluster is Online

Verify the cluster is running and accessible by running the cluster-info command:<br/>
`kubectl cluster-info` {{ execute }}

Another way to verify the cluster is running is to view the nodes in the cluster:<br/>
`kubectl get nodes -o wide` {{ execute }}

<br/>

---

<br/>

### Export the Cluster's Kubeconfig

To export the Kubeconfig file, run the following command:

`mkdir /ab/kubeconfig && k3d kubeconfig get lab-cluster > /ab/kubeconfig/lab-cluster-config.yml && sed -i 's/0.0.0.0/LABSERVERNAME/' /ab/kubeconfig/lab-cluster-config.yml` {{ execute }}

> **NOTE:**<br/>The lab-server cluster's Kubeconfig file can be exported to a file for use with a [tool like K9s](https://k9scli.io/). The AlphaBravo engineering team regularly uses K9s to interact with Kubernetes clusters.

#### What's Happening Here?
The above command is performing the following actions:
1. Create a kubeconfig directory under the `/ab` directory.
2. Exporting the Kubeconfig file for the lab-cluster via K3d.
3. Replacing the IP address in the Kubeconfig file with the hostname of the lab server.

<br/>

---

<br/>

### View the Kubeconfig File

Review the Kubeconfig file created in the previous step:

`/ab/kubeconfig/lab-cluster-config.yml` {{ open }}

<br/>
<br/>

---
---

## Lab 2 - Exploring kubectl Commands

Now that we have a cluster running, let's explore the cluster with kubectl commands.

<br/>

---

<br/>

### Useful Links

* [Kubectl Documentation](https://kubernetes.io/docs/reference/kubectl/overview/)
* [Official Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
<!-- * [AlphaBravo Kubernetes Cheat Sheet](https://gitlab.com/alphabravocompany/ab-cheat-sheets/-/blob/master/kubernetes-cheat-sheet.md) -->

<br/>

---

<br/>

### View All API Resources
API resources are the objects that can be created, updated, and deleted in a Kubernetes cluster. To view all API resources, run the following command:

`kubectl api-resources` {{ execute }}

<br/>

---

<br/>

### View All Kubernetes Namespaces

A namespace is a way to divide cluster resources between multiple users. To view all namespaces in the cluster, run the following command:

`kubectl get ns` {{ execute }}

> **NOTE:**<br/>The `kube-system` namespace is where Kubernetes stores its internal resources. It is not recommended to create resources in this namespace.

<br/>

---

<br/>

### View Running Pods

A Pod is the smallest unit of work in Kubernetes. It is a group of one or more containers that share storage, network, and other resources.

View all running Pods in the cluster:<br/>
`kubectl get pods -A` {{ execute }}

View Pods running a specific namespace, such as `kube-system`:<br/>
`kubectl get pods -n kube-system` {{ execute }}

View Pods running in all namespaces in the cluster:<br/>
`kubectl get pods --all-namespaces -o wide` {{ execute }}

<br/>

---

<br/>

### View Deployments

A Deployment is a way to declaratively define a Pod or group of Pods. It is a higher-level abstraction than a Pod.

View all Deployments in the cluster:<br/>
`kubectl get deploy --all-namespaces -o wide` {{ execute }}

View all Deployments in a specific namespace, such as `kube-system`:<br/>
`kubectl get deploy -n kube-system` {{ execute }}

<br/>

---

<br/>

### View Services

A Service is a way to expose a Pod or group of Pods to the network. It is a higher-level abstraction than a Pod or Deployment.

View all Services in the cluster:<br/>
`kubectl get service --all-namespaces -o wide` {{ execute }}

View all Services in a specific Namespace, such as `kube-system`:<br/>
`kubectl get service -n kube-system` {{ execute }}

> **NOTE:**<br/>Feel free to refer to the links at the top of this Lab for more commands you can run to get information about the cluster.

<br/>
<br/>

---
---

**Congrats! You have completed the Kubectl labs. You may now proceed with the rest of the course.**