# Experiment 2 run first application in kubernetes

## Steps
1. install minikube
2. install kubectl
3. kubectl run kubia (pod)
4. check pod status
5. expose service

## Details
### install minikube and kubectl
```bash
[thyme@VM-0-11-centos ~]$ uname -a
Linux VM-0-11-centos 5.4.119-19-0009.11 #1 SMP Wed Oct 5 18:41:07 CST 2022 x86_64 x86_64 x86_64 GNU/Linux
[thyme@VM-0-11-centos ~]$ kubectl
-bash: kubectl: command not found

# install minikube on Centos
# https://minikube.sigs.k8s.io/docs/start/
image.png
[thyme@VM-0-11-centos ~]$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 17.1M  100 17.1M    0     0  6216k      0  0:00:02  0:00:02 --:--:-- 6214k
[thyme@VM-0-11-centos ~]$ sudo rpm -Uvh minikube-latest.x86_64.rpm
Preparing...                          ################################# [100%]
	package minikube-1.28.0-0.x86_64 is already installed
[thyme@VM-0-11-centos ~]$ minikube start
😄  minikube v1.28.0 on Tencent 2.4 (amd64)
👎  Unable to pick a default driver. Here is what was considered, in preference order:
    ▪ docker: Not healthy: "docker version --format {{.Server.Os}}-{{.Server.Version}}" exit status 1: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version": dial unix /var/run/docker.sock: connect: permission denied
    ▪ docker: Suggestion: Add your user to the 'docker' group: 'sudo usermod -aG docker $USER && newgrp docker' <https://docs.docker.com/engine/install/linux-postinstall/>
💡  Alternatively you could install one of these drivers:
    ▪ kvm2: Not installed: exec: "virsh": executable file not found in $PATH
    ▪ podman: Not installed: exec: "podman": executable file not found in $PATH
    ▪ vmware: Not installed: exec: "docker-machine-driver-vmware": executable file not found in $PATH
    ▪ virtualbox: Not installed: unable to find VBoxManage in $PATH
    ▪ qemu2: Not installed: exec: "qemu-system-x86_64": executable file not found in $PATH

❌  Exiting due to DRV_NOT_HEALTHY: Found driver(s) but none were healthy. See above for suggestions how to fix installed drivers.

[thyme@VM-0-11-centos ~]$ docker -v
Docker version 20.10.21, build baeda1f
[thyme@VM-0-11-centos ~]$ minikube start
😄  minikube v1.28.0 on Tencent 2.4 (amd64)
✨  Using the docker driver based on user configuration

💣  Exiting due to PROVIDER_DOCKER_NEWGRP: "docker version --format -" exit status 1: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version": dial unix /var/run/docker.sock: connect: permission denied
💡  Suggestion: Add your user to the 'docker' group: 'sudo usermod -aG docker $USER && newgrp docker'
📘  Documentation: https://docs.docker.com/engine/install/linux-postinstall/

# do as the suggestion in error message
[thyme@VM-0-11-centos ~]$ sudo usermod -aG docker $USER && newgrp docker

# retry 
[thyme@VM-0-11-centos ~]$ minikube start
😄  minikube v1.28.0 on Tencent 2.4 (amd64)
✨  Using the docker driver based on user configuration
📌  Using Docker driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.25.3 preload ...
    > preloaded-images-k8s-v18-v1...:  385.44 MiB / 385.44 MiB  100.00% 8.07 Mi
    > gcr.io/k8s-minikube/kicbase:  386.27 MiB / 386.27 MiB  100.00% 4.86 MiB p
    > gcr.io/k8s-minikube/kicbase:  0 B [________________________] ?% ? p/s 47s
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

# install kubectl 
# https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management

# quote the code
`
[thyme@VM-0-11-centos ~]$ cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
> [kubernetes]
> name=Kubernetes
> baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
> enabled=1
> gpgcheck=1
> gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
> EOF
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
`
[thyme@VM-0-11-centos ~]$ sudo yum install -y kubectl
Failed to set locale, defaulting to C
Loaded plugins: auto-update-debuginfo, fastestmirror
Loading mirror speeds from cached hostfile
docker-ce-stable                                         | 3.5 kB     00:00     
epel                                                     | 4.7 kB     00:00     
kubernetes                                               | 1.4 kB     00:00     
tlinux                                                   | 3.6 kB     00:00     
tlinux-extras                                            | 3.6 kB     00:00     
tlinux-os                                                | 3.6 kB     00:00     
tlinux-tkernel4                                          | 3.6 kB     00:00     
tlinux-updates                                           | 3.6 kB     00:00     
(1/2): docker-ce-stable/x86_64/primary_db                  |  88 kB   00:00     
(2/2): kubernetes/x86_64/primary                           | 122 kB   00:00     
kubernetes                                                              903/903
Resolving Dependencies
--> Running transaction check
---> Package kubectl.x86_64 0:1.25.5-0 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package          Arch            Version             Repository           Size
================================================================================
Installing:
 kubectl          x86_64          1.25.5-0            kubernetes           10 M

Transaction Summary
================================================================================
Install  1 Package

Total download size: 10 M
Installed size: 43 M
Downloading packages:
warning: /var/cache/yum/x86_64/2.4/kubernetes/packages/8e05629ff143db9359716c6357bed55d6b9e516a3aace2018b31af1c1cbb1983-kubectl-1.25.5-0.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 3e1ba8d5: NOKEY
Public key for 8e05629ff143db9359716c6357bed55d6b9e516a3aace2018b31af1c1cbb1983-kubectl-1.25.5-0.x86_64.rpm is not installed
8e05629ff143db9359716c6357bed55d6b9e516a3aace2018b31af1c1c |  10 MB   00:00     
Retrieving key from https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
Importing GPG key 0x3E1BA8D5:
 Userid     : "Google Cloud Packages RPM Signing Key <gc-team@google.com>"
 Fingerprint: 3749 e1ba 95a8 6ce0 5454 6ed2 f09c 394c 3e1b a8d5
 From       : https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
** Found 2 pre-existing rpmdb problem(s), 'yum check' output follows:
rdma-core-devel-54mlnx1-1.54310.x86_64 has missing requires of pkgconfig(libnl-3.0)
rdma-core-devel-54mlnx1-1.54310.x86_64 has missing requires of pkgconfig(libnl-route-3.0)
  Installing : kubectl-1.25.5-0.x86_64                                      1/1 
  Verifying  : kubectl-1.25.5-0.x86_64                                      1/1 

Installed:
  kubectl.x86_64 0:1.25.5-0                                                     

Complete!

# check kubectl installed successfully
[thyme@VM-0-11-centos ~]$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

# set alias k=kubectl
[thyme@VM-0-11-centos ~]$ vi .bashrc
[thyme@VM-0-11-centos ~]$ k cluster-info
bash: k: command not found

# source to make setting take effect
[thyme@VM-0-11-centos ~]$ source ~/.bashrc
[thyme@VM-0-11-centos ~]$ k cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

# install bashcompletion
[thyme@VM-0-11-centos ~]$ sudo yum install bash-completion
Failed to set locale, defaulting to C
Loaded plugins: auto-update-debuginfo, fastestmirror
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package bash-completion.noarch 1:2.1-8.tl2 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package                Arch          Version               Repository     Size
================================================================================
Installing:
 bash-completion        noarch        1:2.1-8.tl2           tlinux         86 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 86 k
Installed size: 263 k
Is this ok [y/d/N]: y
Downloading packages:
bash-completion-2.1-8.tl2.noarch.rpm                       |  86 kB   00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 1:bash-completion-2.1-8.tl2.noarch                           1/1 
  Verifying  : 1:bash-completion-2.1-8.tl2.noarch                           1/1 

Installed:
  bash-completion.noarch 1:2.1-8.tl2                                            

Complete!

# config fail, need google, skip this step
[thyme@VM-0-11-centos ~]$ source < (kubectl completion bash)
bash: syntax error near unexpected token (
```

