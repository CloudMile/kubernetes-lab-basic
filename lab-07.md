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
|-----------------------------|----------|--------------|
|         ADDON NAME          | PROFILE  |    STATUS    |
|-----------------------------|----------|--------------|
| dashboard                   | minikube | disabled     |
| default-storageclass        | minikube | enabled ✅   |
| efk                         | minikube | disabled     |
| freshpod                    | minikube | disabled     |
| gvisor                      | minikube | disabled     |
| helm-tiller                 | minikube | disabled     |
| ingress                     | minikube | disabled     |
| ingress-dns                 | minikube | disabled     |
| istio                       | minikube | disabled     |
| istio-provisioner           | minikube | disabled     |
| logviewer                   | minikube | disabled     |
| metrics-server              | minikube | disabled     |
| nvidia-driver-installer     | minikube | disabled     |
| nvidia-gpu-device-plugin    | minikube | disabled     |
| registry                    | minikube | disabled     |
| registry-creds              | minikube | disabled     |
| storage-provisioner         | minikube | enabled ✅   |
| storage-provisioner-gluster | minikube | disabled     |
|-----------------------------|----------|--------------|
```

```
minikube addons enable metrics-server
```

Output

```
🌟  The 'metrics-server' addon is enabled
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

## Install Prometheus-operator on minikube

[Prometheus-operator](https://github.com/helm/charts/tree/master/stable/prometheus-operator)

__Reset minikube__

```
minikube stop

minikube start --memory=4096 \
    --extra-config=kubelet.authentication-token-webhook=true \
    --extra-config=kubelet.authorization-mode=Webhook \
    --extra-config=scheduler.address=0.0.0.0 \
    --extra-config=controller-manager.address=0.0.0.0
```

__Create Namespace__

```
kubectl create namespace monitoring
```

__Create Secret for ectd__

We need the cert to access etcd.

```
kubectl create secret generic etcd-certs -nmonitoring \
  --from-literal=ca.crt="$(kubectl exec kube-apiserver-minikube -nkube-system -- cat /var/lib/minikube/certs/etcd/ca.crt)" \
  --from-literal=client.crt="$(kubectl exec kube-apiserver-minikube -nkube-system -- cat /var/lib/minikube/certs/apiserver-etcd-client.crt)" \
  --from-literal=client.key="$(kubectl exec kube-apiserver-minikube -nkube-system -- cat /var/lib/minikube/certs/apiserver-etcd-client.key)"
```

Edit `value.yaml`, remeber change `CHANGE_IT` with the value of `minikube ip`.

This is because the Minikube set the endpoint of etcd as Minikube ip.

```
prometheus:
  prometheusSpec:
    secrets:
      - etcd-certs
prometheusOperator:
  createCustomResource: false
kubeProxy:
  enabled: false
kubeEtcd:
  endpoints:
    - 192.168.99.100 # CHANGE_IT with the value of `minikube ip`
  serviceMonitor:
    scheme: https
    caFile:   /etc/prometheus/secrets/etcd-certs/ca.crt
    certFile: /etc/prometheus/secrets/etcd-certs/client.crt
    keyFile:  /etc/prometheus/secrets/etcd-certs/client.key
```

__Install Helm__

```
$version="helm-v3.1.0"
curl -LO https://get.helm.sh/$version-darwin-amd64.tar.gz
tar -zxvf $version-darwin-amd64.tar.gz

chmod +x darwin-amd64/helm
sudo mv darwin-amd64/helm /usr/local/bin/helm

helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Update Repository

```
helm repo update
```

Install prometheus-operator

```
helm install prometheus-operator \
  stable/prometheus-operator \
  --namespace monitoring \
  --values value.yaml
```

Check operator

```
kubectl get pods -n monitoring
```

__Access Prometheus__

```
kubectl port-forward -n monitoring svc/prometheus-operator-prometheus 9090
```

Browse http://localhost:9090/

Check "Status -> Targets" page.

![](img/lab-07-prometheus-target-status.png)

__Access Grafana__

```
kubectl port-forward -n monitoring svc/prometheus-operator-grafana 8080:80
```

Browse http://localhost:8080/

```
User: admin
Password: prom-operator
```

![](/img/lab-07-kubernetes-cluster-status.png)

## Clear

```
helm uninstall prometheus-operator -n monitoring
kubectl delete crd --all
kubectl delete namespace monitoring
```
