# Kubernetes Basics - Configmaps and Secrets

### This lab will cover the following topics:

* Kubernetes ConfigMaps
* Kubernetes Secrets

<br/>
<br/>

---
---

## Lab 1 - ConfigMaps

A ConfigMap is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume. A ConfigMap allows you to decouple environment-specific configuration from your container images, so that your applications are easily portable.

<br/>

---

<br/>

### Useful Links

* [Kubernetes ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)

<br/>

---

<br/>

### Change the Working Directory

Change to the ConfigMaps & Secrets directory:<br/>
`cd /ab/labs/kubernetes/k8s-basics/6-configmaps-secrets` {{ execute }}

<br/>

---

<br/>

### Create a ConfigMap

Create a ConfigMap called `lab-html` in the `training-lab` Namespace, using a basic HTML file:<br/>
`cp index-dev.html index.html && kubectl create configmap lab-html -n training-lab --from-file index.html -o yaml --dry-run=client | kubectl apply -f -` {{ execute }}

#### What's Happening Here?
1. Copying the file index-dev.html to index.html.
2. Running `kubectl` with the `dry-run` flag to dynamically create a ConfigMap manifest called `lab-html` using the `index.html` file.
3. Using `-o yaml` to output the dynamically generated YAML manifest to the command line.
4. Piping the dynamic YAML into the `kubectl apply` command to create the ConfigMap.
5. The hyphen (`-`) at the end of the command is used to read the YAML from STDIN. The `kubectl -f` flag can read from a file, URL, or STDIN.

<br/>

---

<br/>

### View the ConfigMap

View the ConfigMap that was created:<br/>
`kubectl get configmap -n training-lab lab-html -o yaml` {{ execute }}

<br/>

---

<br/>

### Deploy a Basic Web Server

Deploy the NGINX web server Deployment, along with the ClusterIP Service and Ingress:<br/>
`kubectl apply -f nginx-deployment.yml -f nginx-service.yml -f tls-ingress.yml` {{ execute }}

<br/>

---

<br/>

### Verify the Web Server

Now that the Deployment is running, visit the web server:<br/>
https://tls-nginx.LABSERVERNAME

> **NOTE:**<br/>You should see a `yellow` page with the text "Hello from the Dev Environment!". The web browser may cache the previous content. If the previous content is still displayed, try refreshing the browser window.

<br/>

---

<br/>

### Update the ConfigMap

It is possible to update a ConfigMap to change its stored data. This is useful when the configuration of a running application needs to be updated.

Update the ConfigMap to use the `index-prod.html` file:<br/>
`cp index-prod.html index.html && kubectl create configmap lab-html -n training-lab --from-file index.html -o yaml --dry-run=client | kubectl replace -f -` {{ execute }}


#### What's Happening Here?
1. Copying the index-prod.html file to index.html.
2. Running `kubectl` with the `dry-run` flag to dynamically create a ConfigMap manifest called `lab-html` using the `index.html` file.
3. Using `-o yaml` to output the dynamically generated YAML manifest to the command line.
4. Piping the dynamic YAML into the `kubectl replace` command to replace the existing `lab-html` ConfigMap.

<br/>

---

<br/>

### Verify the Updated ConfigMap

View the ConfigMap that was updated:<br/>
`kubectl get configmap -n training-lab lab-html -o yaml` {{ execute }}

<br/>

---

<br/>

### Delete the Existing Pods

Scale the Deployment to zero and back to two Pods:<br/>
`kubectl scale deployment lab-nginx --replicas=0 -n training-lab && kubectl scale deployment lab-nginx --replicas=2 -n training-lab` {{ execute }}

> **NOTE:**<br/>As Pods are ephemeral, it is required to delete and recreate the Pod to apply the updated ConfigMap. To do this, the Deployment is scaled to zero (0), which will terminate the existing Pods. Afterwards, the Deployment is scaled back to two (2), which will create two new Pods using the updated ConfigMap.

> **NOTE:**<br/>When scaling a Deployment, Pods may take a few moments to terminate and recreate. Please be patient while this process completes.

<br/>

---

<br/>


### Verify the Updated Web Server

Now visit the web server again and verify the updated content "Hello from the Prod Environment":<br/>
https://tls-nginx.LABSERVERNAME

