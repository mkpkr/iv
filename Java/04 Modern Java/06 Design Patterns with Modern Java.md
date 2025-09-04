# Strategy
![[Pasted image 20250904215721.png]]

```java
@FunctionalInterface
public interface ValidationStrategy {
    boolean execute(String s);
}

...

public class Validator {
    private final ValidationStrategy strategy;
    public Validator(ValidationStrategy v) {
        this.strategy = v;
    }
    public boolean validate(String s) {
        return strategy.execute(s);
    }
}

...

Validator numericValidator = new Validator((String s) -> s.matches("[a-z]+")); 
boolean b1 = numericValidator.validate("aaaa");

Validator lowerCaseValidator = new Validator((String s) -> s.matches("\\d+"));
boolean b2 = lowerCaseValidator.validate("bbbb");
```

---
# Template Method

Instead of calling abstract methods, you can accept arguments to the method that contain executable code e.g. a `Consumer`.

---
# Observer

```java
@FunctionalInterface
interface Observer {
    void notify(String tweet);
}
```

---
# Chain of Responsibility

```java
UnaryOperator<String> headerProcessing = (String text) -> "From Raoul, Mario and Alan: " + text; 
UnaryOperator<String> spellCheckerProcessing =  (String text) -> text.replaceAll("labda", "lambda");

Function<String, String> pipeline = headerProcessing.andThen(spellCheckerProcessing);

String result = pipeline.apply("Aren't labdas really sexy?!!");
```