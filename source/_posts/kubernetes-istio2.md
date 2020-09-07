---
title: Explore the Istio Service Mesh
date: 2020-08-09 17:36
category: Kubernetes
tags: 
- Kubernetes
- Istio
author: Liu Lixiang
---

## 1.Explorer Observability

Open the dashboard:

```bash
#!/bin/bash

export MINIKUBE_IP=$(minikube --profile istio-mk  ip)

kubectl patch service/grafana -p '{"spec":{"type":"NodePort"}}' -n istio-system
open http://$MINIKUBE_IP:$(kubectl get svc grafana -n istio-system -o 'jsonpath={.spec.ports[0].nodePort}')

kubectl patch service/jaeger-query -p '{"spec":{"type":"NodePort"}}' -n istio-system
open http://$MINIKUBE_IP:$(kubectl get svc jaeger-query -n istio-system -o 'jsonpath={.spec.ports[0].nodePort}')

kubectl patch service/prometheus -p '{"spec":{"type":"NodePort"}}' -n istio-system
open http://$MINIKUBE_IP:$(kubectl get svc prometheus -n istio-system -o 'jsonpath={.spec.ports[0].nodePort}')

kubectl patch service/kiali -p '{"spec":{"type":"NodePort"}}' -n istio-system
open http://$MINIKUBE_IP:$(kubectl get svc kiali -n istio-system -o 'jsonpath={.spec.ports[0].nodePort}')/kiali
```

Kiali: default username/password: admin/admin

### 1.1 Grafana

In the grapana. Home->Istio->Istio Workload Dashboard.

![Grapha](/images/istio-5.png)

### 1.2 Jaeger

![Jaeger](/images/istio-6.png)

![Jaeger](/images/istio-7.png)

### 1.3 Prometheus



## 2.Inject Chaos

## 3.Add resiliency

## 4.Add security