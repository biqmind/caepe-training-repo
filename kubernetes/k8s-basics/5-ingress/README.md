# Kubernetes Basics - Ingress

### This lab will cover the following topics:

* Kubernetes Ingress
<!-- * Kubernetes IngressController -->

<br/>
<br/>

---
---

## Lab 1 - Ingress

In Kubernetes, Ingress is an API object which exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource. Ingress supports TLS termination, name-based virtual hosting, and path-based routing.

<br/>

---

<br/>

### Useful Links

* [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)
* [Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)

<br/>

---

<br/>

### Change the Working Directory

Change to the Ingress directory:<br/>
`cd /ab/labs/kubernetes/k8s-basics/5-ingress/` {{ execute }}

<br/>

---

<br/>

### Create a Web Server Deployment

First, deploy a basic NGINX web server Deployment:<br/>
`kubectl apply -f nginx-deployment.yml` {{ execute }}

> **NOTE:**<br/>This lab requires a Deployment to be deployed in order to demonstrate Ingress, otherwise there would be no Pods to route traffic to.

<br/>

---

<br/>

Next, create a ClusterIP Service to expose the NGINX Deployment:<br/>
`kubectl apply -f nginx-service.yml` {{ execute }}

> **NOTE:**<br/>A ClusterIP Service is required for Ingress to function. The Ingress will route traffic to the ClusterIP Service, which will then route traffic to the Pods deployed in the previous step.

<br/>

---

<br/>

### Review the Ingress Manifest

Open and review the Ingress manifest:<br/>
`/ab/labs/kubernetes/k8s-basics/5-ingress/ingress.yml` {{ open }}

> **NOTE:**<br/>This Ingress is an example showing how non-secure traffic can be routed to a Web server. A secure Ingress will be deployed later in this lab.

<br/>

---

<br/>

### Apply the Ingress

Apply the Ingress manifest:<br/>
`kubectl apply -f ingress.yml` {{ execute }}

<br/>

---

<br/>

### Verify the Ingress

Verify the Ingress is deployed:<br/>
`kubectl get ingress -n training-lab` {{ execute }}

<br/>

---

<br/>

### Visit the Web Server URL

Now the Ingress is deployed, it is possible to view the web server via the Ingress URL:<br/>
http://nginx.LABSERVERNAME

> **NOTE:**<br/>Ingress understands the concept of domain names and service routing. It translates an incoming request from a valid domain and passes the inbound traffic to the proper Service. The Service, in turn, forwards the inbound traffic to one or more Pods.

<br/>

---

<br/>

### Review the TLS Ingress Manifest

Open and review the TLS Ingress manifest:<br/>
`/ab/labs/kubernetes/k8s-basics/5-ingress/tls-ingress.yml` {{ open }}

> **NOTE:**<br/>Ingress supports TLS termination, which allows for encrypted traffic to be routed to the Service. This is useful for applications that require encryption on inbound traffic, such as web applications.

> **NOTE:**<br/>Ingress reads the TLS certificate from a Kubernetes Secret. This allows for the TLS certificate to be stored in a secure location, and for the TLS certificate to be rotated without having to modify the Ingress manifest.

<br/>

---

<br/>

### Create a TLS Secret

Create a Secret in the `training-lab` Namespace:<br/>
`kubectl -n training-lab create secret tls traefik-ui-tls-cert --key=/ab/certs/live/LABSERVERNAME/privkey.pem --cert=/ab/certs/live/LABSERVERNAME/fullchain.pem` {{ execute }}


#### What's Happening Here?
1. In the `training-lab` Namespace, create a TLS Secret named `traefik-ui-tls-cert`.
2. Reference the pre-generated TLS certificate and private key from the Lab server.

> **NOTE:**<br/>Secrets will be covered in greater detail in a later section of this lab.

<br/>

---

<br/>

### Apply the TLS Ingress

Apply the TLS Ingress manifest:<br/>
`kubectl apply -f tls-ingress.yml` {{ execute }}

<br/>

---

<br/>

### Verify the TLS Ingress

Verify the TLS Ingress is deployed:<br/>
`kubectl get ingress -n training-lab` {{ execute }}

<br/>

---

<br/>

### Visit the Secure Web Server URL

Now the secure Ingress is deployed, it is possible to view the web server via the TLS encrypted Ingress URL:<br/>
https://tls-nginx.LABSERVERNAME

> **NOTE:**<br/>Using the TLS certificate with Ingress allows a secure external connection to the application without having to modify the application itself. This is useful for legacy applications that do not support TLS encryption.

> **NOTE:**<br/>Adding TLS to the Ingress does not secure traffic *inside* the cluster. This requires network policies and service mesh (like Istio), which are not covered in this lab.

<br/>

---

<br/>

### Cleanup

Delete the NGINX Deployment:<br/>
`kubectl delete -f nginx-deployment.yml` {{ execute }}

Delete the NGINX Service:<br/>
`kubectl delete -f nginx-service.yml` {{ execute }}

> **NOTE:**<br/>The Ingress examples will be used in the next lab, so they will not be deleted.

<br/>
<br/>

---
---

**Congrats! You have completed the Ingress labs. You may now proceed with the rest of the course.**