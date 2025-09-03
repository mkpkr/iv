```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:                         # cron job spec
  schedule: "*/1 * * * *"     # minute(0-59) hour(0-23) dayofmonth(1-31) month(1-12) dayofweek(0sun-6sat)
                              # */1 means every 1 minute
  jobTemplate:
    spec:                     # job spec
 
      completions: 3          # see Job
      parallelism: 3          # see Job
      backOffLimit: 4         # see Job
 
      template:
        spec:                 # pod spec
          containers:
            ...
          restartPolicy: OnFailure
```