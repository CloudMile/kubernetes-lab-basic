# Lab 07 - Monitoring

## Check metrics

```
kubectl top nodes
```

Output

```
Error from server (NotFound): the server could not find the requested resource (get services http:heapster:)
```

## Enable metrics-server

```
minikube addons list
```

Output

```
- addon-manager: enabled
- dashboard: enabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- gvisor: disabled
- heapster: disabled
- ingress: enabled
- logviewer: disabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
- storage-provisioner-gluster: disabled
```

```
minikube addons enable metrics-server
```

Output

```
✅  metrics-server was successfully enabled
```

Confirm the metrics-server pod is running

```
kubectl get pods --all-namespaces -w
```

Wait a minute...

```
kubectl top nodes
```

```
NAME       CPU(cores)   CPU%      MEMORY(bytes)   MEMORY%
minikube   240m         12%       1652Mi          87%
```

```
kubectl top po --all-namespaces
```

## Install Prometheus-operator

__Install Helm__

```
brew install kubernetes-helm
```

```
helm init
```

Add CoreOS Helm repository

```
helm install stable/prometheus-operator --name prometheus-operator --namespace monitoring
```

Check operator

```
kubectl get pods -n monitoring
```

Proxy

```
kubectl port-forward -n monitoring svc/prometheus-operator-grafana 8080:80
```

Browse http://localhost:8080

```
User: admin
Password: prom-operator
```

![](/img/lab-07-kubernetes-cluster-status.png)

## Clear

```
helm delete prometheus-operator
kubectl delete crd prometheuses.monitoring.coreos.com
kubectl delete crd prometheusrules.monitoring.coreos.com
kubectl delete crd servicemonitors.monitoring.coreos.com
kubectl delete crd alertmanagers.monitoring.coreos.com
kubectl delete namespace monitoring
```