```bash
# run kubia
# --generator is not supported for my latest installation
# in the book, it says with "--generator" it will create replicationcontoller
[thyme@VM-0-11-centos ~]$ k run kubia --image=jinggegeha/kubia --port=8080 --generator=run/v1
error: unknown flag: --generator
See 'kubectl run --help' for usage.
[thyme@VM-0-11-centos ~]$ k run --help
# skip the help doc, result: no --generator 

# k run without --generator, pod created
[thyme@VM-0-11-centos ~]$ k run kubia --image=jinggegeha/kubia --port=8080
pod/kubia created

[thyme@VM-0-11-centos ~]$ k get pods
NAME    READY   STATUS    RESTARTS   AGE
kubia   1/1     Running   0          31m
[thyme@VM-0-11-centos ~]$ 

# expose pod
[thyme@VM-0-11-centos ~]$ k expose pod kubia --type=LoadBalancer --name kubia-http
service/kubia-http exposed
[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          125m
kubia-http   LoadBalancer   10.107.55.224   <pending>     8080:31070/TCP   54s
[thyme@VM-0-11-centos ~]$ k get services
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          126m
kubia-http   LoadBalancer   10.107.55.224   <pending>     8080:31070/TCP   69s

# wait for svc kubia-http external-ip, fail to get the external-ip in about 15 min
[thyme@VM-0-11-centos ~]$ k get services
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          127m
kubia-http   LoadBalancer   10.107.55.224   <pending>     8080:31070/TCP   2m16s

[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          140m
kubia-http   LoadBalancer   10.107.55.224   <pending>     8080:31070/TCP   15m

# delete the two object
[thyme@VM-0-11-centos ~]$ k delete svc kubia-http
service "kubia-http" deleted
[thyme@VM-0-11-centos ~]$ k delete pod kubia
pod "kubia" deleted
[thyme@VM-0-11-centos ~]$ k get pod
No resources found in default namespace.
[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   141m

```

