# Anonymous class -> lambda

```java title="Before"
Runnable r1 = new Runnable() {
    public void run(){
        System.out.println("Hello");
    }
};
```

```java title="After"
Runnable r2 = () -> System.out.println("Hello");
```

---
# Lambda -> use method references

```java title="Before"
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel =
  menu.stream()
      .collect(
          groupingBy(dish -> {
            if (dish.getCalories() <= 400) return CaloricLevel.DIET;
            else if (dish.getCalories() <= 700) return CaloricLevel.NORMAL;
            else return CaloricLevel.FAT;
          }));
 
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel = 

```

```java title="After"
menu.stream().collect(groupingBy(Dish::getCaloricLevel));
```  

 ---
# Manual comparing -> Collectors.comparing

```java title="Before"
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

```java title="After"
inventory.sort(comparing(Apple::getWeight));   
```
 
 ---
# Imperative -> stream processing

```java title="Before"
List<String> dishNames = new ArrayList<>();
for(Dish dish: menu) {
    if(dish.getCalories() > 300){
        dishNames.add(dish.getName());
    }
}
```
  
```java title="After"
menu.stream() //this could also be parallelized with parallelStream()
    .filter(d -> d.getCalories() > 300)
    .map(Dish::getName)
    .collect(toList());
```

---
# Conditional deferred execution

If you see yourself querying the state of an object (such as the state of the logger) many times in client code, only to call some method on this object with arguments (such as to log a message), consider introducing a new method that calls that method, passed as a lambda or method reference, only after internally checking the state of the object. 
 
```java title="Before"
if (logger.isLoggable(Log.FINER)) {
    logger.finer("Problem: " + generateDiagnostic());
}
```

2 problems:
	• The state of the logger (what level it supports) is exposed in the client code through the method isLoggable.
	• Why should you have to query the state of the logger object every time before you log a message? It clutters your code.
 
A better alternative is to use the log method, which checks internally to see whether the logger object is set to the right level before logging the message:
 
```java
logger.log(Level.FINER, "Problem: " + generateDiagnostic());
```
 
Unfortunately, this code still has an issue: the logging message is always evaluated, even if the logger isn’t enabled for the message level passed as an argument.
 
Solution, a `FunctionalInterface` and lambda:

```java title="After"
public void log(Level level, Supplier<String> msgSupplier);

logger.log(Level.FINER, () -> "Problem: " + generateDiagnostic());
```
 

