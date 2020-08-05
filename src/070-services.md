# Services

A **Service** is an object providing network access to a deployment, the
most common use case being exposing it to the Internet. _Expose_ is also the
jargon used in `kubectl`, where you use a command called `expose` to create
a service.

## Exercise n.1: expose a deployment

Assuming the `nginx-prod` deployment from the previous unit is still running,
we create a new service by exposing it:

```sh
$ kubectl expose deployment nginx-prod
service/nginx-prod exposed
```

As anticipated, under the hood Kubernetes created the corresponding service object:
```sh
$ kubectl get services
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
nginx-prod   ClusterIP   10.104.193.191   <none>        80/TCP    5s
```

Note the newly created service has `ClusterIP` as its `TYPE` and `<none>` as the
`EXTERNAL-IP` value, meaning the corresponding deployment is only exposed internally
within the cluster and won't be reachable from the Internet. A service can be created
in several different flavors, called `types`. The default type is `ClusterIP` and
it doesn't provide any routing facility to let the service be accessed from the outside,
reason why you see the external IP is `<none>`.

Let's delete this service and try exposing the deployment with a different service type,
`LoadBalancer`:
```sh
$ kubectl delete service nginx-prod
service "nginx-prod" deleted
$ kubectl expose deployment nginx-prod --type=LoadBalancer
service/nginx-prod exposed
```

This time, something seems to happen as you can see from:
```sh
kubectl get services
```

EXTERNAL-IP is now `<pending>` and if your cluster is configured to do so,
eventually you'll find a real public IP address listed there and you'll be able
to access the service from a host outside the cluster. This is because Kubernetes
alone doesn't provide any facility to implement an actual Load Balancer, so it's
up to you as the cluster admin to set up the last mile (or your cloud provider).

For the scope of the course we can stop here, state will remain forever `<pending>`
but it's not important at this stage, we'll get back to this later, when we'll
introduce the **Ingress** object.
