Since Spring Boot 2.3

Whenever a microservice instance needs to be stopped, for example in a rolling upgrade scenario, there is a risk that active requests are affected when the instance is stopped. To minimize this risk, Spring Boot has added support for graceful shutdown. 

**When applying graceful shutdown, a microservice stops accepting new requests and waits for a configurable time for active requests to complete before it shuts down the application.** Requests that take a longer time to complete than the shutdown wait period will be aborted. These requests will be seen as exceptional cases that a shutdown procedure can't wait for before it stops the application.

Graceful shutdown has been enabled with a wait period of 10 seconds for all microservices by adding the following to the common file application.yml in the config-repo folder:

```yml
server.shutdown: graceful
spring.lifecycle.timeout-per-shutdown-phase: 10s
```
