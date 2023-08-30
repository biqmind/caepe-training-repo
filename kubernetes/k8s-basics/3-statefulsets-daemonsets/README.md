# Kubernetes Basics - StatefulSets and DaemonSets

### This lab will cover the following topics:

* StatefulSets
* DaemonSets

<br/>
<br/>

---
---

## Lab 1 - StatefulSets

A StatefulSet is the workload resource used to manage stateful applications. It manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods.

Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods. These Pods are created from the same spec, but are not interchangeable: each has a persistent identifier that is maintained across any rescheduling.

<br/>

---

<br/>

### Useful Links

* [Kubernetes StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

<br/>

---

<br/>


### Change Directories

Switch to the directory where the `nginx-statefulset.yml` file is located:<br/>
`cd /ab/labs/kubernetes/k8s-basics/3-statefulsets-daemonsets/` {{ execute }}

<br/>

---

<br/>

### Deploy a StatefulSet

Deploy the StatefulSet manifest:<br/>
`kubectl apply -f nginx-statefulset.yml -n training-lab` {{ execute }}

<br/>

---

<br/>

### Verify the StatefulSet Pods

View the Pods that were created via the StatefulSet manifest. Notice that the StatefulSet automatically created 3 Pods and distributed them across the nodes for fault tolerance:<br/>
`kubectl get pods -n training-lab -o wide` {{ execute }}

> **NOTE:**<br/>Due to the behavior of StatefulSets, it may take a few moments for the Pods to be created.

> **NOTE:**<br/>As the Pods are created, each Pod is assigned a unique name. The Pods are generated in an ordinal fashion (web-0, web-1, web-2). This is ideal for services, such as database clusters, which rely on specific names for each node.

<br/>

---

<br/>

### Review the StatefulSet Manifest

Open the file `nginx-statefulset.yml`:<br/>
`/ab/labs/kubernetes/k8s-basics/3-statefulsets-daemonsets/nginx-statefulset.yml` {{ open }}

<br/>

---

<br/>

### Modify the Manifest

Update the image in the manifest, replacing `nginx:1.20` with `nginx:1.21`. Once complete, save the manifest and apply it again:<br/>
`kubectl apply -f nginx-statefulset.yml -n training-lab` {{ execute }}

<br/>

---

<br/>

### Verify the StatefulSet Pods

Observe the creation of the new Pods:<br/>
`kubectl get pods -n training-lab -o wide` {{ execute }}

> **NOTE:**<br/>The StatefulSet Pods are now in process of being terminated and replaced with the updated image. However, unlike a Deployment and/or ReplicaSet, the name of the Pods will not change. The StatefulSet maintains a sticky identity for each of the Pods, as the Pod names are not interchangeable.

<br/>

---

<br/>

### Review the Persistent Volume Claims

With StatefulSets, `PersistentVolumes` have ordinal names as well.

Observe the `PersistentVolumeClaim` in the `training-lab` Namespace:<br/>
`kubectl get pvc -n training-lab` {{ execute }}

> **NOTE:**<br/>Persistent volumes are covered in greater detail in a later section of the labs.

<br/>

---

<br/>

### Cleanup

Cleanup the StatefulSet:<br/>
`kubectl delete -f nginx-statefulset.yml -n training-lab` {{ execute }}

<br/>

---

<br/>

Verify the training-lab Namespace has no resources deployed:<br/>
`kubectl get all -n training-lab` {{ execute }}

<br/>
<br/>

---
---

## Lab 2 - DaemonSets

A DaemonSet ensures that all (or some) Nodes run a single copy of a Pod. As nodes are added to the cluster, Pods are automatically scheduled to the new nodes. As nodes are removed from the cluster, the Pods assigned to the deleted Node are garbage collected. Deleting a DaemonSet will clean up all Pods it created.

Some typical uses of a DaemonSet are:

* Running a cluster storage daemon on every node
* Running a logs collection daemon on every node
* Running a node monitoring daemon on every node

<br/>

---

<br/>

### Useful Links

* [Kubernetes DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

<br/>

---

<br/>

### Watch the DaemonSet Pods

Open a new `terminal` window on the right and watch the Pods being created:<br/>
`watch kubectl get pods -n training-lab -o wide` {{ execute }}

<br/>

---

<br/>

### Deploy a DaemonSet

In this lab, the DaemonSet will deploy a fluentd Pod on each node. The fluentd Pod will collect logs from the other Pods on the node and send them to a centralized logging service.

Deploy `fluentd-daemonset.yml`:<br/>
`kubectl apply -f fluentd-daemonset.yml -n training-lab` {{ execute }}

<br/>

---

<br/>

### Verify the DaemonSet Pods

The DaemonSet automatically created 4 Pods and distributed them across the nodes for fault tolerance. Review the Pods created by the DaemonSet:<br/>
`kubectl get pods -n training-lab -o wide` {{ execute }}

> **NOTE:**<br/>The DaemonSet deployed 4 Pods. The cluster has is 1 server and 3 worker nodes, so the DaemonSet deployed a Pod for each node. Unlike a Deployment or StatefulSet, each node will have a Pod deployed.

<br/>

---

<br/>

### Add a Node

To show the dynamic nature of DaemonSets, add another node to the cluster existing K3d cluster:
`k3d node create lab-cluster-agent-3 --cluster lab-cluster` {{ execute }}

> **NOTE:**<br/>It will take a moment to add the agent and create the associated Pod. Once the node is online, the Pod count will increase from 4 to 5. Kubernetes automatically scaled the DaemonSet and deployed a fluentd Pod on the new worker node.

<br/>

---

<br/>

### Cleanup

Close the watch command in the new terminal window by pressing `CTRL+C`.<br/>
Exit the terminal window by typing `exit` and pressing `ENTER`.

Delete the DaemonSet:<br/>
`kubectl delete -f fluentd-daemonset.yml` {{ execute }}

Verify the training-lab Namespace has no resources deployed:<br/>
`kubectl get all -n training-lab` {{ execute }}

<br/>
<br/>

---
---

**Congrats! You have completed the StatefulSets and DaemonSets labs. You may now proceed with the rest of the course.**