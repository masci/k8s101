# Ingress

In Kubernetes, an **Ingress** is an object that allows access to your Kubernetes
services from outside the Kubernetes cluster.

You might say this is also true for deployments exposed through a service of type
`LoadBalancer`, but there's a big difference: an ingress is not tied to any
particular service.

Similarly to “virtual hosts” on a traditional HTTP server, Kubernetes can use an
ingress as an HTTP-based load balancer to “inspect” the requests and route them
to the right service depending on user's configuration.

An ingress usually consists of an application called _Ingress Controller_ deployed
in the cluster itself. It's important to remember that Kubernetes doesn't ship any
Ingress Controller out of the box, so you will always need to install this additional
component behind an **Ingress** object. There is plenty of Ingress Controllers
available on the market: Nginx, Traefik, Ambassador just to name a few, so you can
pick the one that better suits your use case.

For the scope of this course, we'll see how to deploy Nginx as an Ingress Controller
for our local tiny cluster.

## Exercise n.1: deploy an ingress controller

There is a specific manifest we can use to install Nginx as an Ingress Controller
in a Kind cluster, so the installation step is just:
```sh
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
namespace/ingress-nginx created
<snip>
```

Kubernetes is pretty fast to create the objects needed but the ingress will take
a while to start. To be sure that the cluster is ready, run this command that will
wait until the Ingress Controller is ready:
```sh
$ kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
pod/ingress-nginx-controller-6b69c57f44-8xxpw condition met
```

With the ingress up and running, we can move to the next exercise.

## Exercise n.2

> Change directory into the folder `definitions` before running the commands.

Now that we have an Ingress Controller, let's deploy an ingress resource to route
requests from outside our cluster to 2 different services.

We're going to deploy 2 services exposing 2 pods that will simply echo a string
in response of an HTTP request. The ingress will process the request and forward
urls ending with `/foo` to one service and urls ending with `/bar` to the other
one according to our `rules` specification:
```sh
$ kubectl apply -f ingress-example.yaml
pod/foo-app created
service/foo-service created
pod/bar-app created
service/bar-service created
ingress.extensions/example-ingress created
```

To confirm it's working, run `curl` on localhost:
```sh
$ curl http://localhost/foo
foo
$ curl http://localhost/bar
bar
```

If no rule is matched, Nginx will respond with its notorious 404 page:
```sh
$ curl http://localhost/
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.19.1</center>
</body>
</html>
```
