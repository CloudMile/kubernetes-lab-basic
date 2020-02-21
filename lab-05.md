# Lab 05 - ConfigMap and Secret

## Create ConfigMaps

```
kubectl create configmap dummy-config --from-literal=foo=bar
```

## Show ConfigMaps

```
kubectl get configmaps
```

__In short__

```
kubectl get cm
```

__Inspet__

```
kubectl describe configmaps dummy-config
```

Output

```
Name:         dummy-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
foo:
----
bar
Events:  <none>
```

## Create ConfigMaps with multi values

```
kubectl delete configmap dummy-config
```

```
kubectl create configmap dummy-config \
  --from-literal=k1=value1 \
  --from-literal=k2=value2 \
  --from-literal=k3=value3
```

```
kubectl describe configmaps dummy-config
```

Output

```
Name:         dummy-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
k1:
----
value1
k2:
----
value2
k3:
----
value3
Events:  <none>
```

```
kubectl delete configmap dummy-config
```

## Create ConfigMaps with file

```
echo 'hello world' > welcome.txt
```

```
kubectl create configmap dummy-config --from-file=welcome.txt
```

```
kubectl describe configmaps dummy-config
```

Output

```
Name:         dummy-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
welcome.txt:
----
hello world

Events:  <none>
```

```
kubectl delete configmap dummy-config
```

## Generate YAML template with command

```
kubectl create configmap dummy-config --from-file=welcome.txt \
  --dry-run -o yaml > configmap.yaml
```

Edit `configmap.yaml`

```
apiVersion: v1
data: # Change data
  welcome: |
    Hello World!!!
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: dummy-config
```

```
kubectl create -f configmap.yaml
```

```
kubectl describe configmaps dummy-config
```

Output

```
Name:         dummy-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
welcome:
----
Hello World!!!

Events:  <none>
```

## Use configmap as environment variable

```
kubectl run busybox --image=busybox:1.28 --restart=Never --dry-run -o yaml > env.yaml
```

Edit `env.yaml`

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
    - "sleep"
    - "3600"
    env:
    - name: WELCOME
      valueFrom:
        configMapKeyRef:
          name: dummy-config
          key: welcome

  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```
kubectl create -f env.yaml
```

```
kubectl exec busybox env
```

```
kubectl delete -f env.yaml
```

----

## Create Secret

```
kubectl create secret generic dummy-secret --from-literal=foo=bar
```

## Show Secret

```
kubectl get secrets
```

```
kubectl describe secrets dummy-secret
```

Output

```
Name:         dummy-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
foo:  3 bytes
```

## Get Secret value

```
kubectl get secrets dummy-secret -o yaml
```

Output

```
apiVersion: v1
data:
  foo: YmFy
kind: Secret
metadata:
  creationTimestamp: "2020-02-21T09:43:43Z"
  name: dummy-secret
  namespace: default
  resourceVersion: "22855"
  selfLink: /api/v1/namespaces/default/secrets/dummy-secret
  uid: ad30a806-6cf6-4148-a999-5fccf1a04417
type: Opaque
```

```
echo -n "YmFy" | base64 -D
```

Output

```
bar
```

## Create secret with YAML template

Base64 encode value

```
echo -n 'Hello World!!!' | base64
```

Output

```
SGVsbG8gV29ybGQhISE=
```

Edit `secret.yaml`

```
apiVersion: v1
data:
  welcome: SGVsbG8gV29ybGQhISE=
kind: Secret
metadata:
  name: dummy-secret
```

__Create secret__

```
kubectl apply -f secret.yaml
```

```
kubectl describe secret dummy-secret
```

Output

```
Name:         dummy-secret
Namespace:    default
Labels:       <none>
Annotations:
Type:         Opaque

Data
====
foo:      3 bytes
welcome:  14 bytes
```

## Use secret as environment variable

Edit `env.yaml`

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
    - "sleep"
    - "3600"
    env:
    - name: WELCOME
      valueFrom:
        secretKeyRef:
          name: dummy-secret
          key: welcome

  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

```
kubectl create -f env.yaml
```

```
kubectl exec busybox env
```

```
kubectl delete -f env.yaml
```

## Clear

```
kubectl delete secret dummy-secret
```
