Pod Status

	• Pending
		○ the scheduler hasn't assigned or can't assign the pod to a node; see why with kubectl describe
	• ContainerCreating
		○ the pod has been scheduled, images required are pulled; containers started
	• Running
		○ all the containers within a pod have started

Pod Conditions

Boolean values that compliment a pod status:
 
	• PodScheduled
	• Initialized
	• ContainerReady
	• Ready

$ kubectl describe pod    # look for Conditions
 
Ready condition is also shown on kubectl get pod command.
 
By default, Kubernetes sets the container to Ready once it is created, but what if the actual application takes more time to start up? See Readiness  Liveness