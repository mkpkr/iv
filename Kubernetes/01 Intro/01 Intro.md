At a high level, as a container orchestrator, Kubernetes makes a cluster of (physical or virtual) servers that run containers appear as one big logical server running containers.

As an operator, we declare a desired state to the Kubernetes cluster by creating objects using the Kubernetes API. Kubernetes continuously compares the desired state with the current state. If it detects differences, it takes action to ensure that the current state is the same as the desired state.

To be able to monitor the health of running containers, Kubernetes assumes that containers implement a liveness probe ([Readiness  Liveness](onenote:Pods.one#Readiness%20%20Liveness&section-id={65a9adae-a371-41a5-a59d-9d84b4c4d3e9}&page-id={b1bbd349-3129-4b21-aae0-ff5e80fa4dbb}&end)). If a liveness probe reports an unhealthy container, Kubernetes will restart the container.

Containers can be configured with quotas that specify the amount of resources a container needs. On the other hand, limits regarding how much a container is allowed to consume can be specified on the Pod or for a group of Pods on the namespace level.

Another main purpose of Kubernetes is to provide service discovery of the running Pods and their containers. Kubernetes Service objects can be defined for service discovery and will also load balance incoming requests over the available Pods. Service objects can be exposed to the outside of a Kubernetes cluster.

However, as we will see, an Ingress object is, in many cases, better suited to handling externally incoming traffic to a group of services. To help Kubernetes find out whether a container is ready to accept incoming requests, a container can implement a readiness probe ([Readiness  Liveness](onenote:Pods.one#Readiness%20%20Liveness&section-id={65a9adae-a371-41a5-a59d-9d84b4c4d3e9}&page-id={b1bbd349-3129-4b21-aae0-ff5e80fa4dbb}&end)).

![[Pasted image 20250903204032.png]]