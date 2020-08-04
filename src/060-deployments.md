# Deployments

So far we scheduled our pods manually but this way we don't leverage any of the
cool features of Kubernetes, like scaling up and down automatically or self-healing
the system when needed.

A **Deployment** is an object that logically groups pods together according to our
specifications. An example of specification is the number of desired pods that must
run at any given time no matter what. If a pod dies, we want Kubernetes to bring another
one up to replace it; if pods are more than we asked for, we want Kubernetes to
kill as many as needed to match our request.

## Exercise n.1: manage a deployment

> Change directory into the folder `definitions` before running these commands.

Create a **Deployment** called `nginx-prod`:
```sh
$ kubectl apply -f nginx-prod.yaml
deployment.apps/nginx-prod created
```

Now let's ask `kubectl` to list all the objects present in the cluster to see
what happened:
```sh
$ kubectl get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/nginx-prod-d6fd669f4-8tng2   1/1     Running   0          102s
pod/nginx-prod-d6fd669f4-9x57t   1/1     Running   0          102s
pod/nginx-prod-d6fd669f4-fpxqw   1/1     Running   0          102s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-prod   3/3     3            3           102s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-prod-d6fd669f4   3         3         3       102s
```

As you can see there are now 3 pods, 1 deployment and 1 object we haven't seen
yet, a **Replicaset**. A replicaset has the only responsibility to keep a certain
number of pods running at any time; in this case, since our deployment specs
defined 3 replicas for our pod, while creating the deployment Kubernetes
also attached a replicaset to it.

If you run a `kubectl describe` on the replicaset, you'll notice that it is under the
control of our deployment:
```sh
$ kubectl describe replicaset <the name of the replicaset>
Name:           nginx-prod-d6fd669f4
Namespace:      default
<snip>
Controlled By:  Deployment/nginx-prod
<snip>
```

Now let's see the replicaset in action: we're going to kill one pod and see if
another one starts to take its place.
```sh
$ kubectl delete pod <the name of the pod>
pod "the name of the pod" deleted
```

Respawning a new pod is almost instantaneous, so by the time you run this
command again:
```sh
kubectl get pods
```

you'll see that a new pod with a different name has started, bringing back
the pod count to 3.
