---
title: Extend Your Kubernetes
date: 2020-07-16 07:15
category: Kubernetes
tags: 
- Kubernetes
author: 刘理想
---

## Discover CustomResourceDefinitions


Custom Resources extend the API

Custom Controllers provide the functionality - continually maintains the desired state -  to monitor its state and reconcile the resource to match with the configuration

https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/

https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/

Custom Resource Definitions (CRDs) in version 1.7

```bash
$ kubectl get crds
$ kubectl api-resources
```

```yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: pizzas.mykubernetes.burrsutter.com
  labels:
    app: pizzamaker
    mylabel: stuff
spec:
  group: mykubernetes.burrsutter.com
  scope: Namespaced
  version: v1beta2
  names:
    kind: Pizza
    listKind: PizzaList
    plural: pizzas
    singular: pizza
    shortNames:
    - pz
  validation:
    openAPIV3Schema:
      type: object
      properties:
        spec:
          type: object
          properties:
            toppings:
              type: array
            sauce:
              type: string
```

## Add Pizzas to your Kubernets Cluster

cheese-pizza.yaml

```yaml
apiVersion: mykubernetes.burrsutter.com/v1beta2
kind: Pizza
metadata:
  name: burrcheese
spec:
  toppings: 
  - mozzarella
  sauce: regular
```

```bash
$ kubectl apply -f pizza-crd.yaml
$ kubectl get crds | grep pizza
$ kubectl create namespace pizzas
$ kubectl config set-context --current --namespace=pizzas
$ kubectl apply -f cheese-pizza.yaml
$ kubectl get pizzas
$ kubectl describe pizza burrcheese
```

delete all the pizzas

```bash
kubectl delete pizzas --all
```

## Understanding Controllers

Build a controller via Metacontroller:
https://metacontroller.app/guide/create/

https://github.com/GoogleCloudPlatform/metacontroller

Note: https://github.com/GoogleCloudPlatform/metacontroller/issues/184

### Setup Metacontroller

```bash
$ kubectl create namespace metacontroller

$ kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/metacontroller/master/manifests/metacontroller-rbac.yaml

$ kubectl apply -f https://raw.githubusercontent.com/GoogleCloudPlatform/metacontroller/master/manifests/metacontroller.yaml

$ kubectl get pods --all-namespaces

$ stern metacontroller-0 -n metacontroller

$ kubectl apply -f pizza-controller.yaml

metacontroller-0 metacontroller I0904 22:23:35.324855       1 metacontroller.go:148] sync CompositeController pizza-controller
metacontroller-0 metacontroller I0904 22:23:35.324899       1 factory.go:104] Starting shared informer for pizzas in mykubernetes.burrsutter.com/v1beta2
metacontroller-0 metacontroller I0904 22:23:35.324940       1 factory.go:104] Starting shared informer for pods in v1
metacontroller-0 metacontroller I0904 22:23:35.325019       1 controller.go:165] Starting Pizza CompositeController
metacontroller-0 metacontroller I0904 22:23:35.325029       1 controller.go:169] Waiting for Pizza CompositeController caches to sync
metacontroller-0 metacontroller I0904 22:23:35.325039       1 controller.go:32] Waiting for caches to sync for Pizza controller
metacontroller-0 metacontroller I0904 22:23:35.325453       1 reflector.go:202] Starting reflector *unstructured.Unstructured (30m0s) from metacontroller.app/dynamic/informer/factory.go:111
metacontroller-0 metacontroller I0904 22:23:35.325476       1 reflector.go:240] Listing and watching *unstructured.Unstructured from metacontroller.app/dynamic/informer/factory.go:111
metacontroller-0 metacontroller I0904 22:23:35.325947       1 reflector.go:202] Starting reflector *unstructured.Unstructured (30m0s) from metacontroller.app/dynamic/informer/factory.go:111
metacontroller-0 metacontroller I0904 22:23:35.325963       1 reflector.go:240] Listing and watching *unstructured.Unstructured from metacontroller.app/dynamic/informer/factory.go:111
metacontroller-0 metacontroller I0904 22:23:35.525234       1 shared_informer.go:123] caches populated
metacontroller-0 metacontroller I0904 22:23:35.525259       1 controller.go:39] Caches are synced for Pizza controller
```

pizza-controoler.yaml:

