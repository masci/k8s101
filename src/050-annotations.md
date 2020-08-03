# Annotations

**Annotation**s are very similar to labels, as they are key/value pairs, with a
big difference: annotations can’t be used to query or filter Kubernetes objects.
Annotations are meant to carry on information used by other softwares, like auditing
or monitoring.

> TIP: when in doubt, use annotations and “promote” them to labels later, if and when
  you need to use them in a query.

## Exercise n.1: can't query

Assuming the pod called `foo` from the previous unit is still running, we
can attach to it an annotation named `version` with a value of `1` by running:
```sh
$ kubectl annotate pods foo version=1
pod/foo annotated
```

Despite the annotation, the filter won't work this time and `foo` won't be
listed:
```sh
$ kubectl get pods --selector="version=1"
No resources found in default namespace.
```

Clean up all the pods before moving to the next step:
```sh
$ kubectl delete pods --all
pod "bar" deleted
pod "foo" deleted
```
