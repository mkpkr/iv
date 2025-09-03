Note if we want to ensure that our nodes only accept certain pods AND our pods only end up on certain servers, we must use a combination of taints/tolerations and node affinity. Taints/tolerations alone will still allow our pods to be scheduled by non-tained nodes; node affinity alone will allow non-desirable pods to be scheduled on our the node.

# Node Selectors

To tell a pod which type of node it is allowed to be scheduled on, the simplest way is to use a node selector:
 
```
$ kubectl label nodes <node-name> <label-key>=<label-value>
```

e.g.

```
$ kubectl label nodes node1 size=Large
```

```yaml
spec:
  containers:
    ...
  nodeSelector:
    size: Large    # this is just a label assigned to the node
```
However what if we want to say "medium or large" or "not small" - we can't do this with node selectors, and must use Node Affinity.

# Node Affinity

```yaml
spec:
  containers:
    ...
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecuition:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values:
              - Large
              - Medium
```

Operators:
 
	• In
	• NotIn
	• Exists
		○ don't need "values" section then; just checks the key exists

Types of node affinity:
 
	• requiredDuringSchedulingIgnoredDuringExecuition
		○ if matching node not found, scheduler will not schedule the pod
	• preferredDuringSchedulingIgnoredDuringExecuition
		○ if matching node not found, scheduler will ignore node affinity rules
	• requiredDuringSchedulingRequiredDuringExecuition
		○ if matching node not found, scheduler will not schedule the pod
		○ if label is changed on a node while pod is executing, remove that pod
		○ (currently not available for use)

