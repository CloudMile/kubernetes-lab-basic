# Lab - Label and annotation

## Show labels

```
$ kubectl get nodes --show-labels
```

```
$ kubectl get pods --show-labels
```

## Add labels

```
$ kubectl run nginx01 --image=nginx --restart=Never
$ kubectl run nginx02 --image=nginx --restart=Never
$ kubectl run nginx03 --image=nginx --restart=Never
```

```
$ kubectl get pods --show-labels
```

Output

```
NAME      READY     STATUS    RESTARTS   AGE       LABELS
nginx01   1/1       Running   0          18s       run=nginx01
nginx02   1/1       Running   0          15s       run=nginx02
nginx03   1/1       Running   0          11s       run=nginx03
```

```
$ kubectl label po nginx01 env=dev
$ kubectl get pods --show-labels
```

Output

```
NAME      READY     STATUS    RESTARTS   AGE       LABELS
nginx01   1/1       Running   0          25s       env=dev,run=nginx01
nginx02   1/1       Running   0          2m        run=nginx02
nginx03   1/1       Running   0          2m        run=nginx03
```

```
$ kubectl label po nginx02 env=prod tier=backend
$ kubectl get pods --show-labels
```

Output

```
NAME      READY     STATUS    RESTARTS   AGE       LABELS
nginx01   1/1       Running   0          2m        env=dev,run=nginx01
nginx02   1/1       Running   0          4m        env=prod,run=nginx02,tier=backend
nginx03   1/1       Running   0          4m        run=nginx03
```

## Remove labels

```
$ kubectl label po nginx02 tier-
```

```
$ kubectl get pods --show-labels
```

Output

```
NAME      READY     STATUS    RESTARTS   AGE       LABELS
nginx01   1/1       Running   0          2m        env=dev,run=nginx01
nginx02   1/1       Running   0          4m        env=prod,run=nginx02
nginx03   1/1       Running   0          4m        run=nginx03
```

## Select resource with labels

```
kubectl get pods --show-labels -l env=dev
```

Output

```
NAME      READY     STATUS    RESTARTS   AGE       LABELS
nginx01   1/1       Running   0          9m        env=dev,run=nginx01
```

----

## Show annotation

```
$ kubectl describe pod nginx03
```

## Add annotation

```
$ kubectl annotate pod nginx03 app=search
```

```
$ kubectl describe pod nginx03
```

## Remove annotation

```
$ kubectl annotate pod nginx03 app-
```

```
$ kubectl describe pod nginx03
```

## Clear

```
$ kubectl delete po -l run
```