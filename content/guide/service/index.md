---
title: "Service"
date: 2022-04-22T14:02:53+02:00
draft: false
weight: 20
---

In Kubernetes the term "Service"[^1] is used for loadbalancers inside the cluster.
In this section, we will explore how to communicate with multiple pods using
these loadbalancers so you can communicate with any of pods through a single
interface.

To begin with, modify your Python application:

```python
import signal
import socket
import sys

from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():
    return f"Hello World! My name is {socket.gethostname()}"


def terminate(signal, frame):
    sys.exit(0)


if __name__ == "__main__":
    # Workaround for Flask not always respecting sigterm
    signal.signal(signal.SIGTERM, terminate)

    app.run(host='0.0.0.0')
```

And run the [`docker build`](../containers) command again:

```yaml
docker build -t pythonapp -f Dockerfile .
```

Kubernetes will not automatically detect that your Docker image has been
changed, but you can ask the `Deployment` to rollout a new set of pods
using this command: 
```yaml
kubectl rollout restart deployment pythonapp-deployment
```

Now we should be able to see which pod we hit when we contact the pod. 
We just need a way to actually contact our pods. We'll create a
NodePort Service[^2] to make contact with our pods.

Once again we'll use a Kubernetes YAML definition:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: pythonapp
spec:
  type: NodePort
  selector:
    app: pythonapp
  ports:
    - port: 5000
      targetPort: 5000
      nodePort: 30005
```

This `spec` has the type `NodePort` which means we will expose the service
on a port on this node (your machine). Then we give it a `selector` which,
like with [deployments](../deployment), is how we "select" the pods we
want. In this case we want all pods with the label "app" set to "pythonapp".

Then the Service has the `ports` which is simply a way to say that port
`5000` on the service maps to port `5000` on the target (our pod). In 
this case we also specify that the port `30005` on our "node" will contact
this service on port `5000`.

If you save this YAML to a file called `service.yaml` and run `kubectl 
apply -f service.yaml` then you should be able to go to 
[localhost:30005](http://localhost:30005) and see that you reach one 
of your pods.

[^1]: https://kubernetes.io/docs/concepts/services-networking/service/
[^2]: https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport