# First contact

Kubernetes is an orchestrator for deploying containers but you can think about it
as an Operating System running your programs. While it's good to know how an
Operating System works (and you should keep learning Kubernetes internals
beyond this course), as long as you know how to install, upgrade and verify your
programs are running fine, that'll be enough.

You will always see Kubernetes as a single component but under the hood it runs on a
**Cluster** of machines called **Nodes**. More nodes means more availability.
Bigger nodes means more memory and CPU available to your programs.

Every **Object** managed by Kubernetes is represented by a RESTful resource and we'll
see how, at the end of the day, `kubectl` is just a smart HTTP client.

For the scope of this course, we won't need a fully functional multi-node Kubernetes cluster.
Instead, we'll be using Kind. [KIND](https://kind.sigs.k8s.io/) stands for
_Kubernetes IN Docker_ and it's capable of managing local clusters of one node orchestrating a
bunch of Docker containers. At this stage, feel free to ignore the internals and look at Kind
for what it is, a tool to start/stop Kubernetes clusters.

Another tool you must get familiar with is `kubectl`, the CLI tool you'll be using to _do stuff_
on any Kubernetes cluster, whatever it's local or a production system. One caveat about `kubectl`,
you need to double check the version you have in your system is compatible with the version of
Kubernetes that runs in the cluster: client and server can be at most 1 minor version distant
from each other. In our case, the versions are pinned so as long as you've followed the [setup
instructions](index.md) you should be good.

In this unit we'll start a local Cluster and we'll explore its components with `kubectl`.

## Exercise n.1: start the Cluster

If `kind` is correctly installed, all you have to do is running this command from the root of
the repo:
```sh
$ kind create cluster --name k8s101 --image kindest/node:v1.18.2 --config kind-config.yaml
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.18.2) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Have a nice day! 👋
```

Notice we use a specific image (and not latest) so that we know it'll work with the pinned
`kubectl` version. If you see no errors the default Cluster called `kind` should be ready
to use, and you can confirm it by running:
```sh
$ kind get clusters
kind
```

## Exercise n.2: explore the Cluster

To get informations about the Cluster, just run
```sh
kubectl cluster-info
```

To prove that everything is a RESTful resource in Kubernetes, you can increase
`kubectl`'s log verbosity and see how it performs HTTP calls under the hood:
```sh
kubectl cluster-info -v6
```

We can query the status of few key components of the Cluster:
```sh
kubectl get componentstatuses
```

And we can also get the list of all the nodes composing the Cluster:
```sh
kubectl get nodes
```

The more detailed, more structured version of `kubectl get` is the command
`describe`, that gives us a comprehensive view of the Objects we query, in this
case the nodes of the Cluster:
```sh
kubectl describe nodes
```
