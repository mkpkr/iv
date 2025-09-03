Used by machines/applications (rather than users). A ServiceAccount provides an identity for processes that run in a Pod. A process inside a Pod can use the identity of its associated service account to authenticate to the cluster's API server.

For every service in k8s, a service account named default is created automatically.

```
$ kubectl create serviceaccount <account-name>

$ kubectl get serviceaccount

$ kubectl describe serviceaccount <account-name>
```

This describe gives us the token that should be used by the application.

The token must be used as a bearer token when making calls to the Kubernetes API curl ... --header: "Authorization: Bearer eyjerj"

When a service account is created:

- first the serviceaccount object is created
- then a token is generated
- then a secret is created and the token is stored in that secret
- finally the secret is stored in the service account, accessed through kubectl describe

# Within the Cluster

If the application accessing k8s is hosted on our k8s cluster, this can be simplified further because the pod's service account's token secret is mounted a volume inside the pod.

When the token secret is mounted via a volume, then we don't need to specify it in any requests.

Next section shows you how to set the service account of the pod (and that service account's token secret will be mounted as a volume) but first remember there is a default service account for each namespace. Whenever a pod is created (and serviceAccount isn't specified in definition), that namespace's "default" service account and its token are automatically mounted to that pod as a volume mount.

For example here we can see no volume defined in the pod definition yet a volume is created containing that namespace's default service account.

![[Pasted image 20250903210045.png]]
It is mounted to /var/run/secrets/...

If you ls that dir (`kubectl exec -it <pod> -- ls /var/run/secrets/...`), you will see the secret as 3 separate files): ca.crt, namespace, token.

Then one with the token is called "token" - cat the file `(kubectl exec -it <pod> -- cat /var/run/secrets/.../token`) you will see the token.

# Using a Different Service Account

default only has basic access to the k8s api.
 
Set the service account you want to use (note the exam doesn't cover adding roles/permissions so they're not covered here):
 
```yaml
spec:
  ...
  serviceAccount: <account>
  containers:
    ...
```
That service account's token secret will be mounted as a volume on the pod (which we can see in describe) and you will not need to specify it in requests.
 
# Choose not to Mount Service Account Volume

```yaml
spec:
  ...
  automountServiceAccountToken: false
  containers:
    ...
```
