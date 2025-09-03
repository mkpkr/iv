```bash
$ kubectl apply -f myfile.yml
$ kubectl apply -f myyamldir/
$ kubectl apply -f https://bret.run/pod.yml

#see what will change using --dry-run=server or --diff:
$ kubectl apply -f app.yml --dry-run=server
$ kubectl diff -f app.yml
```

# Generate a Manifest

```bash
# create new resource definition
# --dry-run=client -o yaml
$ kubectl run nginx --image=nginx --dry-run=client -o yaml
$ kubectl create deployment --replicas=4 --image=nginx nginx --dry-run=client -o yaml
$ kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml 

# create definition from existing resource
$ kubectl get pod <pod-name> -o yaml > pod-definition.yaml
$ kubectl get replicaset <rs-name> -o yaml > rs-definition.yaml
```

# File Contents

Each manifest needs four parts (root key:values in the file):

	• apiVersion:
	• kind:
	• metadata:
	• spec:


## apiVersion

The APIGROUP is what we use as the prefix/ to apiVersion:
 
Note that we might have multiple lines for the same type, where APIGROUP is different, some research may tell you one is deprecated (deployments used to be extensions APIGROUP).
 
```bash
$ kubectl api-versions

apps/v1
...
scheduling.k8s.io/v1
storage.k8s.io/v1
v1
```

As we can see, we have v1 for those with no APIGROUP, or we have apps/v1 for apps APIGROUP.

## kind

We want to create a particular resource, we can run a command to find if that resource is availbable to our cluster.
 
We can get a list of resources our cluster supports by running this:
 
```
$ kubectl api-resources
NAME               SHORTNAMES   APIGROUP      NAMESPACED     KIND
...
pods               po                          true          Pod
services           svc                         true          Service
deployments        deploy       apps           true          Deployment
replicasets        rs           apps           true          ReplicaSet
...
```

The KIND column is what goes in kind:
 
## metadata

name, labels etc

## spec

This is where the action is, the spec will be completely different for each type of resource.
 
List all the keys you can put for a resource type `kubectl explain <resource name> --recursive`
 
 ```
$ kubectl explain services --recursive

KIND:     Service
VERSION:  v1
 
DESCRIPTION:
     Service is a named abstraction of software service (for example, mysql)
     consisting of local port (for example 3306) that the proxy listens on, and
     the selector that determines which pods will answer requests sent through
     the proxy.
 
FIELDS:
   apiVersion   <string>
   kind <string>
   metadata     <Object>
      annotations       <map[string]string>
      clusterName       <string>
      ...
   spec <Object>
      clusterIP <string>
      externalIPs       <[]string>
      externalName      <string>
      externalTrafficPolicy     <string>
      healthCheckNodePort       <integer>
      ...
```

This doesn't tell you what they do, what's required etc, but is good for a quick lookup.
 
We can drill down with `kubectl explain <resource name>.spec`
 
```
$ kubectl explain services.spec
KIND:     Service
VERSION:  v1
 
RESOURCE: spec <Object>
 
DESCRIPTION:
     Spec defines the behavior of a service.
     ...
 
FIELDS:
   clusterIP    <string>
     clusterIP is the IP address of the service and is usually assigned randomly
     by the master. If an address is specified manually and is not in use by
     others, it will be allocated to the service; otherwise, creation of the
     ...
 
   externalIPs  <[]string>
     externalIPs is a list of IP addresses for which nodes in the cluster will
     also accept traffic for this service. These IPs are not managed by
     ...
 
   externalName <string>
     externalName is the external reference that kubedns or equivalent will
     return as a CNAME record for this service. No proxying will be involved.
     ...

   ...
```

## Sub spec

Resources like Deployment have a lot of nested resources.
 
If you run kubectl explain deployment --recursive you will see all the sub resources and their fields.
 
We can run an explain on these by doing something like:
 
```
$ kubectl explain deployment.spec
...
FIELDS:
   ...
   template     <Object> -required-
     Template describes the pods that will be created.


$ kubectl explain deployment.spec.template
KIND:     Deployment
VERSION:  apps/v1
 
RESOURCE: template <Object>
 
DESCRIPTION:
     Template describes the pods that will be created.
 
     PodTemplateSpec describes the data a pod should have when created from a
     template
 
FIELDS:
   metadata     <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
 
   spec <Object>
     Specification of the desired behavior of the pod. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
 
```
 
```
$ kubectl explain deployment.spec.template.spec
...
 
$ kubectl explain deployment.spec.template.spec.volumes
...
 
$ kubectl explain deployment.spec.template.spec.volumes.nfs
...
 
$ kubectl explain deployment.spec.template.spec.volumes.nfs.server
...
```


# Pod Example

Here is an example of a Pod for demonstration, remember in production we will not create individual Pods as this is just like a docker run:

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.17.3
    ports:
    - containerPort: 80
```


# Deployment Example

```
apiVersion: apps/v1        # api version for kind: Deployment
kind: Deployment
metadata:
  name: app-nginx-deployment
 
spec:
  replicas: 3
 
  selector:
    matchLabels:           # manage the pods matching this label (match the label in the below template)
      app: app-nginx
 
  template:
    metadata:
      labels:
        app: app-nginx     # label the pods in this deployment; note a label can be any key:value you choose
        #release: v1       # we may use any number of labels; we can also add these to our selector to AND them
 
    spec:
      containers:
        - name: nginx
          image: nginx:1.17.3
          ports:
            - containerPort: 80
```

# Service Example

```
apiVersion: v1
kind: Service
metadata:
  name: app-nginx-service
 
spec:
  type: NodePort
 
  ports:
    - name: http           # just a label for this traffic
      port: 80             # exposes the service on the specified port within the cluster; other pods within the cluster can communicate with this server on the specified port.
      #targetPort: 80      # optional; the port on which the service will send requests to, that your pod will be listening on; same as port if omitted
      #nodePort: 30080     # optional; the port to expose outside the cluster - K8s allows you to use values 30000 - ~32767; if omitted K8s assigns random port
                           # in production we will replace this with a Load Balancer which can expose a sensible port such as 80
 
  selector:
    app: app-nginx         # direct traffic to pods matching this label#

```
