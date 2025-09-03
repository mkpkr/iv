A bug will land on a person, but you may "taint" your skin with bug spray to prevent that. Other bugs may be tolerant of that taint and still land.

In k8s we may have a server with dedicated hardware to perform some particular task; we only want pods for that task being scheduled on that dedicated hardware.

We add a taint to that node, and we had a toleration to the pods that belong on it. No other pods can tolerate the taint so will be put on differnet nodes.

Remember, taints allows a node to receive only certain types of pods, but it does not prevent those pods being executed on other nodes - this is achieved through Node Selectors/Affinity. 

Note if we want to ensure that our nodes only accept certain pods AND our pods only end up on certain servers, we must use a combination of taints/tolerations and node affinity. Taints/tolerations alone will still allow our pods to be scheduled by non-tained nodes; node affinity alone will allow non-desirable pods to be scheduled on our the node.

There is an automatic taint applied to master nodes to prevent workloads being deployed there: `kubectl describe node <maaster-node-name> | grep Taint`
 
You should not remove the taint on the master node.
 
# Taint Node

```
$ kubectl taint nodes <node-name> <key>=<value>:<taint-effect>
```

(this would be done delaratively in the node manifest in a real prod cluster)
 
	• key/value = the taint
	• taint-effect: what happens to the pods if they do not tolerate the taint
		○ NoSchedule
		○ PreferNoSchedule - try to avoid placing the pod on the node but not guaranteed
		○ NoExecute - new pods won't be scheduled, existing pods will be evicted if they don't tolerate the taint (they may have been scheduled before taint applied)

```
$ kubectl taint nodes node1 app=my-big-app:NoSchedule
```

# Toleration of Pod

```yaml
spec:
  containers:
    ...
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "my-big-app"
    effect: "NoSchedule"
```