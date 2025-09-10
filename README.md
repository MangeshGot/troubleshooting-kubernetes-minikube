
# kubectl commands

## Creating objects 

For start for applying deployment or services

```bash
kubectl apply -f pod.yml
```
## Viewing and finding resources

For checking any deployment or services Status

```bash
kubectl get pods --all-namespaces             # List all pods in all namespaces
kubectl get pods -o wide                      # List all pods in the current namespace, with more details
kubectl get deployment my-dep                 # List a particular deployment
kubectl get pods                              # List all pods in the namespace
kubectl get pod my-pod -o yaml                # Get a pod's YAML
```
## Deleting resources

For deleting any deployment or services

```bash
kubectl delete -f ./pod.json                                      # Delete a pod using the type and name specified in pod.json
```

## Interacting with running Pods 

```bash
kubectl logs my-pod                                 # dump pod logs (stdout)
```
```bash
kubectl run -i --tty busybox --image=busybox:1.28 -- sh  # Run pod as interactive shell
```
```bash
kubectl exec my-pod -- ls /                         # Run command in existing pod (1 container case)
```
```bash
kubectl top pod                                     # Show metrics for all pods in the default namespace
```
## Services

Before creating services clone repo
```bash
git clone https://github.com/iam-veeramalla/Docker-Zero-to-Hero.git
```
in this repo go to

```bash
cd /home/mangesh/Docker-Zero-to-Hero/examples/python-web-app
```
it has docker file build the image file and push it on your docker Hub
```bash
sudo docker build -t mangeshgot/python:latest .         # username/imagename:tagname
```
push this image to docker hub (login in docker before pushing it)

```bash
sudo docker push mangeshgot/python:latest
```

create deployment.yml file
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-deployment
  labels:
    app: python-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: python-deployment
  template:
    metadata:
      labels:
        app: python-deployment
    spec:
      imagePullSecrets:
         - name: regcred
      containers:
      - name: nginx
        image: mangeshgot/python:latest
        ports:
        - containerPort: 8000
```
create imagePullSecrets

```bash
kubectl create secret docker-registry regcred   --docker-username=YOUR_DOCKER_USERNAME   --docker-password=YOUR_DOCKER_PASSWORD/TOKEN   --docker-email=YOUR_EMAIL
```
deploy yml file

```bash
kubectl apply -f deployment.yml
```
check pod IP addresss

```bash
kubectl get pods -o wide
```

for me output is this
```bash
root@mangesh-OptiPlex-5050:/home/mangesh/Docker-Zero-to-Hero/examples/python-web-app# kubectl get pods -o wide
NAME                                 READY   STATUS    RESTARTS   AGE   IP            NODE       NOMINATED NODE   READINESS GATES
python-deployment-75f895676f-4rtv5   1/1     Running   0          65s   10.244.0.82   minikube   <none>           <none>
python-deployment-75f895676f-82tc5   1/1     Running   0          69s   10.244.0.81   minikube   <none>           <none>
````

#### Access the minikube cluster

```bash
minikube ssh
```
and curl your python application

```bash
curl -L http://10.244.0.82:8000/demo
```
you will output like following

```bash
docker@minikube:~$ curl -L http://10.244.0.82:8000/demo
<!DOCTYPE html>
<html lang="en">
<head>
<title>CSS Template</title>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
* {
  box-sizing: border-box;
}

body {
  font-family: Arial, Helvetica, sans-serif;
}

/* Style the header */
header {
  background-color: #666;
  padding: 30px;
  text-align: center;
  font-size: 35px;
  color: yellow;
}

/* Create two columns/boxes that floats next to each other */
nav {
  float: left;
  width: 30%;
  height: 300px; /* only for demonstration, should be removed */
  background: #ccc;
  padding: 20px;
}

/* Style the list inside the menu */
nav ul {
  list-style-type: none;
  padding: 0;
}

article {
  float: left;
  padding: 20px;
  width: 70%;
  background-color: #f1f1f1;
  height: 300px; /* only for demonstration, should be removed */
}

/* Clear floats after the columns */
section::after {
  content: "";
  display: table;
  clear: both;
}

/* Style the footer */
footer {
  background-color: #777;
  padding: 10px;
  text-align: center;
  color: yellow;
}

/* Responsive layout - makes the two columns/boxes stack on top of each other instead of next to each other, on small screens */
@media (max-width: 600px) {
  nav, article {
    width: 100%;
    height: auto;
  }
}
</style>
</head>
<body>


<header>
  <h2>Free DevOps Course By Abhishek</h2>
</header>

<section>
  <nav>
    <ul>
      <li><a href="www.youtube.com/@AbhishekVeeramalla">YouTube</a></li>
      <li><a href="www.linkedin.com/in/abhishek-veeramalla-77b33996/">LinkedIn</a></li>
      <li><a href="https://telegram.me/abhishekveeramalla">Telegram</a></li>
    </ul>
  </nav>
  
  <article>
    <h1>Agenda</h1>
    <p>Learn DevOps with strong foundational knowledge and practical understanding</p>
    <p>Please Share the Channel with your friends and colleagues</p>
  </article>
</section>

<footer>
  <p>@AbhishekVeeramalla</p>
</footer>

</body>
</html>
```

