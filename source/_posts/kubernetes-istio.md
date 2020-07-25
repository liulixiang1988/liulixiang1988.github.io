---
title: Extend The Istio Service Mesh
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

export ISTIO_HOME=$MINIKUBE_HOME/istio-1.6.5
export PATH=$ISITIO_HOME/bin:$PATH
cd $MINIKUBE_HOME
```

Start:

```bash
% minikube start -p istio-mk --memory=8192 --cpus=3 \
 --kubernetes-version=v1.18.3 \
 --vm-driver=virtualbox \
 --disk-size=30g
```

install istio

```bash
% curl -L https://github.com/istio/istio/releases/download/1.6.5/istio-1.6.5-osx.tar.gz | tar xz
% cd istio-1.6.5
% export PATH=$ISTIO_HOME/bin:$PATH
% istioctl manifest apply --set profile=demo --set values.global.proxy.privileged=true
% kubectl config set-context $(kubectl config current-context) --namespace=istio-system
% kubectl get pods -w
```

the outputs is :

```
NAME                                    READY   STATUS    RESTARTS   AGE
grafana-b54bb57b9-j6wc8                 1/1     Running   0          9m49s
istio-egressgateway-7486cf8c97-kdffx    1/1     Running   0          9m50s
istio-ingressgateway-6bcb9d7bbf-fdkv5   1/1     Running   0          9m50s
istio-tracing-9dd6c4f7c-wpshx           1/1     Running   0          9m49s
istiod-788f76c8fc-5smwm                 1/1     Running   0          10m
kiali-d45468dc4-p7tb9                   1/1     Running   0          9m49s
prometheus-6477cfb669-stbh8             2/2     Running   0          9m49s
```

```bash
% kubectl get crds
NAME                                       CREATED AT
adapters.config.istio.io                   2020-07-22T23:25:58Z
attributemanifests.config.istio.io         2020-07-22T23:25:58Z
authorizationpolicies.security.istio.io    2020-07-22T23:25:58Z
clusterrbacconfigs.rbac.istio.io           2020-07-22T23:25:58Z
destinationrules.networking.istio.io       2020-07-22T23:25:58Z
envoyfilters.networking.istio.io           2020-07-22T23:25:58Z
gateways.networking.istio.io               2020-07-22T23:25:58Z
handlers.config.istio.io                   2020-07-22T23:25:58Z
httpapispecbindings.config.istio.io        2020-07-22T23:25:58Z
httpapispecs.config.istio.io               2020-07-22T23:25:58Z
instances.config.istio.io                  2020-07-22T23:25:58Z
istiooperators.install.istio.io            2020-07-22T23:25:58Z
peerauthentications.security.istio.io      2020-07-22T23:25:58Z
quotaspecbindings.config.istio.io          2020-07-22T23:25:58Z
quotaspecs.config.istio.io                 2020-07-22T23:25:58Z
rbacconfigs.rbac.istio.io                  2020-07-22T23:25:58Z
requestauthentications.security.istio.io   2020-07-22T23:25:58Z
rules.config.istio.io                      2020-07-22T23:25:58Z
serviceentries.networking.istio.io         2020-07-22T23:25:58Z
servicerolebindings.rbac.istio.io          2020-07-22T23:25:58Z
serviceroles.rbac.istio.io                 2020-07-22T23:25:58Z
sidecars.networking.istio.io               2020-07-22T23:25:58Z
templates.config.istio.io                  2020-07-22T23:25:58Z
virtualservices.networking.istio.io        2020-07-22T23:25:58Z
workloadentries.networking.istio.io        2020-07-22T23:25:58Z
```

## Deploy with Istio/Envoy Sidecars

```bash
% kubectl config current-context #istio-mk
% minikube --profile istio-mk ip  
192.168.99.102
% minikube --profile istio-mk status
% kubectl create namespace istio-demo
% kubectl config set-context $(kubectl config current-context) --namespace=istio-demo
% kubens
default
istio-demo
istio-system
kube-node-lease
kube-public
kube-system
```

Let's clone https://github.com/redhat-developer-demos/istio-tutorial


### Manual inject

We have a [deployment file](/images/Deployment.yml).

We use `istioctl` to inject sidecar:

```
istioctl kube-inject -f customer/kubernetes/Deployment.yml
```

You can see some sidecar is injected.

### Automatic inject

```
% kubectl get namespace --show-labels
NAME              STATUS   AGE   LABELS
default           Active   47h   <none>
istio-demo        Active   15m   <none>
istio-system      Active   47m   istio-injection=disabled
kube-node-lease   Active   47h   <none>
kube-public       Active   47h   <none>
kube-system       Active   47h   <none>