### Run the example on kubernetes doc
```bash

# ------------------------------------------------------------------------------------
# try another example from https://kubernetes.io/docs/tutorials/hello-minikube/

# create deployment
[thyme@VM-0-11-centos ~]$ kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080
deployment.apps/hello-node created
[thyme@VM-0-11-centos ~]$ kubectl get deployments
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   1/1     1            1           11s
kubia        0/1     1            0           2m6s
[thyme@VM-0-11-centos ~]$ kubectl get pods
NAME                         READY   STATUS             RESTARTS      AGE
hello-node-67949d9db-jklp6   1/1     Running            0             25s
kubia-5c865d6fcb-d9hb2       0/1     CrashLoopBackOff   4 (44s ago)   2m20s

# expose service
[thyme@VM-0-11-centos ~]$ kubectl expose deployment hello-node --type=LoadBalancer --port=8080
service/hello-node exposed
[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   11s
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          145m
[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   33s
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          146m

# execute "minikube service hello-node" to get service IP:Port
[thyme@VM-0-11-centos ~]$ minikube service hello-node
|-----------|------------|-------------|---------------------------|
| NAMESPACE |    NAME    | TARGET PORT |            URL            |
|-----------|------------|-------------|---------------------------|
| default   | hello-node |        8080 | http://192.168.49.2:32063 |
|-----------|------------|-------------|---------------------------|
🎉  Opening service default/hello-node in default browser...
👉  http://192.168.49.2:32063

# check svc, still no external-ip
[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   101s
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          147m

# curl service
[thyme@VM-0-11-centos ~]$ curl http://192.168.49.2:32063
NOW: 2022-12-09 16:50:45.541098805 +0000 UTC m=+235.864269825[thyme@VM-0-11-centos ~]$ 

# check svc, still no external-ip
[thyme@VM-0-11-centos ~]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   5m21s
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          151m
[thyme@VM-0-11-centos ~]$ 
```

### Create ReplicationController with yml file

```bash

[thyme@VM-0-11-centos ~]$ cd experiment/

# edit kubia-rc.yml
[thyme@VM-0-11-centos ~/experiment]$ vi kubia-rc.yml

# create replicationcontroller
[thyme@VM-0-11-centos ~/experiment]$ k create -f kubia-rc.yml 
replicationcontroller/kubia created

[thyme@VM-0-11-centos ~/experiment]$ k get pods
NAME                         READY   STATUS    RESTARTS   AGE
hello-node-67949d9db-jklp6   1/1     Running   0          46m
kubia-cztqs                  1/1     Running   0          10s
kubia-kjfpg                  1/1     Running   0          10s
kubia-qstdm                  1/1     Running   0          10s
[thyme@VM-0-11-centos ~/experiment]$ k get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   3         3         3       35s
[thyme@VM-0-11-centos ~/experiment]$ k describe rc kubia
Name:         kubia
Namespace:    default
Selector:     app=kubia
Labels:       app=kubia
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=kubia
  Containers:
   kubia:
    Image:        jinggegeha/kubia
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                    Message
  ----    ------            ----  ----                    -------
  Normal  SuccessfulCreate  99s   replication-controller  Created pod: kubia-qstdm
  Normal  SuccessfulCreate  99s   replication-controller  Created pod: kubia-kjfpg
  Normal  SuccessfulCreate  99s   replication-controller  Created pod: kubia-cztqs

# expose service
[thyme@VM-0-11-centos ~/experiment]$ k expose rc kubia --type=LoadBalancer --name kubia-http
service/kubia-http exposed

[thyme@VM-0-11-centos ~/experiment]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   49m
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          3h14m
kubia-http   LoadBalancer   10.99.46.85    <pending>     8080:30049/TCP   13s
[thyme@VM-0-11-centos ~/experiment]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   49m
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          3h15m
kubia-http   LoadBalancer   10.99.46.85    <pending>     8080:30049/TCP   39s
[thyme@VM-0-11-centos ~/experiment]$ minikube service kubia-http
|-----------|------------|-------------|---------------------------|
| NAMESPACE |    NAME    | TARGET PORT |            URL            |
|-----------|------------|-------------|---------------------------|
| default   | kubia-http |        8080 | http://192.168.49.2:30049 |
|-----------|------------|-------------|---------------------------|
🎉  Opening service default/kubia-http in default browser...
👉  http://192.168.49.2:30049
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
# quote the result
"You've hit kubia-cztqs"

[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:8080
curl: (7) Failed connect to 192.168.49.2:8080; Connection refused

[thyme@VM-0-11-centos ~/experiment]$ k get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.98.54.176   <pending>     8080:32063/TCP   50m
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP          3h16m
kubia-http   LoadBalancer   10.99.46.85    <pending>     8080:30049/TCP   85s

# check the two service use got different URL
[thyme@VM-0-11-centos ~/experiment]$ minikube service hello-node
|-----------|------------|-------------|---------------------------|
| NAMESPACE |    NAME    | TARGET PORT |            URL            |
|-----------|------------|-------------|---------------------------|
| default   | hello-node |        8080 | http://192.168.49.2:32063 |
|-----------|------------|-------------|---------------------------|
🎉  Opening service default/hello-node in default browser...
👉  http://192.168.49.2:32063
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
# quote the result
"You've hit kubia-cztqs"
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:32063
NOW: 2022-12-09 17:39:06.871804663 +0000 UTC m=+3137.194975683[thyme@VM-0-11-centos ~/experiment]$ 

```
### RC, Pod, Service
#### relation
request --port--> (external ip) service (internal ip) --port--> pod(container) -->ReplicationController

