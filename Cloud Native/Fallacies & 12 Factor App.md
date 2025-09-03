# Fallacies of Distributed Systems

There are couple of incorrect or unfounded assumptions most developers and architects make when they enter the world of distributed systems. Peter Deutsch, a Fellow at Sun Microsystems, was identifying fallacies of distributed computing back in 1994, at a time when nobody thought about cloud computing.

Because cloud native applications are, at their core, distributed systems, these fallacies still have validity today:

	• The Network is Reliable
		○ Even in the cloud you cannot assume that the network is reliable. Because services are typically placed on different machines, you need to develop your software in a way that it accounts for potential network failures.
	• Latency is Zero
		○ Latency and bandwidth are often confused, but it is important to understand the difference. Latency is how much time goes by until data is received, whereas bandwidth indicates how much data can be transferred in a given window of time. Because latency has a big impact on user experience and performance, you should take care to do the following:
			▪ Avoid frequent network calls and introducing chattiness to the network.
			▪ Design your cloud native application in a way that the data is closest to your client by using caching, content delivery networks (CDNs), and multiregion deployments.
			▪ Use publication/subscription (pub/sub) mechanisms to be notified that there is new data and store it locally to be immediately available.
	• There is Infinite Bandwidth
		○ Nowadays, network bandwidth does not seem to be a big issue, but new technologies and areas such as edge computing open up new scenarios that demand far more bandwidth. For example, it is predicted that a self-driving car will produce around 50 terabytes (TB) of data per day. This volume of data requires you to design your cloud native application with bandwidth usage in mind. Domain-Driven Design (DDD) and data patterns such as Command Query Responsibility Segregation (CQRS) are very useful under such bandwidth-demanding circumstances.
	• The Network is Secure
		○ Two things are often an afterthought for developers: diagnostics and security. The assumption that networks are secure can be fatal. As a developer or architect, you need to make security a priority of your design; for example, by embracing a defense-in-depth approach.
	• The Topology Does Not Change
		○ Pets versus cattle is a meme that gained popularity with the advent of containers. It means that you do not treat any machine as a known entity (pet) with its own set of properties, such as static IPs and so on. Instead, you treat machines as a member of a herd that has no special attributes. Because cloud environments are meant to provide elasticity, machines can be added and removed based on criteria such as resource consumption or requests per second.
	• There is One Administrator
		○ In traditional software development, it was quite common to have one person responsible for the environment, installing and upgrading the application, and so forth. Modern cloud architectures and DevOps methods have shifted the way software is built. A modern cloud native application is a composite of many services that need to work together in concert and that are developed by different teams. This makes it practically impossible for a single person to know and understand the application in its entirety, not to mention trying to fix a problem. Thus, you need to ensure that you have governance in place that makes it easy to troubleshoot issues - release management, decoupling, and logging and monitoring.
	• Transport Cost is Zero
		○ From a cloud native perspective, there are two ways to look at this one. First, transport happens over a network and network costs are not free with most cloud providers. Most cloud providers, for example, do not charge for data ingress, but do charge for data egress.
		○ The second way to look at this fallacy is that the cost for translating any payload into objects is not free. For example, serialization and deserialization are usually fairly expensive operations that you need to consider in addition to the latency of network calls.
	• The Network is Homogeneous
		○ This is almost not worth listing given that pretty much every developer and architect understands that there are different protocols that they must consider when building their applications.

# The Twelve-Factor App

In the early days of Infrastructure as a Service (IaaS) and Platform as a Service (PaaS), it quickly became obvious that the cloud required a new way of developing applications. For example, on-premises scaling was often done by scaling vertically, meaning adding more resources to a machine.

Scaling in the cloud, on the other hand, is usually done horizontally, meaning adding more machines to distribute the load. This type of scaling requires stateless applications, and this is one of the factors described by the Twelve-Factor App manifesto, derived from best practices for application development in the cloud:

	• Codebase
		○ One codebase tracked in revision control; many deploys.
		○ There is only one codebase per application, but it can be deployed into many environments such as Dev, Test, and Prod. In cloud native architecture, this translates directly into one codebase per service or function, each having its own Continuous Integration/Continuous Deployment (CI/CD) flow.
	• Dependencies
		○ Explicitly declare and isolate dependencies.
		○ In general, you should always use dependency managers for languages such as Maven or npm. Containers have drastically reduced dependency-based issues because all dependencies are packaged inside a container, and as such should be declared in the Dockerfile. Chef, Puppet, Ansible, and Terraform are great tools to manage and install system dependencies.
	• Configuration
		○ Store configuration in the environment.
		○ Configuration should be strictly separated from code. This allows you to easily apply configurations per environment. Many modern platforms support external configuration, whether it is configuration maps with Kubernetes or managed configuration services in cloud environments.
	• Backing Services
		○ Treat backing services as attached resources.
		○ In the case of cloud native applications, this might be a managed caching service or a Database as a Service (DbaaS) implementation. The recommendation here is to access those services through configuration settings stored in external configuration systems, which allows loose coupling.
	• Build, Release, Run
		○ Strictly separate build and run stages.
		○ Aim for fully automated build and release stages using CI/CD practices.
	• Processes
		○ Execute the app in one or more stateless processes.
		○ As mentioned earlier, compute in the cloud should be stateless, meaning that data should only be saved outside the processes. This enables elasticity, which is one of the promises of cloud computing.
	• Data Isolation
		○ Each service manages its own data.
		○ This is one of the key tenets of microservices architectures, which is a common pattern in cloud native applications. Each service manages its own data, which can be accessed only through APIs, meaning that other services that are part of the application are not allowed to directly access the data of another service.
	• Concurrency
		○ Scale out via the process model.
		○ Improved scale and resource usage are two of the key benefits of cloud native applications, meaning that you can scale each service or function independently and horizontally; thus, you’ll achieve better resource usage.
	• Disposability
		○ Maximize robustness with fast startup and graceful shutdown.
		○ Containers and functions already satisfy this factor given that both provide fast startup times. One thing that is often neglected is to design for a crash or scale in scenario, meaning that the instance count of a function or a container is decreased, which is also captured in this factor.
	• Dev/Prod Parity
		○ Keep development, staging, and production as similar as possible.
		○ Containers allow you to package all of the dependencies of your service, which limits the issues with environment inconsistencies. There are scenarios that are a bit trickier, especially when you use managed services that are not available on-premises in your Dev environment.
	• Logs
		○ Treat logs as event streams.
		○ Logging is one of the most important tasks in a distributed system. The Twelve-Factor manifesto states that you should treat logs as streams, routed to external systems.
	• Admin Processes
		○ Run admin and management tasks as one-off processes.
		○ This basically means that you should execute administrative and management tasks as short-lived processes. Both functions and containers are great tools for that.

# Availability and Service-Level Agreements

Most of the time, cloud native applications are composite applications that use compute, such as containers and functions, but also managed cloud services such as DbaaS, caching services, and/or identity services. What is not obvious is that your compound Service-Level Agreement (SLA) will never be as high as the highest availability of an individual service. SLAs are typically measured in uptime in a year, more commonly referred to as “number of nines.”

Following is an example of a compound SLA:

Service 1 (99.95%) + Service 2 (99.90%): 0.9995 × 0.9990 = 0.9985005

