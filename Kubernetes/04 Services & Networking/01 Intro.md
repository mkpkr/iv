We create a Service in Kubernettes which is a network endpoint for pod(S), if we want to connect to a pod we need a service.

CoreDNS allows us to resolve Services by name.

# ClusterIP

	• default
	• only reachable from within the the cluster; one set of pods talking to another set of pods
	• single, internal virtual IP allocated
	• can connect to the pod using the service's port (i.e. Nginx reached at port 80)
	
```
$ kubectl expose deployment/mydeploy --port 8888

service/mydeploy exposed
```
 
```
$ kubectl get services

NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
mydeploy     ClusterIP   10.101.202.238   <none>        8888/TCP   8m14s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP    21h
```
 
# NodePort

	• something outside the cluster to talk to your nodes through the ip addresses of the nodes themselves
	• high port allocated on each node (won't be able to use port 80 for example)
	• port is open on every node's IP
	• anyone can connect (if they can reach the node)
	• also automatically creates ClusterIP

```
$ kubectl expose deployment/mydeploy --port 8888 --name mydeploy-np --type NodePort

service/mydeploy-np exposed
```
 
```
$ kubectl get services

NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
mydeploy-np   NodePort    10.100.121.177   <none>        8888:32273/TCP   6m33s
kubernetes    ClusterIP   10.96.0.1        <none>        443/TCP          23h
```
 
The ports are opposite of Docker: left is the contianer port, right is the external.
 
The high port came from a default range which are preset for NodePorts.
 
NodePort also creates a ClusterIP behind the scenes.

# LoadBalancer

	• mostly used in the cloud, not built in by default but rather the Kubernets API talks to the infrastructure load balancing API (e.g. AWS ELB)
	• controls an external load balancer through the Kubernetes command line
	• automatically creates ClusterIP and NodePort and then talks to the external system
	• only available through infrastructure provider (AWS ELB etc) that allows Kubernetes to talk to it remotely

Not built in by default, must be provided by infrastructure, the Kubernets API talks to their API (e.g. AWS ELB)
 
Docker Desktop does provide one:
 
```
$ kubectl expose deployment/mydeploy --port 8888 --name mydeploy-lb --type LoadBalancer

service/mydeploy-lb exposed
```
 
```
$ kubectl get services

NAME          TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
mydeploy-lb   LoadBalancer   10.101.83.118    localhost     8888:32620/TCP   70s
kubernetes    ClusterIP      10.96.0.1        <none>        443/TCP          23h
```
 
Now we can curl using port 8888 rather than the high port provided by NodePort.
 
Note that we still see the high port in the LoadBalancer service line 8888:32620/TCP and that's because it also created a NodePort in the background and that is the port of its NodePort.
 
The LoadBalancer accepts a packet and passes it to its NodePort, which then passes it to its ClusterIP
 
# ExternalName
	
	• used less often
	• things in your cluster needing to talk to outside services
	• adds CNAME DNS record to CoreDNS only
	• maybe used when you're doing migrations of things, e.g. moving external service to internal, we may use ExternalName as a substitute to control the DNS inside the Kubernetes workflow

