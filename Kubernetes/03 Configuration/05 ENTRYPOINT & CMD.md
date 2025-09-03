# Recap - Docker CMD vs ENTRYPOINT

Adding a command to end docker run command will:

	• replace whats in CMD
	• append to ENTRYPOINT

So use ENTRYPOINT if you want users to specify an arg e.g

```
ENTRYPOINT["do-something.sh"] 
```
...then when client runs docker run my-image 5 
...the container will start up running dosomething.sh 5
 
If you want to have a default value but allow overriding, use both ENTRYPOINT and CMD
 
```DOCKERFILE
FROM ubuntu
...
ENTRYPOINT["do-something.sh"]
CMD["5"]
```

# Overriding ENTRYPOINT and CMD

Remember CMD would typically be overriden by adding a command at the end of docker/kubectl run command.
 
ENTRYPOINT is not meant to be overridden but appended to, however we can override it:
 
```yaml
spec:
  containers
  - name: my-container
    image: some-image
    command: ["do-something-else.sh"]  # override ENTRYPOINT
    args: ["10"]                       # override CMD
```