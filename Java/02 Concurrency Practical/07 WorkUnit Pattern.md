The Queue interfaces are all generic: they’re `Queue<E>`, `BlockingQueue<E>`, and so on. Although it may seem strange, it’s sometimes wise to exploit this and introduce an artificial container class to wrap the items of work.
 
For example, if you have a class called `MyAwesomeClass` that represents the units of work that you want to process in a multithreaded application, then rather than having this:
 
```java
BlockingQueue<MyAwesomeClass>
```

it can be better to have this:
 
```java
BlockingQueue<WorkUnit<MyAwesomeClass>>
```

where `WorkUnit` (or `QueueObject`, or whatever you want to call the container class) is a packaging class that may look something like this:
 
```java
public class WorkUnit<T> {
    private final T workUnit;
 
    public T getWork() {
        return workUnit;
    }
 
    public WorkUnit(T workUnit) {
        this.workUnit = workUnit;
    }
 
    // ... other methods elided
}
```

The reason for doing this is that this level of indirection **provides a place to add additional metadata without compromising the conceptual integrity of the contained type** (`MyAwesomeClass`, in this example).

![[Pasted image 20250903221831.png]]
This is surprisingly useful. Use cases where additional metadata is helpful are abundant, for example:

- Testing (such as showing the change history for an object)
- Performance indicators (such as time of arrival or quality of service)
- Runtime system information (such as how this instance of MyAwesomeClass has been routed)

It can be much harder to add in this indirection after the fact. If you later discover that more metadata is needed in certain circumstances, it can be a major refactoring job to add in what would have been a simple change in the WorkUnit class.