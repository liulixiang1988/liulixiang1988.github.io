---
title: Craft your Apps for Kubernetes
date: 2020-07-12 23:15
category: Kubernetes
tags: 
- Kubernetes
- Minikube
author: 刘理想
---

## Use Service's Discovery

Say you have a service `mynode` in `yourspace` and a service `myapp` in `myspace`. If the `myapp` wants to access the `mynode` servcie, the url is:

```
mynode.yourspace.svc.cluster.local:8000 # 8000 is the service port, not the node port.
```

## Configure Liveness and Readiness Probes

## Deploy Blue/Green

## Store data with PersistentVolume and PersistentVolumeClaim