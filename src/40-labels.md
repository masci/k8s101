# Labels

**Labels** are simple key/value pairs attached to any Kubernetes object and used
at runtime to query and filter such objects. Labels are heavily used by Kubernetes i
tself as we’ll see in a moment.

## Exercise n.1: list labels and use them in queries

> Change directory into the folder `definitions` before running these commands.

Start two pods with some labels attached (see their definition files for details):
```sh
kubectl apply -f foo-pod.yaml
kubectl apply -f bar-pod.yaml
```

You can see the attached labels for each pod by running:
```sh
$ kubectl get pods --show-labels
NAME   READY   STATUS    RESTARTS   AGE   LABELS
bar    1/1     Running   0          48s   env=staging
foo    1/1     Running   0          53s   env=prod
```

Now let's see how to list only pods having a label `env` with `prod` as value.
To use a label in order to filter the output of `kubectl get pods`, we pass the
option `--selector` to `kubectl`. You can keep the `--show-labels` flag to double
check the query is working as expected:
```sh
$ kubectl get pods --selector="env=prod" --show-labels
NAME   READY   STATUS    RESTARTS   AGE     LABELS
foo    1/1     Running   0          2m19s   env=prod
```

In case you want to see any pod with the label `env`, whatever its value:
```sh
kubectl get pods --selector="env" --show-labels
```

Queries can be negated with the `!` operator:
```sh
kubectl get pods --selector="\!env" --show-labels
No resources found in default namespace.
```
