# Lab 08 - Logging

```
$ kubectl run --dry-run busybox --image=busybox:1.28 \
  --restart=Never -o yaml > log.yaml
```

Edit `log.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox:1.28
    imagePullPolicy: IfNotPresent
    name: busybox
    resources: {}
    command:
    - "/bin/sh"
    args:
    - "-c"
    - "while true; do echo 'Hello world.'; sleep 1; done"
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```
$ kubectl logs -f busybox
```

## See log on Minikube

```
$ minikube ssh
```

```
$ ls /var/log/container
```

```
$ sudo tail /var/log/container/CONTAINER_LOG
```

```
$ exit
```

## Clear

```
$ kubectl delete pod busybox
```
