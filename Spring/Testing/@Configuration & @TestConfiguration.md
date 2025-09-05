
- `@SpringBootTest` + **`@Configuration` on static nested class**
	- nested class picked up automatically by component scanning
	- nested configuration overrides all config
- `@SpringBootTest` + **`@TestConfiguration` on static nested class**
	- nested class picked up automatically by component scanning
	- extends config
	- if you are overriding a @Bean with same type as one in main config, it must have a different name (or context can't be created) and must be annotated with @Primary to take precedence in autowiring (unless main bean is not named after autowired property, but test bean is - this is implicit and shouldn't be relied upon though)
- `@SpringBootTest` + **`@Configuration` on a class in src/test/java**
	- configuration class picked up automatically by component scanning as `@SpringBootTest` scans src/test/java
	- @Bean overriding rules apply (different name + @Primary)
- `@SpringBootTest` + **`@TestConfiguration` on a class in src/test/java**
	- class not picked up automatically by component scanning
	- requires @Import
	- @Bean overriding rules apply (different name + @Primary)
- @SpringJunitConfig(classes = ) & @ContextConfiguration(classes = )
	- these are Spring (not Spring Boot) annotations for integration tests; they will bring up a context pointed to by the parameters to the annotation
	- we specify the `@Configuration` classes to use
	- to pick up @Components automatically the main configuration class must have @ComponentScan(basePackages = )

**Top level class**: `@Configuration` picked up by component scanning, `@TestConfiguration` needs @Import - both extend main config

**Nested class**: `@Configuration` overrides all config, `@TestConfiguration` extends - both picked up by component scanning
