	• kubectl run
		○ closest to docker run; as of 1.18, only creates Pods
	• kubectl create <resource>
		○ create some resources (Deployment etc) via CLI or YAML
	• kubectl apply
		○ create/update anything via YAML
	• kubectl get <type>
	• kubectl get pods -l app=nginx
		○ get pods that match label app=nginx
	• kubectl get <type> -o 
		• -o yaml
		• -o json
		• -o name
		• -o wide
			▪ print additional details about a resource
	• kubectl get all
	• kubectl describe <type> <name>
	• kubectl delete <type> <name>
	• kubectl logs deployment/<deployment>
		• --follow 
		• --tail <num>
	• kubectl logs -f <pod-name>
	• kubectl logs -f <pod-name> <container-name>
		• if there are multiple containers in pod
	
	

kubectl create

Create a Depoloyment and it will also create a ReplicaSet and Pod.

$ kubectl create deployment my-nginx --image nginx
deployment.apps/my-nginx created
 
$ kubectl get deployments
```
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   1/1     1            1           14s
```
 
$ kubectl get replicasets
```
NAME                  DESIRED   CURRENT   READY   AGE
my-nginx-6b74b79f57   1         1         1       18s
```
 
$ kubectl get pods
```
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-6b74b79f57-c6l5t   1/1     Running   0          22s
```

kubectl get

$ kubectl get namespaces

```
NAME              STATUS   AGE
default           Active   2d22h
kube-node-lease   Active   2d22h
kube-public       Active   2d22h
kube-system       Active   2d22h
```


$ kubectl get all

Get all types of resources with one command.

$ kubectl get all
```
NAME                             READY   STATUS    RESTARTS   AGE
pod/my-apache-7b68fdd849-mbwg7   1/1     Running   0          63s
 
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   139m
 
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-apache   1/1     1            1           63s
 
NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/my-apache-7b68fdd849   1         1         1       63s
```


kubectl delete

$ kubectl delete deployment my-nginx
deployment.apps "my-nginx" deleted


kubectl run

Pre-1.18, kubectl run would have created a Deployment.
 
As of 1.18, run will only create a Pod, and nothing else (Deployment, ReplicaSet). 
We will rarely only create a Pod, but here is the syntax as of 1.18:
 
$ kubectl run my-nginx --image nginx
pod/my-nginx created
 
$ kubectl get pods
NAME       READY   STATUS              RESTARTS   AGE
my-nginx   0/1     ContainerCreating   0          62s
 
$ kubectl delete pods my-nginx
pod "my-nginx" deleted

Scaling ReplicaSets

Note here we use both deployment my-apache and deploy/my-apache to highlight the flexibility of kubectl.

$ kubectl create deployment my-apache --image httpd
deployment.apps/my-apache created

$ kubectl scale deploy/my-apache --replicas 2
deployment.apps/my-apache scaled
 
$ kubectl get all
```
NAME                             READY   STATUS    RESTARTS   AGE
pod/my-apache-7b68fdd849-c7l5m   1/1     Running   0          9s
pod/my-apache-7b68fdd849-mbwg7   1/1     Running   0          55m
 
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   3h14m
 
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-apache   2/2     2            2           55m
 
NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/my-apache-7b68fdd849   2         2         2       55m
```

	• Deployment updated to 2 replicas (pods)
	• ReplicaSet updated to 2 pods
	• Control Plane assigns node to pod
	• Kubelet agent sees pod is needed and starts container

Logs

`kubectl logs deployment/<deployment>`
	• `--follow `
	• `--tail <num>`

logs by default it will only pull one of the logs:
 
$ kubectl logs deployment/my-apache

```
Found 2 pods, using pod/my-apache-7b68fdd849-mbwg7
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.0.8. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.0.8. Set the 'ServerName' directive globally to suppress this message
[Thu Nov 19 19:26:02.266002 2020] [mpm_event:notice] [pid 1:tid 140090319242368] AH00489: Apache/2.4.46 (Unix) configured -- resuming normal operations
[Thu Nov 19 19:26:02.266225 2020] [core:notice] [pid 1:tid 140090319242368] AH00094: Command line: 'httpd -D FOREGROUND'
```

We can get logs from multiple pods by using a label:
 
$ kubectl logs -l app=my-apache

Kubernetes can only pull 5 logs at a time and is not a replacement for a full log system (can be increased but becomes taxing on the system).

Describe

Example, describe a pod:
 
$ kubectl get pods
```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-7b68fdd849-c7l5m   1/1     Running   0          55m
my-apache-7b68fdd849-mbwg7   1/1     Running   0          111m
```
 
$ kubectl describe pod my-apache-7b68fdd849-c7l5m
```
Name:         my-apache-7b68fdd849-c7l5m
Namespace:    default
Priority:     0
Node:         docker-desktop/192.168.65.3
Start Time:   Thu, 19 Nov 2020 15:21:24 -0500
Labels:       app=my-apache
              pod-template-hash=7b68fdd849
Annotations:  <none>
Status:       Running
IP:           10.1.0.9
IPs:
  IP:           10.1.0.9
Controlled By:  ReplicaSet/my-apache-7b68fdd849
Containers:
  httpd:
    Container ID:   docker://d467d03b622355b8b8e74803db875b587555ffafb50727d6c78ee224b59ac21c
    Image:          httpd
    Image ID:       docker-pullable://httpd@sha256:fddc534b7f6bb6197855be559244adb11907d569aae1283db8e6ce8bb8f6f456
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 19 Nov 2020 15:21:26 -0500
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-pm5xc (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-pm5xc:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-pm5xc
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  55m   default-scheduler  Successfully assigned default/my-apache-7b68fdd849-c7l5m to docker-desktop
  Normal  Pulling    55m   kubelet            Pulling image "httpd"
  Normal  Pulled     55m   kubelet            Successfully pulled image "httpd" in 1.3270998s
  Normal  Created    55m   kubelet            Created container httpd
  Normal  Started    55m   kubelet            Started container httpd


```