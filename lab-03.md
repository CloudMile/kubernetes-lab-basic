# Lab 03 - Deployment Introduction

## Show Deployment

```
kubectl get deployment
```

__In short__

```
kubectl get deploy
```

## Create a Deployment with command

```
kubectl run nginx --image=nginx
```

Output

```
deployment.apps/nginx created
```

__Inspect__

__Show the deployments__

```
kubectl get deployments
```

Output

```
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx     1         1         1            1           2m
```

__Show the replicasets__

```
kubectl get replicasets
```

__In short__

```
kubectl get rs
```

Output

```
NAME               DESIRED   CURRENT   READY     AGE
nginx-64f497f8fd   1         1         1         2m
```

__Show the pods__

```
kubectl get pods
```

Output

```
NAME                     READY     STATUS    RESTARTS   AGE
nginx-64f497f8fd-5d9s7   1/1       Running   0          2m
```

## Generate YAML template with command

```
kubectl run nginx --image=nginx --dry-run -o yaml
```

Output

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

Save YAML

```
kubectl run nginx --image=nginx --dry-run -o yaml > deploy.yaml
```

## Force restart container

Edit `deploy.yaml`

```
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
        version: v0.0.1
```

__Accept the changes__

```
kubectl apply -f deploy.yaml
```

Show the replicasets

```
kubectl get replicasets
```

Show the pod's status

```
kubectl get pods
```


## Try to delete a pod

__Open three terminal to watch changes__

```
kubectl get deployments -w
```

```
kubectl get replicasets -w
```

```
kubectl get pods -w
```

Delete the pod

```
kubectl delete pod $(kubectl get po -o jsonpath='{.items[0].metadata.name}')
```

## Clear

```
kubectl delete deployment nginx
```
