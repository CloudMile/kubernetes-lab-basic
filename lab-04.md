# Lab 04 - Service Introduction

## Create a deployment

```
kubectl create deployment nginx --image=nginx --save-config
```

## Create a service for deployment

```
kubectl expose deployment nginx --port=80
```

Output

```
service/nginx exposed
```

## Show Service

```
kubectl get services
```

__In short__

```
kubectl get svc
```

__Inspect__

```
kubectl describe service nginx
```

Output

```
Name:              nginx
Namespace:         default
Labels:            app=nginx
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP:                10.100.235.182
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         172.17.0.4:80
Session Affinity:  None
Events:            <none>
```

```
kubectl get endpoints
```

Output
```
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   44h
nginx        172.17.0.4:80         66s
```

## Generate YAML template with command

```
kubectl expose deployment nginx --port=80 --name=nginx-svc --dry-run -o yaml > svc.yaml
```

```
kubectl create -f svc.yaml --save-config
```

## Proxy

```
kubectl port-forward service/nginx-svc 8080:80
```

Output

```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

Open browser: http://127.0.0.1:8080/

## NodePort Type

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx-svc
spec:
  type: NodePort # change here
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
status:
  loadBalancer: {}
```

Update service

```
kubectl apply -f svc.yaml
kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        44h
nginx        ClusterIP   10.100.235.182   <none>        80/TCP         3m35s
nginx-svc    NodePort    10.99.85.18      <none>        80:32031/TCP   80s
```

__Test Node port access__

```
SVC_NODEPORT=$(kubectl get svc nginx-svc -o jsonpath='{.spec.ports[0].nodePort}')

curl $(minikube ip):${SVC_NODEPORT}
```

## Specfic the NodePort

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx-svc
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 32123 # change here
  selector:
    app: nginx
status:
  loadBalancer: {}
```

Update service

```
kubectl apply -f svc.yaml
kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        44h
nginx        ClusterIP   10.100.235.182   <none>        80/TCP         7m39s
nginx-svc    NodePort    10.99.85.18      <none>        80:32123/TCP   5m24s
```

## FQDN

```
kubectl run busybox --image=busybox:1.28 --restart=Never -- sleep 3600
```

```
kubectl exec busybox -- nslookup kubernetes
```

## Headless Service

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx-svc
spec:
  clusterIP: None # change it
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
status:
  loadBalancer: {}
```

Update service

```
kubectl delete service nginx-svc
kubectl apply -f svc.yaml
kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   44h
nginx        ClusterIP   10.100.235.182   <none>        80/TCP    10m
nginx-svc    ClusterIP   None             <none>        80/TCP    1s
```

Nslookup

```
kubectl exec busybox -- nslookup nginx-svc
```

Output

```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx-svc
Address 1: 172.17.0.4 172-17-0-4.nginx.default.svc.cluster.local
```

Test

```
kubectl exec busybox -- wget -q -O- 172-17-0-4.nginx.default.svc.cluster.local
```

## Non-selector Service

Edit `svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
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
kubectl delete service nginx-svc
kubectl apply -f svc.yaml
kubectl get services
```

Output

```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   44h
nginx        ClusterIP   10.100.235.182   <none>        80/TCP    13m
nginx-svc    ClusterIP   None             <none>        80/TCP    0s
```

Check Endpoints

```
kubectl describe services nginx-svc
```

Output

```
Name:              nginx-svc
Namespace:         default
Labels:            app=nginx
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"creationTimestamp":null,"labels":{"app":"nginx"},"name":"nginx-svc","nam...
Selector:          <none>
Type:              ClusterIP
IP:                None
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

```
kubectl get endpoints
```

Output

```
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   44h
nginx        172.17.0.4:80         13m
```

Edit `endpoint.yaml`

```
apiVersion: v1
kind: Endpoints
metadata:
  name: nginx-svc
subsets:
- addresses:
  - ip: 172.17.0.4
  ports:
  - port: 80
    protocol: TCP
```

Create Endpoint

```
kubectl create -f endpoint.yaml
```

Check Endpoints

```
kubectl describe services nginx-svc
```

Output

```
Name:              nginx-svc
Namespace:         default
Labels:            app=nginx
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"creationTimestamp":null,"labels":{"app":"nginx"},"name":"nginx-svc","nam...
Selector:          <none>
Type:              ClusterIP
IP:                None
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         172.17.0.4:80
Session Affinity:  None
Events:            <none>
```

```
kubectl get endpoints
```

Output

```
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   44h
nginx        172.17.0.4:80         16m
nginx-svc    172.17.0.4:80         15s
```

## Clear

```
kubectl delete pod busybox
kubectl delete deployment nginx
kubectl delete service nginx
kubectl delete service nginx-svc
```
