```yaml
spec:
  containers:
  - image: alpine
    name: alpine
    ...
    volumeMounts:
    - mountPath: /opt
      name: data-volume
 
  volumes:
  - name: data-volume
    hostPath:
      path: /data
      type: Directory
```

This works on a single node, but on a multi node cluster, every pod would use /data directory on their node and expect it to be the same.
 
We need an external storage solution for this, k8s supports many such as NFS, AWS EBS etc):

 ```yaml
  volumes:
  - name: data-volume
    awsElasticBlockStore:
      volumeID: <volume-id>
      fsType: ext4
```