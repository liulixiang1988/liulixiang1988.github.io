---

title: Building Images & Running Containers

date: 2020-07-12 09:30

category: Kubernetes

tags: 

- Kubernetes
- Minikube
- Docker

author: 刘理想

---

## End to End

1. Find a base image: docker.io, quay.io, gcr.io, registry.redhat.io

2. Craft your Dockerfile

3. Configure docker: `eval $(minikube --profile myprofile decoder-env)`

4. Build your image: `docker build -t liulx/myimage:v1 .`

   a. Test via:

   	- `docker run -it -p 8080:8080 --name myboot liulx/myimage:v1`
   	- `docker run -d -p 8080:8080 --name my boot liulx/myboot:v1`
   	- `curl $(minikube --profile myprofile ip):8080`

   b. If remote repo, do `docker tag` and `docker push`

   c. `docker stop containername` to stop testing

5. `kubectl apply -f myDeployment.yaml`

6. `kubectl apply -f myService.yaml`

7. Expose a URL via your kubernetes distribution's load-balancer

## docker build

```bash
docker build -t something/animagename:tag
```

The `.`indicates where to find the Dockerfile and assets to be included in the build process.

You can alse explicitly identify the Dockerfile:

- `docker build -t somestring/animagename:tag -f somedirectory/Dockerfile_Production .`
- `docker build -t somestring/animagename:tag -f somedirectory/Dockerfile_Testing .`
- `docker build -f src/main/docker/Dockerfile.native -t mystuff/myimage:v1 .`

## Builiding Images

Options Include:

1. `docker build` then `kubectl run` or `kubectl create -f deploy.yml`
2. Jib - Maven/Gradle plugin by google
3. Shift maven plugin by Red hat
4. s2i - source to image
5. Tekton - pipeline-based image building
6. No docker: red hat's podman, Google's kaniko, Uber's makisu
7. Buildpacks - similar to Heroku & Cloud Foundry

## Namespaces

```
- `kubectl create namespace demo`
- `kubectl get namespace demo -o yaml`
- `kubectl get namesapce demo --export -o yaml`
- `kubectl delete namespace/demo`
- `kubectl config set-context --current --namespace=myspace`
- ` kubectl config view --minify --template '{{ index .contexts 0 "context" "namespace" }}'` confirm your context
```

## Pods

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: quarkus-demo
spec:
  containers:
  - name: quarkus-demo
    image: quay.io/burrsutter/quarkus-demo:1.0.0
EOF
```

## ReplicaSets

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: ReplicaSet
metadata:
    name: rs-quarkus-demo
spec:
    replicas: 3
    selector:
       matchLabels:
          app: quarkus-demo
    template:
       metadata:
          labels:
             app: quarkus-demo
             env: dev
       spec:
          containers:
          - name: quarkus-demo
            image: quay.io/burrsutter/quarkus-demo:1.0.0
EOF
```

See what it produced:

```bash
$ kubectl get pods --show-labels
NAME                    READY   STATUS    RESTARTS   AGE   LABELS
rs-quarkus-demo-64dnl   1/1     Running   0          49s   app=quarkus-demo,env=dev
rs-quarkus-demo-8pn8b   1/1     Running   0          29s   app=quarkus-demo,env=dev
rs-quarkus-demo-ksm5f   1/1     Running   0          16s   app=quarkus-demo,env=dev
```

## Deployments

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: quarkus-demo-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: quarkus-demo
  template:
    metadata:
      labels:
        app: quarkus-demo
        env: dev
    spec:
      containers:
      - name: quarkus-demo
        image: quay.io/burrsutter/quarkus-demo:1.0.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
EOF
```

Run curl inside Pod: `kubectl exec -it pod name -- curl localhost:8080`

## Service

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: the-service
spec:
  selector:
    app: quarkus-demo
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: NodePort
EOF
```

curl those Endpoints behind that service:

```bash
$ IP=$(minikube --profile 9steps ip)
$ PORT=$(kubectl get service the-service -o jsonpath="{.spec.ports[*].nodePort}")
$ while true; do curl $IP:$PORT; sleep .5; done
```

## Scale

Let’s scale up the application to 3 replicas, there are several possible ways to achieve this result.

You can edit the myboot-deployment.yml, updating replicas to 3

```
$ kubectl replace -f kubefiles/myboot-deployment.yml
```

Or use the kubectl scale command

```
$ kubectl scale --replicas=3 deployment/myboot
```

Or you might patch it

```
$ kubectl patch deployment/myboot -p '{"spec":{"replicas":3}}'
```

