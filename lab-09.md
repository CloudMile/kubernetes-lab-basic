# Lab 09 - Update Version

```
kubectl run nginx --image=nginx:1.7.9
```

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

__Update image__

```
kubectl set image deployment nginx nginx=nginx:1.9.2
```

__Show status__

```
kubectl rollout status deployment nginx
```

__Show history__

```
kubectl rollout history deployment nginx
```

## Rollback

```
kubectl describe deployment nginx
```

```
kubectl rollout undo deployment nginx
```

```
kubectl describe deployment nginx
```

## Update image with yaml

```
kubectl get deployment nginx -o yaml > deploy.yaml
```

Edit `deploy.yaml`, and change image version to `nginx:1.14.0`.

```
kubectl apply -f deploy.yaml
```

## Scale manually

```
kubectl scale deployment nginx --replicas=5
```

```
kubectl get pods
```

## Scale with Horizontal Pod Autoscaler(HPA)

```
kubectl run php-apache --image=gcr.io/google_containers/hpa-example \
  --requests=cpu=200m --expose --port=80
```

__Show Status__

```
kubectl top pods
```

## Create HPA

```
kubectl autoscale deployment php-apache --cpu-percent=30 --min=1 --max=10
```

```
kubectl get hpa
```

Output

```
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         10        4          5m
```

__Load Testing__

```
kubectl run -it load-generator --image=busybox /bin/sh

> while true; do wget -q -O- http://php-apache.default.svc.cluster.local; done
```


## Clear

```
kubectl delete deployment php-apache
kubectl delete service php-apache
kubectl delete hpa php-apache
kubectl delete deployment load-generator
```
