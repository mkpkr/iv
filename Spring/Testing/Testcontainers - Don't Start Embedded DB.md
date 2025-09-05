The `@DataMongoTest` and `@DataJpaTest` annotations are designed to start an embedded database by default. 

If we want to use a containerized database (Testcontainers), we have to disable this feature. 

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class PersistenceTests extends MySqlTestBase {
```

----

```java
@DataMongoTest(excludeAutoConfiguration = EmbeddedMongoAutoConfiguration.class)
class PersistenceTests extends MongoDbTestBase {
```
