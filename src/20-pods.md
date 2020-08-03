# Pods

A **Pod** is the minimum deployable unit on kubernetes and consists of one or more 
Docker containers. You can’t “run a container” in a Kubernetes cluster but you 
can schedule and “run a pod”.

If we keep using the Operating System metaphor, a pod would be a process
and its containers would be threads: each container has its own life 
(for example it has separated cgroups) but at the same time it's strongly coupled 
with the other containers in the pod by having shared namespaces, same hostname, 
same IP address and same open ports.

It's important to note that a pod is atomic and it always runs on one node of the 
Cluster. In other words, all the containers in a pod always lands in the same 
Node of the Cluster.

## Exercise n.1: create a Pod

First of all, create a pod called `nginx-pod` running a single container and 
pulling the `nginx` image from Dockerhub:
```sh
$ kubectl run nginx-pod --generator=run-pod/v1 --image=nginx
Flag --generator has been deprecated, has no effect and will be removed in the future.
pod/nginx-pod created
```

> Please ignore the deprecation warning for now, this command is only meaningful in the
> context of this course and you wouldn't use it in a real world scenario.

Now to get the list of running pods run:
```sh
$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          104s
```

As we mentioned in the previous unit, we can get a lot more info about an Object
using `describe`, let's do it with our pod:

```sh
$ kubectl describe pod nginx-pod
Name:         nginx-pod
Namespace:    default
...
```

Finally, we can delete the pod:

```sh
$ kubectl delete pods nginx-pod
pod "nginx-pod" deleted
```

## Exercise n.2: interact with a Pod

Let's create the same pod again but this time we expose its TCP port 
number 80:

```sh
kubectl run nginx --generator=run-pod/v1 --image=nginx --port 80
```

The port is only exposed within the **Cluster** but there's a way to easily
open a connection tunnel to our local host at the port 8080 by running:

```sh
$ kubectl port-forward nginx-pod 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80kubectl port-forward nginx-pod 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

The tunnel will stay open until you hit CTRL+C, in the meantime you can open the 
browser at http://localhost:8080 and see the pod serving requests.

Last but not least, we can also get logs for our pod by running:

```sh
kubectl logs nginx
```
