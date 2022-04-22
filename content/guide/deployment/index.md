---
title: "Deployment"
date: 2022-04-22T14:02:45+02:00
draft: false
weight: 15
---

[Creating a pod](../pod) is cool, but it's not that different from just
running the Docker container by hand. Let's try doing something 
slightly more sophisticated.

We'll create a Deployment[^1] which is a "controller" that manages
your pods.

It follows the same basic structure as the Pod YAML but its spec 
is significantly more complicated:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pythonapp-deployment
spec:
  selector:
    matchLabels:
      app: pythonapp
  template:
    metadata:
      labels:
        app: pythonapp
    spec:
      containers:
      - name: pythonapp
        image: pythonapp
        imagePullPolicy: Never
```

So let's look into the `spec` first we have a `selector` which describes 
how the deployment identifies its pods. In case you have multiple 
deployments you don't want them to become confused, so you must always
specify a selector.

Now the `template` section is actually more or less just a pod YAML 
without the `apiVersion` and `kind`. We have added a label that matches 
the selector.

Now save this file as `deployment.yaml` and use it by running:

```bash
kubectl apply -f deployment.yaml
```

Verify with:

```
kubectl get deployment
```

It should return something like this:

```yaml
❯ kubectl get deployment
NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
pythonapp-deployment   1/1     1            1           10m
```

Now, why is this clever? Well, among other things deployments have a 
field in their `spec` called "replicas" which you can set like so:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pythonapp-deployment
spec:
  replicas: 3 # <-- Add this line
  selector:
    matchLabels:
      app: pythonapp
  template:
    metadata:
      labels:
        app: pythonapp
    spec:
      containers:
      - name: pythonapp
        image: pythonapp
        imagePullPolicy: Never
```

And now `kubectl apply -f deployment.yaml` and verify with 
`kubectl get deployment`:

```yaml
❯ kubectl get deployment
NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
pythonapp-deployment   3/3     3            3           12m
```

You can also try `kubectl get pod` and see the 3 pods that have been 
created:

```yaml
❯ kubectl get pod
NAME                                    READY   STATUS    RESTARTS   AGE
pythonapp-deployment-695db994db-7s445   1/1     Running   0          49s
pythonapp-deployment-695db994db-989j6   1/1     Running   0          9m
pythonapp-deployment-695db994db-sfxlh   1/1     Running   0          49s
```

You can also change the scale directly with the `scale` command:

```yaml
kubectl scale deployment/pythonapp-deployment --replicas=5
```

However, now we have an issue.. We have multiple pods and if we want to 
talk to all of them we can't just use `kubectl port-forward` because then
we'd have to forward once for each pod and it would be on different ports.

We'll cover a solution for this in the [Service topic](../service).

[^1]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/