# Intro

Lambda expressions let you provide the implementation of the abstract method of a functional interface directly inline and treat the whole expression as an instance of a functional interface.

A note on terminology (useful when discussing lambda rules):

- Statements do not have values or types, but their execution may change state.
- An expression has a value, computed from variables or fields, operators, method calls.
- The term “statement expression” or “expression statement” refers to expressions that are also allowed to be used as a statement:
	- Method invocations
	- Assignments
	- Increment/decrement expressions
	- Class instance creation expressions

# Lambda Rules

- Statements must (almost always, see exception below) be surrounded by {} and terminated with ;
- Expressions must not be.

Note that expressions evaluate to return value, so (String s) -> s.toUpperCase() will return a new String created from toUpperCase()

Valid:
```
1. () -> {}
2. () -> “Raoul”
3. () -> { return “Mario”; }
```

Invalid:

```
1. (Integer i) -> return “Alan” + i;
2. (String s) -> { “Iron Man” }
```

1. return is a control-flow statement. To make this lambda valid, curly braces are required: (Integer i) -> { return “Alan” + i; }

2. “Iron Man” is an expression, not a statement. To make this lambda valid, you can remove the curly braces and semicolon: (String s) -> "Iron Man"

Or if you prefer, you can use an explicit return statement: (String s) -> { return "Iron Man"; }

## void Exception to the Statements Rule

This is valid even though println returns void so this is clearly not an expression: `() -> System.out.println(“this is awesome”)`

Why don't we have to enclose the body with curly braces? `() -> { System.out.println(“this is awesome”); }`

There’s a special rule for a void method invocation: you don’t have to enclose a single void method invocation in braces.

## void Compatibility Rule

If a lambda has a statement expression (see terminology above) as its body, it’s compatible with a function descriptor that returns void (provided the parameter list is compatible, too).

For example, both of the following lines are legal even though the method add of a List returns a boolean and not void as expected in the Consumer context (T -> void):

```java
// Predicate has a boolean return  
Predicate p = (String s) -> list.add(s);  

// Consumer has a void return  
Consumer<String> b = (String s) -> list.add(s);
```

# Type Inference

Types can be omitted from the lamda, as they can be inferred, so (Apple apple) can become (apple).

Also, when a lambda has a single parameter whose type is inferred, the parentheses surrounding the parameter name can also be omitted.

```java
List<Apple> greenApples = filter(inventory,
                   apple -> GREEN.equals(apple.getColor()));  
```

Note that sometimes it’s more readable to include the types explicitly, and sometimes it’s more readable to exclude them. There’s no rule for which way is better; developers must make their own choices about what makes their code more readable.

# Local Variables

Lambda expressions are also allowed to use free variables (variables that aren’t the parameters and are defined in an outer scope) like anonymous classes can. They’re called capturing lambdas.

```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
```

- Lambdas are allowed to capture (to reference in their bodies) instance variables and static variables without restrictions.
- But when local variables are captured, they have to be explicitly declared final or be effectively final.

Because instance variables are stored on the heap, whereas local variables live on the stack, if a lambda could access the local variable directly and the lambda was used in a thread, then the thread using the lambda could try to access the variable after the thread that allocated the variable had deallocated it. Hence, Java implements access to a free local variable as access to a copy of it, rather than access to the original variable.

# Lambda Ambiguity

In the context of overloading with a method taking two different functional interfaces that have the same function descriptor, you can cast the lambda in order to explicitly disambiguate which method signature should be selected.

For example, the following would be ambiguous, because both Runnable and Action have the same function descriptor:

```java
@FunctionalInterface
interface Action {
    void act();
}    

public void execute(Runnable runnable) { ... }

public void execute(Action action) { ... }
```

But, you can explicitly disambiguate the call by using a cast expression:

```java
execute ((Action) () -> {});
```

# Throwing Exceptions

If the lambda body throws an exception, you have two options:

- define your own functional interface that declares the checked exception, or
- wrap the lambda body with a try/catch block

# Example Lambdas for Functional Interfaces

- `Predicate<List<String>>`
	- `(List<String> list) -> list.isEmpty()`

- `Supplier<Apple>`
	- `() -> new Apple(10)`

- `Consumer<Apple>`
	- `(Apple a) -> System.out.println(a.getWeight())`

- `Function<String, Integer>`
- `ToIntFunction<String>`
	- `(String s) -> s.length()`

- `IntBinaryOperator`  
	- `(int a, int b) -> a*b`

- `Comparator<Apple>`
- `BiFunction<Apple, Apple, Integer>`
- `ToIntBiFunction<Apple, Apple>  `
	- `(Apple a1, Apple b2) -> a1.getWeight().compareTo(a2.getWeight())`

If a lambda exceeds a few lines in length (so that its behavior isn’t instantly clear), you should instead use a method reference to a method with a descriptive name instead of using an anonymous lambda. Code clarity should be your guide.