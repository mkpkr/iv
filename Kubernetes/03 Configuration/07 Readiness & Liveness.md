Readiness means your application is ready to start serving requests.
 
Liveness means your application is healthy (an application may fail but not crash - so process doesn't exit, so k8s doesn't restart the container within the pod)

	• HTTP: check an endpoint responds e.g. /api/ready
	• Database: check if a TCP socket is listening
	• Custom script: runs some checks and exits successfully if application ready#


```yaml
#http
  httpGet:
    path: /api/ready
    port: 8080
 
#tcp
  tcpSocket:
    port: 3306  
 
#script
  exec:
    command: 
    - cat
    - /app/is_ready
 
#delay/period/retries
    initialDelaySeconds: 5
    periodSeconds: 10
    failureThreshold: 10    # how many retries (default is 3)
```

Example:

```yaml
spec:
  containers:
  - name: <name>
    image: <image>
    readinessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
      failureThreshold: 5
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
      failureThreshold: 5
```