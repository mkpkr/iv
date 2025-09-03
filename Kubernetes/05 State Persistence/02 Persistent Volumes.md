We want an administrator to configure a large pool of storage and users carve out pieces of it as required.
 
Users can select storage from the pool using persistent volume claims.
 
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
  - ReadWriteOnce
 
  capacity:
    storage: 1Gi
 
  hostPath:         # not a production solution; use awsElasticBlockStore etc
    path: /tmp/data
```

Access modes are:
 
- ReadOnlyMany
- ReadWriteOnce
- ReadWriteMany

# Persistent Volume Claims

Kubernetes attempts to find a PersistentVolume that has sufficient capacity as requested by the claim, and any other request properties such as access modes, volume modes, storage class.

If there are multiple matches, but you would like to bind to a specific volume, you can use labels and selectors.

A small claim may get bound to a large volume if there are no other options available. Also note that there is a one-to-one relationship between volumes and claims, so no other claim can take up the remaining space.

If there are no volumes available, the persistent volume claim is left in a pending state until more volumes are added.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
  - ReadWriteOnce
 
  resources:
    requests:
      storage: 500Mi
 
  selector:       # optional
    matchLabels:
      release: "stable"
```


Use in a pod:
 
```yaml
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

When we delete a claim, the default behaviour is to retain the data until it is deleted manually.

```yaml
persitentVolumeReclaimPolicy: Retain/Delete/Recycle
```