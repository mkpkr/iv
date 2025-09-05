# Overriding Beans of Primary Context

Say we have a controller that uses an autowired service.

In our integration test we want to mock this service, but we're using `@SpringBootTest` or test slice which wires the application together automatically.

How can we mock the service which is autowired into the controller, when we don't create the controller ourselves.

We need a test version of the bean registered in Spring which will take priority of the main bean.

## My Observations

Unless specified, Spring tries to autowire by type, then by name (or @Qualifier) if there are multiple beans of that type.

If there are two beans of same type, but neither has the name of the field you're autowiring, this throws an Exception as Spring does not know which to autowire.

`@SpringBootApplication` component scans src/main/java, but does not component scan src/test/java

`@SpringBootTest` (or a test slice) will component scan both src/main/java and src/test/java

So, we can't just create a bean of same type in src/test/java configuration class and expect it to be used in our tests in place of the main bean.

Actually (don't do this, bit of a hack, too implicit) if the main configuration bean's name does not match the field you're autowiring (main application will autowire by type), but you specify the test configuration bean to have the same name as the field you're autowiring, then autowiring will go by name as there are two beans with same type, and test configuration will take priority during tests.

## `@Primary`

Annotate the bean in test config with `@Primary`

Note it must use a different bean name or won't be allowed to register

Used for overriding beans with another implementation - not for overriding with mocks.

## `@MockBean`

Not used in @Configuration classes

Use  `@MockBean` PersonService personService; in test class

This defines a Mockito mock for a bean inside the ApplicationContext.

# Specifying Config for ApplicationContext

- `@SpringBootTest(classes = TestConfig.class)`
- `@ContextConfiguration(classes = TestConfig.class)`
- `@SpringJUnitConfig(classes = TestConfig.class)`

However, this will replace your entire configuration.

So you will need the configuration that you import to also include all necessary main beans, and `@EnableAutoconfiguration` or configure all auto configured beans manually.

```java
//using exclude
@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.mike.mvcdemo", excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, value = Configuration.class))
public class TestConfig {
 
//different exclude
@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = "com.mike.mvcdemo", excludeFilters = @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, value = MvcDemoMainConfig.class))
public class TestConfig {
 
//using include
@Configuration
@EnableAutoConfiguration
@ComponentScan(basePackages = {"com.mike.mvcdemo.controller", "com.mike.mvcdemo.service"}) //implicitly excludes .config package
public class TestConfig {
```
# Nested `@Configuration`

Will override your whole configuration.

# `@Configuration` on Top Level Class

Picked up by component scanning, bean overriding rules apply (`@Primary` + different name)

# `@TestConfiguration`

`@SpringBootTest` will scan src/main/java and src/test/java for @Configuration classes. However it will not scan for `@TestConfiguration`, these avoid component scanning and can be brought in manually via `@Import`. Note that `@TestConfiguration` on static nested classes do not require `@Import`.

Used to extend (not override the primary config)

Can be used as a static nested class, if used as top level class it not picked up by component scanning, so in your tests you need to `@Import(MyTestConfig.class)`

Bean overriding rules apply (`@Primary` + different name)