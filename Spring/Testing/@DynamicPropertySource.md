
Useful for getting properties from Test Containers and registering with application context

`@DynamicProprertySource` allows you to register properties in application context, after the container has started:

- Dynamic properties have higher precedence than those loaded from `@TestPropertySource`, the operating system's environment, Java system properties, or property sources added by the application declaratively by using `@PropertySource` or programmatically.
- Use for things like TestContainers where the property needs to be set after the container is started

```properties
spring.cloud.stream.kafka.streams.binder.brokers = ${spring.cloud.stream.kafka.streams.binder.brokers:localhost:9092}
```
```java
@DynamicPropertySource 
static void kafkaProperties(DynamicPropertyRegistry registry) { 
	registry.add("spring.cloud.stream.kafka.streams.binder.brokers", kafka::getBootstrapServers); 
}
```


---

```java
public abstract class MySqlTestBase { 

	private static MySQLContainer database = new MySQLContainer("mysql:5.7.32"); 
	
	static { database.start(); } 
	
	@DynamicPropertySource 
	static void databaseProperties(DynamicPropertyRegistry registry) {      
		registry.add("spring.datasource.url", database::getJdbcUrl);
		registry.add("spring.datasource.username", database::getUsername); 
		registry.add("spring.datasource.password", database::getPassword);
	} 
}
```

