---
title: Extend Your Kubernetes
date: 2020-07-19 10:48
category: Kubernetes
tags: 
- Kubernetes
- Istio
author: Liu Lixiang
---
## Understand Microservices architecture requirements and challenges

[resource](/images/Istio-on-Kubernetes.pdf)

- API
- Discovery
- Invocation
- Elasticity
- Resillience
- Pipeline
- Authentication
- Logging
- Monitoring
- Tracing

![before Istio](/images/before-istio.png)


![after Istio](/images/after-istio.png)

The sidecar intercepts all network traffic.

### How to add an Istio-Proxy(sidecar)?

`istioctl kube-inject -f NormalDeployment.yaml`

or

`kubectl label namespace myspace istio-injection=enabled`

To "see" the sidecar:

`kubectl describe deployment customer`

Envoy is the current sidecar.

### Next Generation Microservice - Service Mesh

![Istio](/images/istio-1.png)

![Istio-Gateway](/images/istio-2.png)

![Istio-Gateway](/images/istio-3.png)

![Istio-Resource](/images/istio-4.png)


## Install Istio

https://github.com/redhat-developer-demos/istio-tutorial

https://github.com/burrsutter/scripts-istio

```bash
export MINIKUBE_HOME=/Users/lixiangliu/Projects/k8s/minikube-istio
# this is where the minikube executable is
export PATH=$MINIKUBE_HOME/bin:$PATH
export KUBECONFIG=$MINIKUBE_HOME/.kube/config
export KUBE_EDITOR="code -w"

export ISTIO_HOME=$MINIKUBE_HOME/istio-1.6.0
export PATH=$ISITIO_HOME/bin:$PATH
```

Start:

```bash
minkube start -p instio-mk --memory=8192 --cpus=3 \
 --kubernetes-version=v1.18.3 \
 --vm-driver=virtualbox \
 --disk-size=30g
```

install istio

```bash
curl -L https://github.com/istio/istio/releases/download/1.4.6/istio-1.4.6-osx.tar.gz | tar xz
cd istio-1.4.6
export PATH=$ISTIO_HOME/bin:$PATH
istioctl manifest apply --set profile=demo --set values.global.proxy.privileged=true
kubectl config set-context $(kubectl config current-context) --namespace=istio-system
kubectl get pods -w
```

## Deploy with Istio/Envoy Sidecars
