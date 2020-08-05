# Config Maps

Config maps are a feature of Kubernetes we can use to store configuration
parameters that pods running on the cluster can access at runtime.  A config
map can be filled with data coming from different sources:

* A Yaml dictionary in a `.yaml` file
* A file on disk containing one key per line with the format: `my.settings.key=myval`
* A directory containing configuration files in the same format as previous bullet
* From the command line as args for `kubectl`

Once a **ConfigMap** resource containing our data is created, a pod can use it in two
different ways:

1. By mounting a volume (a file will be created in the pod’s volume for each config key)
1. By injecting environment variables in the pod, each value will be stored in an env var
   named after the key

## Exercise n.1: config a Pod

> Change directory into the folder `definitions` before running the commands.

First of all, we need to create the **ConfigMap** resource:
```sh
kubectl apply -f myconfig.yaml
```

You can inspect the contents of the config map by running:
```sh
kubectl describe configmap my-config
```

We can now use it from a pod by mounting a volume (see the definition file):
```sh
kubectl apply -f nginx-configmap-pod.yaml
```

At this point, we can verify the pod can actually access the configuration
values:
```sh
$ kubectl exec -it nginx-configmap -- bash
$$ ls /etc/config
password  username
$$ cat /etc/config/username ; echo
root
$$ cat /etc/config/password ; echo
mypass
```
