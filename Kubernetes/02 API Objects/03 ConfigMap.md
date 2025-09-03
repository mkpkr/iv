Used to pass configuration data in the form of key/value pairs

```
$ kubectl get configmaps
$ kubectl describe configmaps <config-map>
```

# Create Config Map

## Imperative
 
```
kubectl create configmap <config-name> \
--from-literal=<key1>=<value1> \
--from-literal=<key2>=<value2>
 
#or from file containing key=value per line
 
kubectl create configmap <config-name> \
--from-env-file=<file-path>
 
#or from file where name is the key and contents is the value
#can also specify directory and it will iterate all
 
kubectl create configmap <config-name> \
--from-file=<file-path>
```
## Declarative

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
 
data:
  APP_COLOR: blue
  APP_MODE: prod
```

# Inject Single Value

```yaml
spec:
  containers
  - name: my-container
    image: some-image
    env:
    - name: APP_COLOR
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_COLOR        
```

# Inject All

```yaml
spec:
  containers
  - name: my-container
    image: some-image
    envFrom:
    - configMapRef: 
        name: app-config
```

# Volume

It might be preferable to access the key-value pairs from the filesystem of the container.
 
Each attribute of the configmap is created as a file with the value of its configmap as its content.
 
```yaml
containers:
- name: test-container
  image: k8s.gcr.io/busybox
  command: [ "/bin/sh", "-c", "ls /etc/config/" ]
  volumeMounts:
  - name: config-volume
    mountPath: /etc/config
volumes:
- name: config-volume
  configMap:
    name: special-config
```
