# Lab 04 - Service Introduction

## Create a deployment

```
$ kubectl run nginx --image=nginx --port=80
```

## Create a service for deployment

```
$ kubectl expose deployment nginx
```

Output

```
service/nginx exposed
```

## Show Service

```
$ kubectl get services
```

__In short__

```
$ kubectl get svc
```

__Inspect__

```
$ kubectl describe service nginx
```

Output

```
Name:              nginx
Namespace:         default
Labels:            run=nginx
Annotations:       <none>
Selector:          run=nginx
Type:              ClusterIP
IP:                10.98.135.68
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         172.17.0.7:80
Session Affinity:  None
Events:            <none>
```

```
$ kubectl get endpoints
```

Output
```
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   19h
nginx        172.17.0.7:80         6m
```

## Generate YAML template with command

```
$ kubectl expose --dry-run deployment nginx --name=nginx-svc -o yaml > svc.yaml
```

```
$ kubectl create -f svc.yaml
```

## Proxy

```
$ kubectl port-forward service/nginx-svc 8080:80
```

## NodePort Type

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx-svc
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
status:
  loadBalancer: {}
```

Update service

```
$ kubectl apply -f svc.yaml
$ kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        19h
nginx        ClusterIP   10.98.135.68    <none>        80/TCP         15m
nginx-svc    NodePort    10.107.140.99   <none>        80:32630/TCP   5m
```

__Test Node port access__

```
$ curl $(minikube ip):32630
```

## Specfic the NodePort

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx-svc
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 32123
  selector:
    run: nginx
status:
  loadBalancer: {}
```

Update service

```
$ kubectl apply -f svc.yaml
$ kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        19h
nginx        ClusterIP   10.98.135.68    <none>        80/TCP         25m
nginx-svc    NodePort    10.107.140.99   <none>        80:32123/TCP   15m
```

## FQDN

```
$ kubectl run busybox --image=busybox:1.28 --restart=Never -- sleep 3600
```

```
$ kubectl exec busybox -- nslookup kubernete
```

## Headless Service

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx-svc
spec:
  clusterIP: None
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
status:
  loadBalancer: {}
```

Update service

```
$ kubectl delete service nginx-svc
$ kubectl apply -f svc.yaml
$ kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   19h
nginx        ClusterIP   10.98.135.68   <none>        80/TCP    48m
nginx-svc    ClusterIP   None           <none>        80/TCP    6s
```

Nslookup

```
$ kubectl exec busybox -- nslookup nginx-svc
```

Output

```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-svc
Address 1: 172.17.0.7 172-17-0-7.nginx.default.svc.cluster.local
```

Test

```
$ kubectl exec busybox -- wget -q -O- 172-17-0-7.nginx.default.svc.cluster.local
```

## Non-selector Service

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx-svc
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
status:
  loadBalancer: {}
```

```
$ kubectl delete service nginx-svc
$ kubectl apply -f svc.yaml
$ kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   19h
nginx        ClusterIP   10.98.135.68    <none>        80/TCP    1h
nginx-svc    ClusterIP   10.100.94.111   <none>        80/TCP    4s
```

Check Endpoints

```
$ kubectl get endpoints
```

Output

```
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   19h
nginx        172.17.0.7:80         1h
```

Edit `endpoint.yaml`

```
apiVersion: v1
kind: Endpoints
metadata:
  name: nginx-svc
subsets:
- addresses:
  - ip: 172.17.0.7
  ports:
  - port: 80
    protocol: TCP
```

Check Endpoints

```
$ kubectl get endpoints
```

Output

```
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   20h
nginx        172.17.0.7:80         1h
nginx-svc    172.17.0.7:80         10s
```

## Clear

```
$ kubectl delete pod busybox
$ kubectl delete deployment nginx
$ kubectl delete service nginx
$ kubectl delete service nginx-svc
```
