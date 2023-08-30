# Kubernetes Basics - Namespaces and Deployments

### This lab will cover the following topics:

* Kubernetes Namespaces
* Kubernetes Pods
* Kubernetes Deployment
* Kubernetes ReplicaSet

<br/>
<br/>

---
---

## Lab 1 - Creating Namespaces and a Pod

In Kubernetes, Namespaces provides a mechanism for isolating groups of resources within a single cluster. Names of resources need to be unique within a Namespace, but not across Namespaces.

In this lab, we will create a Namespace and create a Pod in that Namespace. All of the resources for the Kubernetes Basics labs will be deployed using this Namespace.

<br/>

---

<br/>

### Useful Links

* [Kubernetes Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
* [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
* [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get)
* [kubectl run](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run)
* [kubectl delete](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete)

<br/>

---

<br/>

### Create a Namespace

First, create a Namespace called `training-lab`:<br/>
`kubectl create namespace training-lab` {{ execute }}

> **NOTE:**<br/>Most Kubernetes resources have a short-hand version of the resource name. For example, `kubectl create namespace` is the same as `kubectl create ns`.

<br/>

---

<br/>

### Verify the Namespace

Next, verify the Namespace was created:<br/>
`kubectl get ns` {{ execute }}

> **NOTE:**<br/>Namespaces are a cluster-wide resource. When running the `kubectl get ns` command, you will see all Namespaces in the cluster.

<br/>

---

<br/>

### Create a Pod in the Namespace

Now create a Pod in the `training-lab` Namespace:<br/>
`kubectl run nginx --image=nginx --restart=Never --namespace=training-lab` {{ execute }}

> **NOTE:**<br/>A short-hand version of the Namespace flag is `-n`. For example, `--namespace=training-lab` is the same as `-n training-lab`.

<br/>

---

<br/>

### Verify the Pod is Running

Next, verify the Pod was created and note what `Node` it is running on:<br/>
`kubectl get pods -n training-lab -o wide` {{ execute }}

> **NOTE:**<br/>The `-o wide` flag will show additional information about the Pod, including the `Node` it is running on.

<br/>

---

<br/>

### Delete the Pod

Next, delete the pod from the Namespace:<br/>
`kubectl delete pod nginx -n training-lab` {{ execute }}

<br/>

---

<br/>

### Verify the Pod is Deleted

Finally, verify the pod was deleted:<br/>
`kubectl get pods -n training-lab -o wide` {{ execute }}

<br/>
<br/>

---
---

## Lab 2 - Creating ReplicaSets and Deployments

The creation of single pods is generally done for troubleshooting or testing, and generally is not designed to scale. Using a ReplicaSet and/or Deployment is preferred when creating pods as they are designed to scale and automatically maintain a desired number of pods.

In this lab, a Deployment will be created. As part of the Deployment, a ReplicaSet and 3 pods will be created.

<br/>

---

<br/>

### Useful Links

* [Kubernetes ReplicaSets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
* [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

<br/>

---

<br/>

### Change Directories

In the terminal, switch to the directory where the `nginx-deployment.yml` file is located:<br/>
`cd /ab/labs/kubernetes/k8s-basics/2-namespaces-deployments/` {{ execute }}

Verify the current working directory:<br/>
`pwd` {{ execute }}

<br/>

---

<br/>

### Review the Deployment Manifest File

In VS Code, open the `nginx-deployment.yml` file and review the structure.<br/>
`/ab/labs/kubernetes/k8s-basics/2-namespaces-deployments/nginx-deployment.yml` {{ open }}

<br/>

---

<br/>

### Apply the Manifest File

Once the file is reviewed, deploy `nginx-deployment.yml`:<br/>
`kubectl apply -f nginx-deployment.yml` {{ execute }}

> **NOTE:**<br/>The `apply` command will create or update a resource. If the resource already exists, it will be updated. If the resource does not exist, it will be created.

<br/>

---

<br/>

### Review the Deployed Pods

Review the pods that were created. Notice that 3 pods were created and distributed across  worker nodes for fault tolerance:<br/>
`kubectl get pods -n training-lab -o wide` {{ execute }}

<br/>

---

<br/>

### Review the ReplicaSets

The ReplicaSets are responsible for maintaining the desired number of pods. Review the ReplicaSets that were created via the Deployment:<br/>

`kubectl get rs -n training-lab` {{ execute }}

> **NOTE:**<br/>It is rare to create a ReplicaSet directly. ReplicaSets should generally be created as part of a Deployment.

<br/>

---

<br/>

### Split the Terminal

Let's open a second Terminal in vscode so we can watch the pods. Click the “Split Terminal” button in the upper right of the VS Code `Terminal` window or press `Control+Shift+5`. You should now have 2 windows.

Click on the right terminal window and run:<br/>
`watch -n1 kubectl get pods -n training-lab` {{ execute }}

> **NOTE:**<br/>The watch command will run the specified command every second. This is useful for monitoring changes to resources. It is possible to increase or decrease the interval by changing the `-n1` flag, where `1` is the number of seconds.

<br/>

---

<br/>

### Manual Pod Scaling

Now that the watch is running, manually scale the deployment to 5 pods. In the left terminal, run the following command to scale the Deployment:<br/>
`kubectl scale --replicas=5 -f nginx-deployment.yml` {{ execute }}

Watch the output in the right terminal window observe additional pods being created.

> **NOTE:**<br/>It is possible to scale to zero (0) replicas. This is useful when you want to temporarily stop a service.

> **NOTE:**<br/>Pods are ephemeral. When scaling a Deployment down and back up, the existing pods will be terminated and new pods will be created.

<br/>

---

<br/>

### Automatic Pod Reconciliation

What happens if a Deployment managed Pod is deleted manually? Run the following command in the left terminal to see how Kubernetes deals with this:<br/>
`kubectl delete pod $(kubectl get pod -n training-lab --no-headers | awk 'NR==1{print $1}') -n training-lab` {{ execute }}

Watch the output in the right terminal window and observe the deleted pod being replaced. As the Deployment has been modified to scale to 5 pods, Kubernetes will **automatically reconcile** the desired state of 5 pods and create a new pod to replace the one that was deleted.

<br/>

---

<br/>

### Reapply the Manifest File

What happens if the Deployment is re-applied? Run the following command in the left terminal to see how Kubernetes deals with this:<br/>
`kubectl apply -f nginx-deployment.yml` {{ execute }}

Watch the output in the right terminal window and observe Pods being terminated. As the Deployment manifest specifies 3 replicas, Kubernetes will reconcile the desired state of 3 pods and terminate the 2 additional pods.

> **NOTE:**<br/>Changes to Kubernetes objects should be made declaratively via manifest files. If changes are made via the CLI, they can be overwritten when the manifest file is re-applied.

<br/>

---

<br/>

### View the Current Deployment Image Version

It is possible to view the current image version being used by the Deployment by describing a Pod in the Deployment or the Deployment itself:<br/>

Pod Example:<br/>
`kubectl get pod <podname> -n training-lab -o=jsonpath='{.spec.containers[].image}'` {{ execute }}

Deployment Example:<br/>
`kubectl get deployment nginx-deployment -n training-lab -o=jsonpath='{.spec.template.spec.containers[*].image}'` {{ execute }}

#### What's Happening Here?
1. Using the Pod command as the example, the `kubectl describe pod` command is being used to describe a Pod in the `training-lab` Namespace.
2. The manifest for the Pod contains a specification, followed by a containers section, followed by an image section.
3. The `-o=jsonpath='{.spec.containers[*].image}'` flag is being used to query the manifest and output the image name of the Pod.
4. In the manifest, the container image is `nginx:1.14.2`.

<br/>

---

<br/>

### Change the Manifest File

It is possible to make changes to the manifest file, such as the NGINX image version, and re-apply the manifest. Upon submission, Kubernetes will delete the existing pods and replace them with the image version specified.

Update the nginx-deployments.yml file on line 20 so that the image is `nginx:1.20.1`.<br/>
`/ab/labs/kubernetes/k8s-basics/2-namespaces-deployments/nginx-deployment.yml` {{ open }}

<br/>

---

<br/>

### Re-apply the Manifest File

Save and re-apply the manifest file:<br/>
`kubectl apply -f nginx-deployment.yml` {{ execute }}

In the right terminal window, observe the existing Pods being terminated and replaced with pods running the new specified container image.

<br/>

---

<br/>

### Verify the New Image Version

Run the following command against one of the new pod names:<br/>
`kubectl get pod <podname> -n training-lab -o=jsonpath='{.spec.containers[].image}'` {{ execute }}

<br/>

---

<br/>

### Review Updated ReplicaSets

Deployment changes will also create new ReplicaSets. In the example, there are now 2 ReplicaSets. Run the following command to verify:<br/>
`kubectl get rs -n training-lab` {{ execute }}

> **NOTE:**<br/>Deployments have a built-in rollback feature, which make use of the deployed ReplicaSets. If a change is made to a Deployment that causes issues, it is possible to rollback to the previous ReplicaSet. See the [Kubernetes Deployment Rollback Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment) for more information.

<br/>

---

<br/>

### Observe All Resources in the Namespace

In the right window, hit `Ctrl+C` to stop the `watch` command. Run the following command to observe all resources in the `training-lab` Namespace:<br/>
`watch kubectl get all -n training-lab` {{ execute }}

As the lab is cleaned up, observing this command will show all resources being deleted.

<br/>

---

<br/>

### Cleanup

In the left terminal window, delete the Deployment. This will also delete the ReplicaSets and Pods:<br/>
`kubectl delete -f nginx-deployment.yml` {{ execute }}

In the right terminal window, observe the resources being deleted.

<br/>

---

<br/>

### Close the Extra Terminal Window

Finally, let's close the extra terminal window.  In the right terminal, press `Ctrl+C` to stop the `watch` command. Then, type `exit` and press `Enter` to close the extra terminal window.

<br/>
<br/>

---
---

**Congrats! You have completed the Namespaces and Deployments labs. You may now proceed with the rest of the course.**