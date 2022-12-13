# Pod
Pod can hold multi containers
Pod has its own IP, hostname, process, running app(s)

TODO:
Add details for POD P55


```
[thyme@VM-0-11-centos ~]$ cd experiment/
[thyme@VM-0-11-centos ~/experiment]$ ls
kubia-manual.yaml  kubia-rc.yml
[thyme@VM-0-11-centos ~/experiment]$ vi kubia-manual.yaml 
[thyme@VM-0-11-centos ~/experiment]$ k create -f kubia-manual.yaml 
pod/kubia-manual created
[thyme@VM-0-11-centos ~/experiment]$ k get po kubia-manual -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-12-12T15:27:44Z"
  name: kubia-manual
  namespace: default
  resourceVersion: "185848"
  uid: f51cf9e0-7af4-4905-86ce-b7d94a3141cd
spec:
  containers:
  - image: jinggegeha/kubia
    imagePullPolicy: Always
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-f7c76
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: minikube
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-f7c76
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2022-12-12T15:27:44Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2022-12-12T15:27:48Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2022-12-12T15:27:48Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2022-12-12T15:27:44Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://6a472d010f368569d9eca3a1868793e45d8de7a50d5072c7c6beaa0fd0611691
    image: jinggegeha/kubia:latest
    imageID: docker-pullable://jinggegeha/kubia@sha256:79f07daa53e40d548a7268b5797a71fd9a98a4333fef5fb329692384e97aab2c
    lastState: {}
    name: kubia
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2022-12-12T15:27:48Z"
  hostIP: 192.168.49.2
  phase: Running
  podIP: 172.17.0.10
  podIPs:
  - ip: 172.17.0.10
  qosClass: BestEffort
  startTime: "2022-12-12T15:27:44Z"
[thyme@VM-0-11-centos ~/experiment]$ k get po
NAME                         READY   STATUS    RESTARTS   AGE
hello-node-67949d9db-jklp6   1/1     Running   0          2d22h
kubia-9st8k                  1/1     Running   0          2d20h
kubia-cztqs                  1/1     Running   0          2d21h
kubia-hwbdn                  1/1     Running   0          2d20h
kubia-kjfpg                  1/1     Running   0          2d21h
kubia-manual                 1/1     Running   0          39s
[thyme@VM-0-11-centos ~/experiment]$ 

# port-forward
[thyme@VM-0-11-centos ~/experiment]$ k port-forward kubia-manual 8888:8080
Forwarding from 127.0.0.1:8888 -> 8080
Forwarding from [::1]:8888 -> 8080
Handling connection for 8888


```

with label
```
[thyme@VM-0-11-centos ~/experiment]$ cp kubia-manual.yaml kubia-manual-with-label.yml
[thyme@VM-0-11-centos ~/experiment]$ vi kubia-manual-with-label.yml 
[thyme@VM-0-11-centos ~/experiment]$ k create -f kubia-manual-with-label.yml 
pod/kubia-manual-v2 created
[thyme@VM-0-11-centos ~/experiment]$ k get po --show-labels
NAME                         READY   STATUS    RESTARTS   AGE     LABELS
hello-node-67949d9db-jklp6   1/1     Running   0          2d22h   app=hello-node,pod-template-hash=67949d9db
kubia-9st8k                  1/1     Running   0          2d20h   app=kubia
kubia-cztqs                  1/1     Running   0          2d22h   app=kubia
kubia-hwbdn                  1/1     Running   0          2d20h   app=kubia
kubia-kjfpg                  1/1     Running   0          2d22h   app=kubia
kubia-manual                 1/1     Running   0          16m     <none>
kubia-manual-v2              1/1     Running   0          13s     creation_method=manual,env=prod

# add label
[thyme@VM-0-11-centos ~/experiment]$ k label po kubia-manual creation_method=manual
pod/kubia-manual labeled
[thyme@VM-0-11-centos ~/experiment]$ k get po --show-labels
NAME                         READY   STATUS    RESTARTS   AGE     LABELS
hello-node-67949d9db-jklp6   1/1     Running   0          2d22h   app=hello-node,pod-template-hash=67949d9db
kubia-9st8k                  1/1     Running   0          2d20h   app=kubia
kubia-cztqs                  1/1     Running   0          2d22h   app=kubia
kubia-hwbdn                  1/1     Running   0          2d20h   app=kubia
kubia-kjfpg                  1/1     Running   0          2d22h   app=kubia
kubia-manual                 1/1     Running   0          17m     creation_method=manual
kubia-manual-v2              1/1     Running   0          95s     creation_method=manual,env=prod

# update label
[thyme@VM-0-11-centos ~/experiment]$ k label po kubia-manual-v2 env=debug --overwrite
pod/kubia-manual-v2 labeled
[thyme@VM-0-11-centos ~/experiment]$ k get po --show-labels
NAME                         READY   STATUS    RESTARTS   AGE     LABELS
hello-node-67949d9db-jklp6   1/1     Running   0          2d22h   app=hello-node,pod-template-hash=67949d9db
kubia-9st8k                  1/1     Running   0          2d20h   app=kubia
kubia-cztqs                  1/1     Running   0          2d22h   app=kubia
kubia-hwbdn                  1/1     Running   0          2d20h   app=kubia
kubia-kjfpg                  1/1     Running   0          2d22h   app=kubia
kubia-manual                 1/1     Running   0          18m     creation_method=manual
kubia-manual-v2              1/1     Running   0          2m14s   creation_method=manual,env=debug

# label selection
[thyme@VM-0-11-centos ~/experiment]$ k get po -l creation_method=manual
NAME              READY   STATUS    RESTARTS   AGE
kubia-manual      1/1     Running   0          54m
kubia-manual-v2   1/1     Running   0          38m
[thyme@VM-0-11-centos ~/experiment]$ k get po -l env
NAME              READY   STATUS    RESTARTS   AGE
kubia-manual-v2   1/1     Running   0          38m

# remember add quotes here
[thyme@VM-0-11-centos ~/experiment]$ k get po -l '!env'
NAME                         READY   STATUS    RESTARTS   AGE
hello-node-67949d9db-jklp6   1/1     Running   0          2d23h
kubia-9st8k                  1/1     Running   0          2d21h
kubia-cztqs                  1/1     Running   0          2d22h
kubia-hwbdn                  1/1     Running   0          2d21h
kubia-kjfpg                  1/1     Running   0          2d22h
kubia-manual                 1/1     Running   0          54m

# failed one
[thyme@VM-0-11-centos ~/experiment]$ k get po -l !env
-bash: !env: event not found

# GPU
[thyme@VM-0-11-centos ~/experiment]$ cp kubia-manual.yaml kubia-gpu.yml
[thyme@VM-0-11-centos ~/experiment]$ vi kubia-gpu.yml 
[thyme@VM-0-11-centos ~/experiment]$ k get ns
NAME                   STATUS   AGE
default                Active   3d2h
kube-node-lease        Active   3d2h
kube-public            Active   3d2h
kube-system            Active   3d2h
kubernetes-dashboard   Active   2d21h


```

## delete pod
- delete by name
- delete by lable