## Troubleshooting Kubernetes - minikube and kubectl


### 1. Unhandled Error 
```bash
kubectl cluster-info
```

if you run "kubectl cluster-info" and get following out 


```bash
root@mangesh-OptiPlex-5050:/home/mangesh# kubectl cluster-info
E0906 16:18:26.421648    5929 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://192.168.49.2:8443/api?timeout=32s\": dial tcp 192.168.49.2:8443: connect: no route to host"
E0906 16:18:29.493814    5929 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://192.168.49.2:8443/api?timeout=32s\": dial tcp 192.168.49.2:8443: connect: no route to host"
E0906 16:18:32.565601    5929 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://192.168.49.2:8443/api?timeout=32s\": dial tcp 192.168.49.2:8443: connect: no route to host"
E0906 16:18:35.637620    5929 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://192.168.49.2:8443/api?timeout=32s\": dial tcp 192.168.49.2:8443: connect: no route to host"
E0906 16:18:38.709581    5929 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"https://192.168.49.2:8443/api?timeout=32s\": dial tcp 192.168.49.2:8443: connect: no route to host"

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
Unable to connect to the server: dial tcp 192.168.49.2:8443: connect: no route to host
```

then run following command
1. Delete the minikube
```bash
minikube delete
```

```bash
root@mangesh-OptiPlex-5050:/home/mangesh# minikube delete
🔥  Deleting "minikube" in docker ...
🔥  Deleting container "minikube" ...
🔥  Removing /root/.minikube/machines/minikube ...
💀  Removed all traces of the "minikube" cluster.
```
2. Start minikube  again 

```bash
minikube start --driver=docker --force
```

You will get following output
```bash
root@mangesh-OptiPlex-5050:/home/mangesh# minikube start --driver=docker --force
😄  minikube v1.36.0 on Ubuntu 24.04
❗  minikube skips various validations when --force is supplied; this may lead to unexpected behavior
✨  Using the docker driver based on user configuration
🛑  The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
💡  If you are running minikube within a VM, consider using --driver=none:
📘    https://minikube.sigs.k8s.io/docs/reference/drivers/none/
📌  Using Docker driver with root privileges
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.47 ...
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.33.1 on Docker 28.1.1 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
3. check cluster-info

```bash
kubectl cluster-info
```

```bash
root@mangesh-OptiPlex-5050:/home/mangesh# kubectl cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

```
This link help me to find solution -https://github.com/kubernetes/minikube/issues/8844

### 2. Deleting Deployments

1. If created it using url 

```bash
kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml
```
2. Delete it using only url
```bash
kubectl delete -f https://k8s.io/examples/controllers/nginx-deployment.yaml
```
3. If created it using yml file
```bash
kubectl apply -f pod.yml
```

```bash
kubectl delete -f pod.yml
```

### 3. ImagePullBackOff/ErrImagePull Status

1. if creating any deployment e.g. following pod.yml file from official link : https://kubernetes.io/docs/concepts/workloads/controllers/deployment

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

2. and you had apply it using following command

```bash
kubectl apply -f pod.yml
```

```bash
root@mangesh-OptiPlex-5050:~# kubectl apply -f pod.yml
deployment.apps/nginx-deployment configured
```

3. if find any of ImagePullBackOff/ErrImagePull Error


```bash
root@mangesh-OptiPlex-5050:~# kubectl get pods -w
NAME                                READY   STATUS             RESTARTS   AGE
nginx                               0/1     ImagePullBackOff   0          24h
nginx-deployment-647677fc66-nmhm6   0/1     ImagePullBackOff   0          2d2h
nginx-deployment-647677fc66-nslpm   0/1     ErrImagePull       0          2d2h
nginx-deployment-647677fc66-srj42   0/1     ImagePullBackOff   0          2d2h
nginx-deployment-7d98b54d9b-spqfw   0/1     ImagePullBackOff   0          2d2h
nginx-deployment-647677fc66-nslpm   0/1     ImagePullBackOff   0          2d2h
```
4. use following command to check errors


```bash
oot@mangesh-OptiPlex-5050:~# kubectl describe pod nginx -n default
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sun, 07 Sep 2025 19:52:57 +0530
Labels:           <none>
Annotations:      <none>
Status:           Pending
IP:               10.244.0.27
IPs:
  IP:  10.244.0.27
