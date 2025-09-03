```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

Existing namespaces:
	• Default
	• kube-system
	• kube-public

Resources within a namespace can refer to services within the same namespace, but when referring to services in other namespaces they must refer to each other by qualifying the namespace for example connecting to the db-service in the dev namespace:
 
mysql.connect("db-service.dev.svc.cluster.local")
 
When a service is created a DNS entry is created automatically with this format.
 
cluster.local is the default domain of the Kubernetes cluster.

svc is the subdomain for service.

