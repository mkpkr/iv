Each node has limited CPU, memory and disk.

We set our pods to require a certain amount of each, and the kubernetes scheduler will look for a node that has enough of each available.

If there are none that have the required resources, the pod will be in a pending state and the events (in describe) will show the reason e.g. insuffient CPU.

```yaml
spec:
  containers:
  - name: some-name
    image: some-image
    resources:
      requests:
        memory: "1Gi"
        cpu: 1
      limits:
        memory: "2Gi"
        cpu: 2
```

# CPU

0.1 CPU can also expressed as 100m where m stands for milli; you can go as low as 1m

1 CPU is equal to:

- 1 Hyperthread
- 1 vCPU in AWS
- 1 Core in GCP or Azure

# Memory

No suffix means bytes

- 1G == Gigabyte == 1,000,000,000 bytes
- 1M == Megabyte == 1,000,000 bytes
- 1K == Kilobyte == 1,000 bytes

- 1Gi == Gibibyte == 1,073,741,824 bytes (1024 x 1024 x 1024)
- 1Mi == Mebibyte == 1,048,576 bytes (1024 x 1024)
- 1Ki == Kibibyte == 1,024 bytes

# Set Default Limits for Namespace

See [[06 LimitRange]]