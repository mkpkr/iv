Incoming traffic is ingress; outgoing is egress (note we only care about the origin of the request - we don't call the return of ingress as egress).

Pods and services in a cluster can communicate with each other.

![[Pasted image 20250903212109.png]]
All pods and services are in a virtual private network that spans across the cluster nodes.

K8s is by default configured with an "All Allow" rule that allows traffic from any pod to any other pod or service.

What if we want to not allow traffic from web server to database server, and only allow traffic to the database server if its from the api server.

We can link a network policy to one or more pods and define rules within the policy.

This is done using labels & selectors, and ipBlock

- podSelector
- namespaceSelector
- ipBlock

We add an array of selector groups (OR) and can have multiple selectors within each (AND)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
 
spec:
  podSelector:          # which pods this policy applies to
      matchLabels:
        role: db
 
  policyTypes:
  - Ingress
  - Egress
 
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: api-pod
      namespaceSelector:        # define the namespace that we are targeting - otherwise for example dev api server could access prod db
        matchLabels:
          name: prod
 
    -ipBlock:
      cidr: 192.168.5.10/32     # we might have a backup server outside of our cluster that we want to be able to access our pod as well

    ports:
    - protocol: TCP
      port: 3306
 
  - from:                      # can add more froms with different ports
    ...  
 
  egress:
  - to:                        # same as "from" with ingress
```