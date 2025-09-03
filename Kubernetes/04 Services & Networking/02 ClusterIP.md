Group pods by name and access them by that name within the cluster (e.g. "backend", "redis").

Pods can be added or removed and we do not need to worry about their IP address:

![[Pasted image 20250903211056.png]]
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  type: ClusterIp
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: my-app
    tier: backend
```