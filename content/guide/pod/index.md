---
title: "Pods"
date: 2022-04-22T14:02:36+02:00
draft: false
weight: 10
---

In Kubernetes, a "Pod"[^1] is the smallest deployable unit possible.
This is kind of an abstract description. It's easiest to think of as
a pod = a container. In advanced Kubernetes usage, this is not always 
true, but for now it is enough.

All Kubernetes deployments are described using a generic YAML format,
which can be quite verbose.

Here is a YAML for creating a pod using [our Docker image](../containers):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pythonapp
spec:
  containers:
  - name: pythonapp
    image: pythonapp
    imagePullPolicy: Never
```

It's quite mouthful isn't it? Well Kubernetes is incredibly flexible but 
that flexibility comes at a cost of complexity. Let's take this file from 
the top.

Most Kubernetes YAML files start with `apiVersion`, `kind`, `metadata` and 
`spec`. 

`apiVersion` and `kind` describe which kind and version of the "pod" object
you are trying to create. If at some point in the future the "pod" object
was changed in an incompatible way, the apiVersion would be bumped version
`v2` and the kind would stay the same, that way Kubernetes can maintain 
backwards compatibility for many versions.

`metadata` is a collection of settings that give you information about the 
created Pod. In this case we are just setting the name, but there is a lot
of options for e.g. tagging your objects in order to identify them easily.

`spec` is where we actually describe the pod object, in this case we 
specify that this pod consists of a single container called "pythonapp" 
using the Docker image "pythonapp".

We also set "imagePullPolicy" to never, this is necessary in order to 
make it use our locally built Docker image and avoid it trying to 
fetch it from the internet instead.

## Deploying the pod
Alright, in order to deploy this pod to Kubernetes: just copy the YAML 
above to a file called "pod.yaml" and run the following command:

```bash
kubectl apply -f pod.yaml
```

Verify that the pod is now running with this command:

```bash
kubectl get pod
```

You should see something like:

```bash 
❯ kubectl get pod
NAME        READY   STATUS    RESTARTS   AGE
pythonapp   1/1     Running   0          5m29s
```

You might notice that [localhost:5000](http://localhost:5000) doesn't 
work. This is because pods are protected from external connection by 
default, if you want to connect to a local pod you can use:

```bash
kubectl port-forward pod/pythonapp 5000:5000
```

And then try [localhost:5000](http://localhost:5000) again!

You can remove the pod by running:

```bash
kubectl delete pod pythonapp 
```

[^1]: https://kubernetes.io/docs/concepts/workloads/pods/