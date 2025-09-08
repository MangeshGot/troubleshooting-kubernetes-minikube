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


### 2. ImagePullBackOff/ErrImagePull Status

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


