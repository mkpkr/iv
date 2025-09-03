Create a service and access externally via its node port:

![[Pasted image 20250903211144.png]]

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
 
spec:
  type: NodePort
  ports:
  - targetPort: 80     # optional; assumed to be same as port if left out
    port: 80
    nodePort: 30008    # optional; a valid nodePort will be used
  selector:
    app: my-app
    tier: frontend

```


Note that this works whether we have a single pod on a single node (above), or multiple pods in a node, or single pods on multiple nodes:

![[Pasted image 20250903211219.png]]