One pod may got multi container

#### Role of replicationcontroller
To keep the replicas as expected, if pod disappears, it will create a new one.
If the target replicas number changes, it will adjust to the target number

#### Why we need service
The pod IP is not fixed. It would change if got issue on pod or do migration or scalation
The service keep the visit URL fixed and match the changed pod IP

Generally, the pod IP is not important, but if you would like to check
```bash
[thyme@VM-0-11-centos ~/experiment]$ k get pod -o wide
NAME                         READY   STATUS    RESTARTS   AGE    IP           NODE       NOMINATED NODE   READINESS GATES
hello-node-67949d9db-jklp6   1/1     Running   0          142m   172.17.0.4   minikube   <none>           <none>
kubia-9st8k                  1/1     Running   0          13m    172.17.0.7   minikube   <none>           <none>
kubia-cztqs                  1/1     Running   0          96m    172.17.0.5   minikube   <none>           <none>
kubia-hwbdn                  1/1     Running   0          13m    172.17.0.3   minikube   <none>           <none>
kubia-kjfpg                  1/1     Running   0          96m    172.17.0.6   minikube   <none>           <none>
```

### Easy to scale
 k scale rc kubia --replicas=2
```bash
# scale
[thyme@VM-0-11-centos ~/experiment]$ k scale rc kubia --replicas=2
replicationcontroller/kubia scaled
[thyme@VM-0-11-centos ~/experiment]$ k get rc
NAME    DESIRED   CURRENT   READY   AGE
kubia   2         2         2       81m
[thyme@VM-0-11-centos ~/experiment]$ minikube service kubia-http
|-----------|------------|-------------|---------------------------|
| NAMESPACE |    NAME    | TARGET PORT |            URL            |
|-----------|------------|-------------|---------------------------|
| default   | kubia-http |        8080 | http://192.168.49.2:30049 |
|-----------|------------|-------------|---------------------------|
🎉  Opening service default/kubia-http in default browser...
👉  http://192.168.49.2:30049

# got 2 services
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-cztqs
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-cztqs
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-kjfpg
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-cztqs

# got 4 services
[thyme@VM-0-11-centos ~/experiment]$ k scale rc kubia --replicas=4
replicationcontroller/kubia scaled
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-kjfpg
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-hwbdn
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-kjfpg
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-kjfpg
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
You've hit kubia-kjfpg
[thyme@VM-0-11-centos ~/experiment]$ 
[thyme@VM-0-11-centos ~/experiment]$ curl http://192.168.49.2:30049
、You've hit kubia-9st8k
[thyme@VM-0-11-centos ~/experiment]$ 

```

### Dashboard
```bash
# We don't have it for the experiment
# It will show the URL when there is dashboard, like Google Kubernetes Engine
[thyme@VM-0-11-centos ~/experiment]$ k cluster-info | grep dashboard

# visit minikube dashboard
[thyme@VM-0-11-centos ~/experiment]$ minikube dashboard
🔌  Enabling dashboard ...
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server	


🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:33841/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
👉  http://127.0.0.1:33841/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/

```