# Lab 09 - Update Version

```
kubectl create deployment nginx --image=nginx:1.7.9 --save-config
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

__Update image and show status__

```
kubectl set image deployment nginx nginx=nginx:1.9.2
kubectl rollout status deployment nginx
```

__Show history__

```
kubectl rollout history deployment nginx
```

__Recorde changes__

```
kubectl set image deployment nginx nginx=nginx:1.14.0 --record=true
```

Show change cause

```
kubectl rollout history deployment nginx
```

## Rollback

See image version

```
kubectl describe deployment nginx
```

```
kubectl get deployment nginx -ojsonpath="{.spec.template.spec.containers[0].image}"
```

Rollback to previous version

```
kubectl rollout undo deployment nginx
```

See current image version

```
kubectl describe deployment nginx
```

```
kubectl get deployment nginx -ojsonpath="{.spec.template.spec.containers[0].image}"
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
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=5
```

```
kubectl get hpa
```

Output

```
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         10        1          1m
```

__Load Testing__

```
kubectl run -it --rm --restart=Never load-generator --image=busybox /bin/sh

> while true; do wget -q -O- http://php-apache.default.svc.cluster.local; done
```

## Clear

```
kubectl delete deployment php-apache
kubectl delete service php-apache
kubectl delete hpa php-apache
```
