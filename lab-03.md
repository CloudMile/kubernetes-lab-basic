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
kubectl create deployment nginx --image=nginx --save-config
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
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           11s
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
NAME               DESIRED   CURRENT   READY   AGE
nginx-86c57db685   1         1         1       29s
```

__Show the pods__

```
kubectl get pods
```

Output

```
NAME                     READY   STATUS    RESTARTS   AGE
nginx-86c57db685-5xzkt   1/1     Running   0          49s
```

## Generate YAML template with command

```
kubectl create deployment nginx --image=nginx --save-config --dry-run -o yaml
```

Output

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

Save YAML

```
kubectl create deployment nginx --image=nginx --save-config --dry-run -o yaml > deploy.yaml
```

## Force restart container

Edit `deploy.yaml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
        version: v0.0.1
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
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