Containers:
  nginx:
    Container ID:   
    Image:          nginx:1.14.2
    Image ID:       
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ErrImagePull
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-dvt68 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-dvt68:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason          Age                  From     Message
  ----     ------          ----                 ----     -------
  Warning  Failed          22h (x6 over 24h)    kubelet  Failed to pull image "nginx:1.14.2": Error response from daemon: toomanyrequests: You have reached your unauthenticated pull rate limit. https://www.docker.com/increase-rate-limit
  Normal   BackOff         22h (x509 over 24h)  kubelet  Back-off pulling image "nginx:1.14.2"
  Warning  Failed          22h (x509 over 24h)  kubelet  Error: ImagePullBackOff
  Normal   SandboxChanged  5m10s                kubelet  Pod sandbox changed, it will be killed and re-created.
  Normal   Pulling         112s (x3 over 5m9s)  kubelet  Pulling image "nginx:1.14.2"
  Warning  Failed          26s (x3 over 3m59s)  kubelet  Failed to pull image "nginx:1.14.2": error pulling image configuration: download failed after attempts=6: dial tcp: lookup docker-images-prod.6aa30f8b08e16409b46e0173d6de2f56.r2.cloudflarestorage.com on 192.168.49.1:53: server misbehaving
  Warning  Failed          26s (x3 over 3m59s)  kubelet  Error: ErrImagePull
  Normal   BackOff         14s (x3 over 3m58s)  kubelet  Back-off pulling image "nginx:1.14.2"
  Warning  Failed          14s (x3 over 3m58s)  kubelet  Error: ImagePullBackOff
```

### 4. "Failed to pull image "nginx:1.14.2": toomanyrequests: You have reached your unauthenticated pull rate limit"

if you are using following yaml link : https://kubernetes.io/docs/concepts/workloads/controllers/deployment

File name : controllers/nginx-deployment.yaml

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

1. If you apply above deployment file there is issues
 you will get

```bash
kubectl apply -f pod.yml
```
you will get following error "ErrImagePull/ImagePullBackOff"

```bash
root@mangesh-OptiPlex-5050:~# kubectl get pods -w
NAME                                READY   STATUS             RESTARTS   AGE
nginx-deployment-854d56bbc7-9x2rc   0/1     ImagePullBackOff   0          6m12s
nginx-deployment-dd9497b84-n6tzg    0/1     ImagePullBackOff   0          21m
nginx-deployment-dd9497b84-tmrpr    0/1     ImagePullBackOff   0          21m
nginx-deployment-dd9497b84-wlgv2    0/1     ImagePullBackOff   0          21m
```
for this error you will get "Failed to pull image "nginx:1.14.2": Error response from daemon: toomanyrequests: You have reached your unauthenticated pull rate limit. https://www.docker.com/increase-rate-limit"

```bash
Name:             nginx-deployment-f9cb5d648-67dwj
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Mon, 08 Sep 2025 22:00:51 +0530
Labels:           app=nginx
                  pod-template-hash=f9cb5d648
Annotations:      <none>
Status:           Pending
IP:               10.244.0.57
IPs:
  IP:           10.244.0.57
Controlled By:  ReplicaSet/nginx-deployment-f9cb5d648
Containers:
  nginx:
    Container ID:   
    Image:          nginx:1.14.2
    Image ID:       
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ErrImagePull
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     250m
      memory:  256Mi
    Requests:
      cpu:        100m
      memory:     128Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-5v8w8 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-5v8w8:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age   From               Message
  ----     ------     ----  ----               -------
  Normal   Scheduled  15s   default-scheduler  Successfully assigned default/nginx-deployment-f9cb5d648-67dwj to minikube
  Normal   Pulling    14s   kubelet            Pulling image "nginx:1.14.2"
  Warning  Failed     9s    kubelet            Failed to pull image "nginx:1.14.2": Error response from daemon: toomanyrequests: You have reached your unauthenticated pull rate limit. https://www.docker.com/increase-rate-limit
  Warning  Failed     9s    kubelet            Error: ErrImagePull
  Normal   BackOff    8s    kubelet            Back-off pulling image "nginx:1.14.2"
  Warning  Failed     8s    kubelet            Error: ImagePullBackOff

```
This happens because Docker Hub enforces pull rate limits for anonymous users:

100 pulls / 6 hours for unauthenticated users.

200 pulls / 6 hours if logged in with a free Docker account.

Higher limits for Pro/Team accounts.

for fixing this issue 

Authenticate with Docker Hub (Recommended)

Create a Kubernetes Secret with your Docker Hub credentials:

```bash
kubectl create secret docker-registry regcred \
  --docker-username=YOUR_DOCKER_USERNAME \
  --docker-password=YOUR_DOCKER_PASSWORD/TOKEN \
  --docker-email=YOUR_EMAIL

```
Reference the secret in your Deployment:
```bash
spec:
  imagePullSecrets:
    - name: regcred
  containers:
    - name: nginx
      image: nginx:1.14.2
```
File will be like 
```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      imagePullSecrets:
         - name: regcred
      containers:
      - name: nginx
        image: nginx:1.29.1
        ports:
        - containerPort: 80
```
Now apply the yaml check your pods

```bash
```

```bash
```

