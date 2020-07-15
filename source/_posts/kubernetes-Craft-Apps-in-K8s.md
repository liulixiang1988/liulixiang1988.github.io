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

`kubectl scale --replicas=3 deployment xxx`

```
StrategyType: RollingUpdate
RollingUpdateStrategy: 1max unavailable, 1max surge
```

```yaml
template:
metadata:
    labels:
    app: myboot
spec:
    containers:
    - name: myboot
    image: myboot:v1
    ports:
        - containerPort: 8080
    livenessProbe:
        httpGet:
            port: 8080
            path: /
        initialDelaySeconds: 10
        periodSeconds: 5
        timeoutSeconds: 2          
    readinessProbe:
        httpGet:
        path: /health
        port: 8080
        initialDelaySeconds: 10
        periodSeconds: 3
```


Once you understand the basics then you can try the advanced demonstration.  Where a stateful shopping cart is preserved across a rolling update based on leveraging the readiness probe.

https://github.com/redhat-developer-demos/popular-movie-store


More information on Live & Ready 
https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/


## Deploy Blue/Green
[Description of Blue/Green Deployment](https://martinfowler.com/bliki/BlueGreenDeployment.html)

You have the new pod as well as the old ones

```yaml
$ kubectl get pods
NAME                         READY     STATUS    RESTARTS   AGE
mynode-68b9b9ffcc-jv4fd      1/1       Running   0          23m
mynode-68b9b9ffcc-vq9k5      1/1       Running   0          23m
mynodenew-5fc946f544-q9ch2   1/1       Running   0          25s
mynodenew-6bddcb55b5-wctmd   1/1       Running   0          25s
```

your client/user is still seeing the old one only

```bash
$  curl $(minikube ip):$(kubectl get service/mynode -o jsonpath="{.spec.ports[*].nodePort}")
Node Hello on mynode-668959c78d-j66hl 102
```

Now update the single Service to point to the new pod and go GREEN

```bash
$ kubectl patch svc/mynode -p '{"spec":{"selector":{"app":"mynodenew"}}}'
```

Note: Our deployment yaml did not have a live & ready probe, things worked out OK here because we waited until long after mynodenew was up and running before flipping the service selector.

### Built-In Canary

[Description of Canary](https://martinfowler.com/bliki/CanaryRelease.html)

There are at least two types of deployments that some folks consider "canary deployments" in Kubernetes.  The first is simply the rolling update strategy with the health check (liveness probe), if the liveness check fails, it knows to undo the deployment.

Switching back to focusing on myboot and myspace
```
$ kubectl config set-context --current --namespace=myspace
$ kubectl get pods
kubectl get pods
NAME                      READY     STATUS        RESTARTS   AGE
myboot-859cbbfb98-4rvl8   1/1       Running       0          55m
myboot-859cbbfb98-rwgp5   1/1       Running       0          55m
```

Make sure myboot has 2 replicas
```
$ kubectl scale deployment/myboot --replicas=2
```

and let's attempt to put some really bad code into production

Go into hello/springboot/MyRESTController.java and add a System.exit(1) into the /health logic
```java
   @RequestMapping(method = RequestMethod.GET, value = "/health")
   public ResponseEntity<String> health() {
        System.exit(1);
        return ResponseEntity.status(HttpStatus.OK)
            .body("I am fine, thank you\n");
   }
```

Obviously this sort of thing would never pass through your robust code reviews and automated QA but let's assume it does.

Build the code
```
$ mvn clean package
```

Build the docker image for v3
```
$ docker build -t 9stepsawesome/myboot:v3 .
```

Terminal 1: Start a poller
```bash
while true
do
  curl $(minikube -p 9steps ip):$(kubectl get service/myboot -o jsonpath="{.spec.ports[*].nodePort}" -n myspace)
  sleep .3;
done
```

Terminal 2: Watch pods
```
$ kubectl get pods -w
```

Terminal 3: Watch events
```
$ kubectl get events --sort-by=.metadata.creationTimestamp
```

Terminal 4: rollout the v3 update
```
$ kubectl set image deployment/myboot myboot=9stepsawesome/myboot:v3
```

and watch the fireworks


```
$ kubectl get pods -w
myboot-5d7fb559dd-qh6fl   0/1       Error     1         11m
myboot-859cbbfb98-rwgp5   0/1       Terminating   0         6h
myboot-859cbbfb98-rwgp5   0/1       Terminating   0         6h
myboot-5d7fb559dd-qh6fl   0/1       CrashLoopBackOff   1         11m
myboot-859cbbfb98-rwgp5   0/1       Terminating   0         6h
```

Look at your Events
```
$ kubectl get events -w
6s          Warning   Unhealthy           pod/myboot-64db5994f6-s24j5    Readiness probe failed: Get http://172.17.0.6:8080/health: net/http: request canceled (Client.Timeout exceeded while awaiting headers)
6s          Warning   Unhealthy           pod/myboot-64db5994f6-h8g2t    Readiness probe failed: Get http://172.17.0.7:8080/health: net/http: request canceled (Client.Timeout exceeded while awaiting headers)
5s          Warning   Unhealthy
```

And yet your polling client, stays with the old code & old pod
```
Aloha from Spring Boot! 133 on myboot-859cbbfb98-4rvl8
Aloha from Spring Boot! 134 on myboot-859cbbfb98-4rvl8
```

If you watch a while, the CrashLoopBackOff will continue and the restart count will increment.

Now, go fix the MyRESTController and also change from Hello to Aloha

No more System.exit()
```
   @RequestMapping(method = RequestMethod.GET, value = "/health")
   public ResponseEntity<String> health() {        
        return ResponseEntity.status(HttpStatus.OK)
            .body("I am fine, thank you\n");
   }
```
And change the greeting response to something you recognize.

Save

```
$ mvn clean package

$ docker build -t 9stepsawesome/myboot:v3 .
```

and now just wait for the "control loop" to self-correct

### Manual Canary with multiple Deployments

Go back to v1
```
$ kubectl set image deployment/myboot myboot=9stepsawesome/myboot:v1
```

Next, we will use a 2nd Deployment like we did with Blue/Green.  

```
$ kubectl create -f kubefiles/myboot-deployment-canary.yml
```

And you can see a new pod being born
```
$ kubectl get pods
```

And this is the v3 one
```
$ kubectl get pods -l app=mybootcanary
$ kubectl exec -it mybootcanary-6ddc5d8d48-ptdjv curl localhost:8080/
```

Now we add a label to both v1 and v3 Deployments PodTemplate, causing new pods to be born
```
$ kubectl patch deployment/myboot -p '{"spec":{"template":{"metadata":{"labels":{"newstuff":"withCanary"}}}}}'
$ kubectl patch deployment/mybootcanary -p '{"spec":{"template":{"metadata":{"labels":{"newstuff":"withCanary"}}}}}'
```

Tweak the Service selector for this new label
```
$ kubectl patch service/myboot -p '{"spec":{"selector":{"newstuff":"withCanary","app": null}}}'
```

You should see approximately 30% canary mixed in with previous deployment
```
Hello from Spring Boot! 23 on myboot-d6c8464-ncpn8
Hello from Spring Boot! 22 on myboot-d6c8464-qnxd8
Aloha from Spring Boot! 83 on mybootcanary-74d99754f4-tx6pj
Hello from Spring Boot! 24 on myboot-d6c8464-ncpn8
```

You can then manipulate the percentages via the replicas associated with each deployment
20% Aloha (Canary)
```
$ kubectl scale deployment/myboot --replicas=4
$ kubectl scale deployment/mybootcanary --replicas=1
```

The challenge with this model is that you have to have the right pod count to get the right mix. If you want a 1% canary, you need 99 of the non-canary pods.

### Istio Cometh

The concept of the Canary rollout gets a lot smarter and more interesting with Istio.  You also get the concept of dark launches which allows you to push a change into the production environment, send traffic to the new pod(s) yet no responses are actual sent back to the end-user/client.

See [bit.ly/istio-tutorial](https://bit.ly/istio-tutorial)


## Store data with PersistentVolume and PersistentVolumeClaim

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: postgres-pv
  labels:
    type: local
spec:
  storageClassName: mystorage
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 2Gi
  hostPath:
    path: "/data/mypostgresdata/"
```

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: postgres-pvc
  labels: 
   app: postgres
spec:
  storageClassName: mystorage
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:10.5
        imagePullPolicy: "IfNotPresent"
        env: 
        - name: POSTGRES_DB 
          value: postgresdb
        - name: POSTGRES_USER
          value: admin
        - name: POSTGRES_PASSWORD
          value: adminS3cret
        ports:
        - containerPort: 5432
          name: postgres
        volumeMounts:
          # mountPath within the container
        - name: postgres-pvc
          mountPath: "/var/lib/postgresql/data/:Z"          
      volumes:
          # mapped to the PVC
        - name: postgres-pvc
          persistentVolumeClaim:
            claimName: postgres-pvc
```

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: postgres
  labels:
    app: postgres
    visualize: "true"
spec: 
  ports:
    # the port that this service should serve on
    - port: 5432  
  selector: 
    app: postgres
```