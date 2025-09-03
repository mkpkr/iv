# Restart Policy

Default behaviour is to restart pods when the container exits, but what if we just want to run a batch job?

When the process finishes & exits, k8s will restart the container, as it wants it to stay alive.

restartPolicy: Always is the default Pod config, but we can change that:

```
spec:
  containers:
     ...
  restartPolicy: #Always; OnFailure; Never
```

But we are better to create a Job:

# Jobs

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello
spec:               # job spec
 
  completions: 3    # default 1: create pods (by default one after the other) until you have this many successful completions of the job
  parallelism: 3    # default 1: how many pods can we run in parallel
  backOffLimit: 4   # default 6: how many failed attempts before considering the job failed
 
  template:
    spec:           # pod spec
      containers:
        ...
      restartPolicy: Never   # don't recreate the pod; can also use OnFailure
```

If we get pods we can see that pods are created until the number of completions (successful runs).

```
$ kubectl get jobs
$ kubectl logs <job-name>
```

E.g a script that succeeds if it rolls a 6 (completions: 1):

```
throw-dice-job-2fdsj   0/1     Error       0          60s
throw-dice-job-b7mfb   0/1     Completed   0          30s
throw-dice-job-fwnc7   0/1     Error       0          50s
throw-dice-job-g28g8   0/1     Error       0          63s
```