To be able to work with more than one Kubernetes cluster, kubectl comes with the concept of contexts. A context is a combination of the following:

- A Kubernetes cluster
- Authentication information for a user
- A default namespace

By default, contexts are saved in the `~/.kube/config` file, but the file can be changed using the KUBECONFIG environment variable.

When a Kubernetes cluster is created in Minikube, a context is created with the same name as the Minikube profile and is then set as the current context. So, kubectl commands that are issued after the cluster is created in Minikube will be sent to that cluster.

To list the available contexts, run the following command:

```
$ kubectl config get-contexts
```

The following is a sample response:

![[Pasted image 20250903204549.png]]

The wildcard, `*`, in the first column marks the current context.

```
$ kubectl config use-context some-cluster
```

To update a context, for example, switching the default namespace used by kubectl, use the kubectl config set-context command.

For example, to change the default namespace of the current context to my-namespace, use the following command:

```
$ kubectl config set-context $(kubectl config current-context) --namespace my-namespace
```