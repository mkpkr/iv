# Master Nodes (Control Plane)

- The API server — When you ran commands on kubectl, this is what it communicated with to perform operations. The API server exposes an API for both external users and other components within the cluster.
- The scheduler — This is responsible for selecting an appropriate node where a pod will run, given priority, resource needs, and other constraints.
- The controller manager — This is responsible for executing control loops: the continual observe-diff-act operation that underpins the operation of Kubernetes.
- A distributed key-value data store, etcd — This stores the underlying state of the cluster and thereby makes sure it persists when nodes fail or restarts are required.

# Worker Nodes (Data Plane)

Each worker node uses the following components to run and monitor applications:

- A container runtime — e.g Docker.
- The kubelet — This interacts with the Kubernetes master to start, stop, and monitor containers on the node.
- The kube-proxy — This provides a network proxy to direct requests to and between different pods across the cluster.

These components are relatively small and loosely coupled. A key design principle of Kubernetes is to separate concerns and ensure components can operate autonomously — a little like microservices.

Affinity rules can be used to control how Pods are assigned to nodes. For example, Pods that perform a lot of disk I/O operations can be assigned to a group of worker nodes that have fast SSD disks. Anti-affinity rules can be defined to separate Pods, for example, to avoid scheduling Pods from the same Deployment to the same worker node.

![[Pasted image 20250903204105.png]]

From the official docs:

![[Pasted image 20250903204118.png]]