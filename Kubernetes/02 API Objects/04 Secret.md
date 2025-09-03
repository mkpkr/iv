```
$ kubectl get secrets
$ kubectl describe secrets <secret-name> #doesn't show values
$ kubectl get secret <secret-name> -o yaml #shows values (base64 encoded)
```

# A Note on Secrets

Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer:
 
	• Not checking-in secret object definition files to source code repositories.
	• Enabling Encryption at Rest for Secrets so they are stored encrypted in ETCD.

Also the way kubernetes handles secrets. Such as:
 
	• A secret is only sent to a node if a pod on that node requires it.
	• Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
	• Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

Having said that, there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, HashiCorp Vault.

# Create a Secret

## Imperative
 
```
kubectl create secret generic <secret-name>  \
--from-literal=<key>=<value> \
--from-literal=<key>=<value>

#or

kubectl create secret generic <secret-name>  \
--from-file=<properties file path>
```

## Declarative
 
Kubernetes secrets are base64 encoded, which allows you to provide binary data (certificates etc.) as secret, and also escape any tricky characters such as " ' \ etc. 

So run `echo -n '<secret-value>' | base64` before putting it in yaml
 
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
 
data:
  DB_Host: bXlzZWNyZXQ=
  DB_User: c29tZXRoaW5n
  DB_Password: ZWxzZQ==
```

# Inject single secret key/value

```yaml
spec:
  containers
  - name: my-container
    image: some-image
    env:
    - name: DB_Password
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: DB_Password
```

# Inject all from secret

```yaml
spec:
  containers
  - name: my-container
    image: some-image
    envFrom:
    - secretRef: 
        name: app-secret
```

# Mount as volume

If we mount a secret as a volume in the pod, each attribute of the secret is created as a file with the value of its secret as its content.

```yaml
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
 
  volumes:
  - name: foo
    secret:
      secretName: mysecret
```