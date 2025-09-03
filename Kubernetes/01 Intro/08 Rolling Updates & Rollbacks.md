When updating a Deployment,

```
$ kubectl apply -f <deployment-fille>

# or imperatively e.g

$ kubectl set image deployment/my-app-deployment nginx=nginx:1.9.1
```

k8s by default will take pods down and replace them one by one - it will scale the current ReplicaSet down and the new one up simultaneously.

You can specify "recreate" and it will take it all down at once and redeploy - this results in downtime.

You will be able to see this in the events with kubectl describe - recreate will scale the current ReplicaSet to 0 first.

You can undo the previous rollout:

```
$ kubectl rollout undo deployment/my-app-deployment

$ kubectl rollout status deployment/my-app-deployment

$ kubectl rollout history deployment/my-app-deployment
```

Note when you undo your changes, it will create a new revision, and you will see the old revision gone, So if we have 123 and we undo (rollback to 2) 2 gets removed from history as it becomes 4; and we have history 134

## --revision

You can check the status of each revision individually by using the --revision flag (1 is the earliest):

```
$ kubectl rollout history deployment nginx --revision=4
```