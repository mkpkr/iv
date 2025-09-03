A liveness probe tells Kubernetes if a Pod needs to be replaced and a readiness probe tells Kubernetes if its Pod is ready to accept requests.

To simplify this work, Spring Boot has added support for implementing liveness and readiness probes. The probes are exposed on the URLs /actuator/health/liveness and /actuator/health/readiness.

They can either be declared by configuration or implementation in source code, if increased control is required compared to what configuration gives.

When declaring the probes by configuration, a health group can be declared for each probe specifying what existing health indicators it should include.

For example, a readiness probe should report DOWN if a microservice can't access its MongoDB database. In this case, the health group for the readiness probe should include the mongo health indicator. For available health indicators, see [https://docs.spring.io/spring-boot/docs/2.5.2/reference/htmlsingle/#actuator.endpoints.health.auto-configured-health-indicators](https://docs.spring.io/spring-boot/docs/2.5.2/reference/htmlsingle/#actuator.endpoints.health.auto-configured-health-indicators)

                                  
```yaml
management.endpoint.health.probes.enabled: true
management.endpoint.health.group.readiness.include: rabbit, db, mongo
```

The first line of the configuration enables the liveness and readiness probes. The second line declares that readiness probes will include health indicators for RabbitMQ, MongoDB, and SQL databases, if available. For the liveness probe, it is sufficient that the liveness probe reports UP given that the Spring Boot application is up and running.

For more information, see [https://docs.spring.io/spring-boot/docs/2.5.2/reference/htmlsingle/#actuator.endpoints.kubernetes-probes](https://docs.spring.io/spring-boot/docs/2.5.2/reference/htmlsingle/#actuator.endpoints.kubernetes-probes).