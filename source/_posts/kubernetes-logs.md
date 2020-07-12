---
title: Kubernetes Logs, Exec, Resource Constraint, ConfigMap, Secret
date: 2020-07-12 13:30
category: Kubernetes
tags: 
- Kubernetes
- Minikube
- Docker
author: 刘理想
---

## logs

```bash
kubectl get pods
kubectl logs podname -p
kubectl logs podname
```

## exec

```bash
kubectl exec -it pod-name /bin/bash
# 查看cgroup配置
cd /sys/fs/cgroup/memory
```

## Constraint CPU & Memory

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: myboot
  name: myboot
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myboot
  template:
    metadata:
      labels:
        app: myboot
    spec:
      containers:
      - name: myboot
        image: 9stepsawesome/myboot:v1     
        ports:
          - containerPort: 8080
        resources:
          requests: 
            memory: "300Mi" 
            cpu: "250m" # 1/4 core
          limits:
            memory: "400Mi"
            cpu: "1000m" # 1 core
```

## ConfigMap

First Let's see the environment.

Change the environment on deployment:

```
kubectl set env deployment/myboot GREETING="hi"
```

Unset environment:

```
kubectl set env deployment/myboot GREETING-
```

Then let's see the config map.

`kubectl create cm my-config --from-env-file=config/some.properties`

some.properties:

```
GREETING=LiuLixiang
MSG=hello
```

deployment.yml which uses the ConfigMap:

```yaml
spec:
    containers:
    - name: myboot
    envFrom:
    - configMapRef:
        name: my-config
```

Partial.java

```java
@Autowired
private Environment environment;

String greeting = environment.getProperties("GREETING", "default");
```

## Secrets

```bash
kubectl create secret generic mysecret --from-literal=user='username' --from-literal=password='mypassord'
kubectl get secret mysecret -o yaml
```

Secrets's data is encoded.

```bash
echo 'dXNlcm5hbWU=' | base64 --decode
```

use the secret in the yaml:

```yaml
spec:
    containers:
    - name: myboot
    image: 9stepsawesome/myboot:v1  
    ports:
        - containerPort: 8080
    volumeMounts:          
        - name: mysecretvolume
        mountPath: /mystuff/secretstuff
        readOnly: true
    volumes:
    - name: mysecretvolume
        secret:
        secretName: mysecret
```

use in the container:

```bash
kubectl exec -it podname /bin/bash
# in the pod
cd /mystuff/secretstuff
cat username
```