> **NOTE:**<br/>The web browser may cache the previous content. If the previous content is still displayed, try refreshing the browser window.

<br/>
<br/>

---
---

## Lab 2 - Secrets

A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Such information might otherwise be put in a Pod specification or in a container image. Using a Secret means that you don't need to include confidential data in your application code.

<br/>

---

<br/>

### Useful Links

* [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)

<br/>

---

<br/>

### View the Auth File

Open the auth file and observe the contents:<br/>
`/ab/labs/kubernetes/k8s-basics/6-configmaps-secrets/auth` {{ open }}

> **NOTE:**<br/>This file contains a username and password. This is a basic auth file, which is used to authenticate to the web server. The username is plain text, while the password is a HASH of the username and password.

<br/>

---

<br/>

### Create a Generic Secret

Create a generic secret called `lab-secret` in the `training-lab` Namespace, using a basic auth file:<br/>
`kubectl create secret generic lab-secret --from-file auth -n training-lab` {{ execute }}

#### What's Happening Here?
1. Create a Secret using the `generic` type named `lab-secret` in the `training-lab` Namespace.
2. Use the file called `auth` as the source of the Secret.

<br/>

---

<br/>

### View the Secret

View the Secret that was created:<br/>
`kubectl get secret -n training-lab lab-secret -o yaml` {{ execute }}

> **NOTE:**<br/>Secrets are Base64 encoded. Encoded is NOT the same as being encrypted. It is possible to decode a Secret using the `base64` command.

<br/>

---

<br/>

### Decode the Secret

Decode the Secret using Base64:<br/>
`kubectl get secret -n training-lab lab-secret -o jsonpath="{.data.auth}" | base64 --decode` {{ execute }}

#### What's Happening Here?
1. Using `kubectl` to get the Secret named `lab-secret` in the `training-lab` Namespace.
2. Using `-o jsonpath` to output the `auth` key stored in the Secret data field.
3. Piping the output into the `base64` command to decode the Base64 encoded Secret.

> **NOTE:**<br/>It is possible to store one or more Secrets in an external key management system, such as HashiCorp Vault. This allows for the storage of sensitive data outside of the Kubernetes cluster.

<br/>

---

<br/>

### Deploy a Basic Web Server

Deploy an NGINX web server Pod:<br/>
`kubectl apply -f nginx-pod.yml` {{ execute }}

<br/>

---

<br/>

### Access the Pod Shell

Using the `exec` command, access the Pod shell:<br/>
`kubectl exec -it -n training-lab nginx -- /bin/bash` {{ execute }}

> **NOTE:**<br/>This command will open a working shell inside the Pod. This is useful for troubleshooting and debugging.

<br/>

---

<br/>

### View the Unencrypted Secret in the Pod

Once in the Pod, it is possible to view the unencrypted Secret:<br/>
`cat /etc/secrets/auth` {{ execute }}

> **NOTE:**<br/>The above command shows the username unencoded and the password as a HASH of the username and password. Anyone with access to the Pod can view the unencrypted Secret.

<br/>

---

<br/>

### Exit the Pod Shell

Exit out of the container:<br/>
`exit` {{ execute }}

<br/>

---

<br/>

### Create an Ingress with Basic Auth Secret

Create an Ingress with Basic Auth using the existing `lab-secret` Secret:<br/>
`kubectl apply -f tls-auth-ingress.yml` {{ execute }}

> **NOTE:**<br/>This example Ingress utilizes a Custom Resource Definition and annotation supported by Traefik Ingress, preventing the need to mount a secret inside a container.

<br/>

---

<br/>

### Visit the Authenticated Web Server URL

When visiting the URL below, a prompt will appear asking for a username and password. Enter `admin` for the username and `password123` for the password.

https://tls-auth-nginx.LABSERVERNAME

<br/>

---

<br/>

### Cleanup

Run the following command to cleanup the lab environment:<br/>
`kubectl delete -f /ab/labs/kubernetes/k8s-basics/6-configmaps-secrets` {{ execute }}

<br/>
<br/>

---
---

**Congrats! You have completed the Configmaps and Secrets labs. You may now proceed with the rest of the course.**