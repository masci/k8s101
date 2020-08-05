# Secrets

**Secret** resources are very similar to **ConfigMap** but they were introduced
later, with the goal to store sensitive informations and you should consider this
feature still a work in progress, at least in vanilla instance of Kubernetes.
In fact at this time Kubernetes secrets are stored in plain-text, base64 encoded
format.

It's important to stress the fact that **Encoding is not Encryption**: despite the
name and the original design, Kubernetes' secrets can't be trusted to store
sensitive informations without providing some kind of encryption on top of them.
Some cloud providers encrypt Kubernetes secrets at the application layer, see
for example [GKE](https://cloud.google.com/kubernetes-engine/docs/how-to/encrypting-secrets).

## Exercise n.1: create a secret with kubectl

Same as config maps, there are several options to create a secret, for brevity
we'll pass our secret strings to `kubectl`:
```sh
$ kubectl create secret generic my-secret --from-literal=username=superuser --from-literal=password=topsecret
secret/my-secret created
```

You can see what's in your secret by running:
```sh
$ kubectl describe secret my-secret
Name:         my-secret
<snuip>
Data
====
username:  9 bytes
password:  9 bytes
```

As you can see, differently from a config map we can't see the plain text version of our data
because `kubectl` encoded the literals in base64 on the fly.

## Exercise n.2: create a secret manually

If we want to manually create a secret, we have to handle encoding ourselves:
```sh
$ echo -n 'admin' | base64
YWRtaW4=
```

Then we can use the encoded value (e.g.) in a yaml definition like this:
```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
```

And create the object:
```sh
$ cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
EOF
secret/mysecret created
```