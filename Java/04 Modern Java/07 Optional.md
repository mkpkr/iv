# Important

Java language architect Brian Goetz clearly stated that the purpose of Optional is to support the optional-return idiom only.

Because the Optional class wasn’t intended for use as a field type, it doesn’t implement the Serializable interface. For this reason, using Optionals in your domain model could break applications with tools or frameworks that require a serializable model to work.

However, as discussed in flatMap, it is useful as a field type and therefore is commonly used like that.

Alternatively, if you need to have a serializable domain model, we suggest that you at least provide a method allowing access to any possibly missing value as an optional, as in the following example:

```java
public class Person {
    private Car car;
    public Optional<Car> getCarAsOptional() {
        return Optional.ofNullable(car);
    }
```

# Creating

- `{java} Optional.empty();
- `{java} Optional.of(car);
- `{java} Optional.ofNullable(car);

# Getting / Checking

- `{java} filter(Predicate<? super T> predicate)
- `{java} isPresent()
- `{java} ifPresent(Consumer<? super T> consumer)
- `{java} orElse(T other)
- `{java} orElseGet(Supplier<? extends T> other)
- `{java} or(Supplier<? extends Optional<? extends T>> supplier)

- similar to the former orElseGet method, but it doesn’t unwrap the value inside the Optional, if present.

- `{java} orElseThrow(Supplier<? extends X> exceptionSupplier)
- `{java} ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction).

- This differs from ifPresent by taking a Runnable that gives an empty-based action to be executed when the Optional is empty.

# `map`

```java
Optional<Insurance> optInsurance = Optional.ofNullable(insurance);

Optional<String> name = optInsurance.map(Insurance::getName);
```

![[Pasted image 20250904215950.png]]

This idea looks useful, but how can you use it to rewrite the following code with potential nulls in a chain in a safe way:

```java
public String getCarInsuranceName(Person person) {
    return person.getCar().getInsurance().getName();
}
```

The answer is to use another method supported by Optional called flatMap.

# `flatMap`

Let's say we rewrote the classes to use Optional fields:

```java
public class Person {
    private Optional<Car> car;
    public Optional<Car> getCar() { return car; }
}
public class Car {
    private Optional<Insurance> insurance;
    public Optional<Insurance> getInsurance() { return insurance; }
}
public class Insurance {
    private String name;
    public String getName() { return name; }
}
```

Trying to write a chain with map doesn't compile:

```java
Optional<Person> optPerson = Optional.of(person);
Optional<String> name =
    optPerson.map(Person::getCar)
             .map(Car::getInsurance)
             .map(Insurance::getName);
```

Unfortunately, this code doesn’t compile. 

 `getCar` returns an object of type `Optional<Car>`, which means that the result of the map operation is an object of type `Optional<Optional<Car>>`

![[Pasted image 20250904220033.png]]

For this we use `flatMap`:

![[Pasted image 20250904220042.png]]

```java
public String getCarInsuranceName(Optional<Person> person) {
    return person.flatMap(Person::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("Unknown"); 
}
```


# `stream`

The `Optional`’s stream() method, introduced in Java 9, allows you to convert an `Optional` with a value to a `Stream` containing only that value or an empty `Optional` to an equally empty `Stream`. 

This technique can be particularly convenient in a common case: when you have a `Stream` of `Optional` and need to transform it into another `Stream` containing only the values present in the nonempty `Optional` of the original `Stream`. 

However there is another practical example why you could find yourself having to deal with a `Stream` of `Optional` and how to perform this operation. If at the end of several stream transformations, you obtain a` Stream<Optional<String>>` in which some of these `Optional`s may be empty because a person doesn’t own a car or because the car isn’t insured, now you have the problem of getting rid of the empty `Optional`s and unwrapping the values contained in the remaining ones before collecting the results into a `Set`. You could have obtained this result with a filter followed by a map, of course, as follows:
 
```java
Stream<Optional<String>> stream = ...

Set<String> result = stream.filter(Optional::isPresent)
                           .map(Optional::get)
                           .collect(toSet());
```


But it is easier to use `Optional.stream` to unwrap in one:

```java
Set<String> result = persons.stream()
                            .map(Person::getCar)
                            .map(optCar -> optCar.flatMap(Car::getInsurance))
                            .map(optIns -> optIns.map(Insurance::getName))
                            .flatMap(Optional::stream)
                            .collect(toSet());
```


# `filter`

```java
Optional<Insurance> optInsurance = ...;
optInsurance.filter(insurance -> "CambridgeInsurance".equals(insurance.getName()))
            .ifPresent(x -> System.out.println("ok"));


public String getCarInsuranceName(Optional<Person> person, int minAge) {
    return person.filter(p -> p.getAge() >= minAge)
                 .flatMap(Person::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("Unknown");
}
```