Or use the kubectl edit command which essentially gives you "vi" for editing the deployment yaml

```
$ kubectl edit deployment/myboot
/replicas
esc-w-q
```

Note: You can use VS Code as your editor with export KUBE_EDITOR="code -w"

You can then use "kubectl get pods" to see the pods that have been created

```
$ kubectl get pods
```

Note: 3 pods might push you out of your memory limits for your VM. Check your memory usage with:

```
$ minishift -p 9steps ssh
# or
$ minikube ssh
$ free -m
$ top -o %MEM
```

## Update

Update MyRESTController.java

```
greeting = environment.getProperty("GREETING","Bonjour");
```

Compile & Build the fat jar

```
mvn clean package
```

You can test with "java -jar target/boot-demo-0.0.1.jar" and "curl localhost:8080". Ideally, you would have unit tests executed with "mvn test" as well.

Build the new docker image with a v2 tag

```
$ docker build -t 9stepsawesome/myboot:v2 .
$ docker images | grep myboot
```

Rollout the update

```
$ kubectl set image deployment/myboot myboot=9stepsawesome/myboot:v2
OR
$ kubectl set image deployment/myboot myboot=quay.io/burrsutter/myboot:v2
```

And if you were running your polling curl command, you might see

```
Aloha from Spring Boot! 346 on myboot-79bfc988c8-ttz5r
Aloha from Spring Boot! 270 on myboot-79bfc988c8-ntb8d
Aloha from Spring Boot! 348 on myboot-79bfc988c8-ttz5r
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
curl: (7) Failed to connect to 192.168.99.105 port 31218: Connection refused
Bonjour from Spring Boot! 0 on myboot-757658fc4c-qnvqx
Bonjour from Spring Boot! 1 on myboot-5955897c9b-zthj9
```

We will be working on those errors when we address Liveness and Readiness probes in Step 7

For now, undo the update, going back to v1

```
$ kubectl rollout undo deployment/myboot
```

Scale to 1

```
$ kubectl scale --replicas=1 deployment/myboot
```

Hit the sysresources endpoint

```
$ curl $(minikube -p 9steps ip):$(kubectl get service/myboot -o jsonpath="{.spec.ports[*].nodePort}")/sysresources
 Memory: 1324 Cores: 2
```

Note: you can use echo to see what this URL really looks like on your machine

```
$ echo $(minikube -p 9steps ip):$(kubectl get service/myboot -o jsonpath="{.spec.ports[*].nodePort}")/sysresources
192.168.99.105:30479/sysresources
```

The results of the "sysresources" call should be about 1/4 memory and all the cores that were configured for the VM. You can double check this with the following command:

```
$ minikube config view
- cpus: 2
- memory: 6144
- vm-driver: virtualbox
```

Now, let’s apply resource contraints via Kubernetes & deployment yaml. Look at myboot-deployment-resources.yml

```
$ kubectl replace -f kubefiles/myboot-deployment-resources.yml
```

Curl sysresources again

```
$ curl $(minikube -p 9steps ip):$(kubectl get service/myboot -o jsonpath="{.spec.ports[*].nodePort}")/sysresources
Memory: 1324 Cores: 2
```

In another terminal window, watch the pods

```
$ kubectl get pods -w
```

and now curl the consume endpoint

```
$ curl $(minikube -p 9steps ip):$(kubectl get service/myboot -o jsonpath="{.spec.ports[*].nodePort}")/consume
```

and you should see a OOMKilled

```
$ kubectl get pods -w
NAME                      READY     STATUS    RESTARTS   AGE
myboot-68d666dd8d-m9m5r   1/1       Running   0          1m
myboot-68d666dd8d-m9m5r   0/1       OOMKilled   0         2m
myboot-68d666dd8d-m9m5r   1/1       Running   1         2m
```

And you can also see the OOMKilled with the kubectl describe command

```
$ kubectl describe pod -l app=myboot
...
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    137
      Started:      Tue, 31 Jul 2018 13:42:18 -0400
      Finished:     Tue, 31 Jul 2018 13:44:24 -0400
    Ready:          True
    Restart Count:  1
...
```

## JIB

```xml
<plugin>
    <groupId>com.google.cloud.tools</groupId>
    <artifactId>jib-maven-plugin</artifactId>
    <version>1.6.1</version>
</plugin>
```

Build Docker Image and run:

```bash
eval $(minishift docker-env)
mvn compile jib:dockerBuild -Dimage=9stepsawesome/myboot:v1
docker run -it -p 8080:8080 9stepsawesome/myboot:v1
curl $(minishift ip):8080
```