```yaml
apiVersion: metacontroller.k8s.io/v1alpha1
kind: CompositeController
metadata:
  name: pizza-controller
spec:
  generateSelector: true
  parentResource:
    apiVersion: mykubernetes.burrsutter.com/v1beta2
    resource: pizzas
  childResources:
  - apiVersion: v1
    resource: pods
    updateStrategy:
      method: Recreate
  hooks:
    sync:
      webhook:
        url: http://pizza-controller.pizzahat:8080/sync
```

### Add Pizza Controller

```
kubectl -n pizzahat create configmap pizza-controller --from-file=sync.py

kubectl -n pizzahat apply -f webhook-py.yaml
```

sync.py:

```python
from BaseHTTPServer import BaseHTTPRequestHandler, HTTPServer
import json
import logging

class Controller(BaseHTTPRequestHandler):
  def sync(self, parent, children):
    
    # Compute status based on observed state.
    desired_status = {
       "pods": 1
    }

    # Collect the specifications
    name = parent["metadata"]["name"]
    sauce = parent.get("spec", {}).get("sauce")
    toppings = parent.get("spec",{}).get("toppings")
    print("\n\nsauce: %s" % sauce)
    print("toppings: %s" % toppings)
    for topping in toppings:
      print(topping)

    stuff = ' ' + sauce + ' ' + ' '.join(toppings) + ' of ' + name
   
    print stuff

    # Generate the desired child object(s)
    
    desired_pods = [
      {
        "apiVersion": "v1",
        "kind": "Pod",
        "metadata": {
          "name": name
        },
        "spec": {
          "restartPolicy": "OnFailure",
          "containers": [
            {
              "name": "pizza",
              "image": "busybox",
              "command": ["echo", "requested pizza: %s" % stuff]
            }
          ]
        }
      }
    ]

    return {"status": desired_status, "children": desired_pods}

  def do_POST(self):
    # Serve the sync() function as a JSON webhook.
    
    observed = json.loads(self.rfile.read(int(self.headers.getheader("content-length"))))
    desired = self.sync(observed["parent"], observed["children"])

    self.send_response(200)
    self.send_header("Content-type", "application/json")
    self.end_headers()
    self.wfile.write(json.dumps(desired))

HTTPServer(("", 8080), Controller).serve_forever()
```

webhook-py.yaml

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: pizza-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pizza-controller
  template:
    metadata:
      labels:
        app: pizza-controller
    spec:
      containers:
      - name: controller
        image: python:2.7
        command: ["python", "/hooks/sync.py"]
        volumeMounts:
        - name: hooks
          mountPath: /hooks
      volumes:
      - name: hooks
        configMap:
          name: pizza-controller        
---
apiVersion: v1
kind: Service
metadata:
  name: pizza-controller
spec:
  selector:
    app: pizza-controller
  ports:
  - port: 8080        
```

### Deploy some Pizzas

```bash
$ stern metacontroller-0 -n metacontroller

$ stern pizza-controller

$ watch kubectl get pods 

$ kubectl -n pizzahat apply -f veggie-lovers.yaml

$ kubectl logs burrveggie
```

### Eat the Pizza

```bash
$ kubectl delete pizza burrveggie
$ kubectl delete namespace pizzahat
```

## Discover Operators

Operator SDK

https://github.com/operator-framework/operator-lifecycle-manager/

### Kafka via OperatorHub

```bash
$ kubectl create namespace franz

$ kubectl config set-context --current --namespace=franz

$ curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/0.12.0/install.sh | bash -s 0.12.0

$ kubectl create -f https://operatorhub.io/install/strimzi-kafka-operator.yaml

$ kubectl get csv -n operators

$ kubectl get crds | grep kafka

$ watch kubectl get pods

$ kubectl apply -f kafka-strimzi-minikube.yml

$ kubectl get kafkas
```

kafka-strimzi-minikube.yml

```yaml
apiVersion: kafka.strimzi.io/v1alpha1
kind: Kafka
metadata: 
  name: burr-cluster
spec:
  kafka:
    replicas: 3
    listeners:
      external:
        type: nodeport
    storage:
      type: ephemeral
  zookeeper:
    replicas: 3
    storage:
      type: ephemeral
  entityOperator:
    topicOperator: {}
```

### Clean up

```bash
$ kubectl delete kafka burr-cluster
$ kubectl delete namespace franz
```


