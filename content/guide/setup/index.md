---
title: "Installation"
date: 2022-04-20T09:33:09+02:00
draft: false
weight: 100
---

We will use Docker For Desktop which is free for smaller companies and 
costs a small fee per developer for large companies.
It supports both Mac and Windows.

 * [Install Docker For Desktop on Mac](https://docs.docker.com/desktop/mac/install/)
 * [Install Docker For Desktop on Windows](https://docs.docker.com/desktop/windows/install/)

And then [enable Kubernetes](https://docs.docker.com/desktop/kubernetes/).

Verify that everything is set up and running by running the following 
command in a terminal and check that you get a similar output:

```bash
❯ kubectl get pods -A
NAMESPACE     NAME                                     READY   STATUS    RESTARTS       AGE
kube-system   coredns-78fcd69978-tr6tn                 1/1     Running   1 (80s ago)    18d
kube-system   coredns-78fcd69978-v46pp                 1/1     Running   1 (80s ago)    18d
kube-system   etcd-docker-desktop                      1/1     Running   10 (80s ago)   18d
kube-system   kube-apiserver-docker-desktop            1/1     Running   10 (80s ago)   18d
kube-system   kube-controller-manager-docker-desktop   1/1     Running   10 (80s ago)   18d
kube-system   kube-proxy-9xw2x                         1/1     Running   1 (80s ago)    18d
kube-system   kube-scheduler-docker-desktop            1/1     Running   17 (80s ago)   18d
kube-system   storage-provisioner                      1/1     Running   2 (40s ago)    18d
```

Is it looking alright? Great! Then proceed to [deploying a simple app]()