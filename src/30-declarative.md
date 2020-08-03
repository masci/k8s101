# Going declarative

So far we have operated Kubernetes in an _imperative_ fashion, issuing "orders"
through `kubectl` but this is not the way Kubernetes was designed to be used.

From now on we’ll be using a _declarative_ approach, writing down in `YAML` syntax how we 
want the cluster to be _after_ we run `kubectl apply`.

Such Yaml files are called **definitions**.

## Exercise n.1: get familiar with kubectl apply

> Change directory into the folder `definitions` before running these commands.

Apply the definition named `two-containers-pod.yaml` and see how one of the containers
in the pod will create an `index.html` file that will be then served by the second container 
in the same pod:
```sh
$ kubectl apply -f two-containers-pod.yaml
pod/two-containers-pod created
```

Then let's see with a browser what the Nginx in that pod is serving:
```
kubectl port-forward two-containers-pod 8080:80
```

Open the browser at http://localhost:8080.

Finally, delete all the pods running in the Cluster:

```sh
kubectl delete pods --all
```
