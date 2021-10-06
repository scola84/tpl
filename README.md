# The Scola Template

## Kubernetes

### Prerequisites

Setup a DigitalOcean account with https://cloud.digitalocean.com/registrations/new.

Install the DigitalOcean command line interface (doctl) with https://docs.digitalocean.com/reference/doctl/how-to/install/.

Install the Kubernetes command line interface (kubectl) with https://kubernetes.io/docs/tasks/tools/.

Install the Kubernetes package manager (helm) with https://helm.sh/docs/intro/install/.

If necessary switch to the correct DigitalOcean account with https://docs.digitalocean.com/reference/doctl/reference/auth/switch/.

### Create a cluster

Create a new cluster with https://docs.digitalocean.com/reference/doctl/reference/kubernetes/cluster/create/.

### Install NGINX Ingress (load balancer)

Install the NGINX Ingress Controller with https://kubernetes.github.io/ingress-nginx/deploy/#using-helm.

Install the certificate manager with https://cert-manager.io/docs/installation/helm/.

Update the ConfigMap of the controller with

```
kubectl apply --filename .kubernetes/ingress/configmap/ingress-nginx-controller.yaml
```

Create an Issuer with

```
kubectl apply --filename .kubernetes/ingress/issuer/letsencrypt.yaml
```

### Install NGINX (client)

Create a ConfigMap with

```
kubectl create configmap nginx --from-file .kubernetes/nginx/nginx.conf
```

Create a Deployment, Service and Ingress with

```
kubectl apply \
  --filename .kubernetes/nginx/deployment/app-client.yaml \
  --filename .kubernetes/nginx/service/app-client.yaml \
  --filename .kubernetes/nginx/ingress/app-root.yaml
```

### Install Node.js (server)

Create a ConfigMap with

```
kubectl create configmap node --from-file .kubernetes/node/node.js
```

Create a Deployment, Service and Ingress with

```
kubectl apply \
  --filename .kubernetes/node/deployment/app-server.yaml \
  --filename .kubernetes/node/service/app-server.yaml \
  --filename .kubernetes/node/ingress/app-api.yaml
```

### Modify DNS

Find the external IP of the cluster with

```
kubectl get services ingress-nginx-controller
```

and add an A-record with the hostname and the external IP to the DNS.

Visit the site with

```
curl https://<hostname>
```

### Delete cluster

Delete the cluster with https://docs.digitalocean.com/reference/doctl/reference/kubernetes/cluster/delete/.

### Additional information

See the following resources for more information:

* [Build & Ship: GitHub Container Registry & Kubernetes](https://itnext.io/build-ship-github-container-registry-kubernetes-aa06029b3f21)
* [Exclude specific hosts from ssl redirect in Kubernetes Nginx Ingress](https://stackoverflow.com/a/54370053)