% kubectl label namespace istio-demo istio-injection=enabled
% kubectl get namespace --show-labels
NAME              STATUS   AGE   LABELS
default           Active   47h   <none>
istio-demo        Active   17m   istio-injection=enabled
istio-system      Active   48m   istio-injection=disabled
kube-node-lease   Active   47h   <none>
kube-public       Active   47h   <none>
kube-system       Active   47h   <none>
```

Alse, we notice the yaml has an annotation `sidecar.istio.io/inject: "true"`, that's the different with the normal yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: customer
    version: v1
  name: customer
spec:
  replicas: 1
  selector:
    matchLabels:
      app: customer
      version: v1
  template:
    metadata:
      labels:
        app: customer
        version: v1
      annotations:
        sidecar.istio.io/inject: "true" #here!
    spec:
      containers:
      - env:
        - name: JAVA_OPTIONS
          value: -Xms15m -Xmx15m -Xmn15m
        name: customer
        image: quay.io/rhdevelopers/istio-tutorial-customer:v1.1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
          name: http
          protocol: TCP
        - containerPort: 8778
          name: jolokia
          protocol: TCP
        - containerPort: 9779
          name: prometheus
          protocol: TCP
        resources:
          requests: 
            memory: "20Mi" 
            cpu: "200m" # 1/5 core
          limits:
            memory: "40Mi"
            cpu: "500m" 
        livenessProbe:
          exec:
            command:
            - curl
            - localhost:8080/health/live
          initialDelaySeconds: 5
          periodSeconds: 4
          timeoutSeconds: 1
        readinessProbe:
          exec:
            command:
            - curl
            - localhost:8080/health/ready
          initialDelaySeconds: 6
          periodSeconds: 5
          timeoutSeconds: 1
        securityContext:
          privileged: false
```

```
% kubectl apply -f customer/kubernetes/Deployment.yml 
deployment.apps/customer created
% kubectl get pods               
NAME                        READY   STATUS            RESTARTS   AGE
customer-76bddbf59c-7p6mr   0/2     PodInitializing   0          11s
```

There are two containers in the pods.

```bash
% kubectl apply -f customer/kubernetes/Service.yml 
service/customer created
```

service.yaml:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: customer
  labels:
    app: customer    
spec:
  ports:
  - name: http
    port: 8080
  selector:
    app: customer

```

```bash
% kubectl apply -f customer/kubernetes/Gateway.yml 
gateway.networking.istio.io/customer-gateway created
virtualservice.networking.istio.io/customer-gateway created
% kubectl get vs
NAME               GATEWAYS             HOSTS   AGE
customer-gateway   [customer-gateway]   [*]     18s
```

Gateway.yaml:

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: customer-gateway
spec:
  selector:
    istio: ingressgateway # use istio default controller
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: customer-gateway
spec:
  hosts:
  - "*"
  gateways:
  - customer-gateway
  http:
  - match:
    - uri:
        prefix: /customer
    rewrite:
      uri: /
    route:
    - destination:
        host: customer
        port:
          number: 8080
```

```bash
% kubectl apply -f preference/kubernetes/Deployment.yml 
deployment.apps/preference-v1 created
% kubectl apply -f preference/kubernetes/Service.yml   
service/preference created
% kubectl apply -f recommendation/kubernetes/Deployment.yml 
deployment.apps/recommendation-v1 created
% kubectl apply -f recommendation/kubernetes/Service.yml   
service/recommendation created
% kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
customer         ClusterIP   10.98.110.140   <none>        8080/TCP   6m39s
preference       ClusterIP   10.97.254.164   <none>        8080/TCP   32s
recommendation   ClusterIP   10.108.35.221   <none>        8080/TCP   6s
% kubectl get vs      
NAME               GATEWAYS             HOSTS   AGE
customer-gateway   [customer-gateway]   [*]     7m36s
```
call trace:
user->customer->preference->recommendation

```bash
kubectl get services -n istio-system
NAME                        TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                      AGE
grafana                     ClusterIP      10.110.100.175   <none>        3000/TCP                                                                     70m
istio-egressgateway         ClusterIP      10.111.112.128   <none>        80/TCP,443/TCP,15443/TCP                                                     70m
istio-ingressgateway        LoadBalancer   10.108.217.161   <pending>     15020:30321/TCP,80:30350/TCP,443:30445/TCP,31400:31920/TCP,15443:31115/TCP   70m
istiod                      ClusterIP      10.103.248.183   <none>        15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                                70m
jaeger-agent                ClusterIP      None             <none>        5775/UDP,6831/UDP,6832/UDP                                                   70m
jaeger-collector            ClusterIP      10.103.241.201   <none>        14267/TCP,14268/TCP,14250/TCP                                                70m
jaeger-collector-headless   ClusterIP      None             <none>        14250/TCP                                                                    70m
jaeger-query                ClusterIP      10.101.27.250    <none>        16686/TCP                                                                    70m
kiali                       ClusterIP      10.111.79.49     <none>        20001/TCP                                                                    70m
prometheus                  ClusterIP      10.104.161.153   <none>        9090/TCP                                                                     70m
tracing                     ClusterIP      10.106.110.151   <none>        80/TCP                                                                       70m
zipkin                      ClusterIP      10.111.30.70     <none>        9411/TCP                                                                     70m
```

We can see the nodeport is 30350

```
% minikube --profile istio-mk ip
192.168.99.102
% curl 192.168.99.102:30350/customer
customer => preference => recommendation v1 from 'f11b097f1dd0': 1
```

