# `toList/Set/Collection`

The `Collectors` for gathering elements of a stream into a true collection are straightforward:

- `toList()`
- `toSet()`
- `toCollection(Supplier<C> collectionFactory)`

Most of the other methods in `Collectors` exist to collect streams into maps.

See bottom of document for example of `toCollection`

# toMap

## `toMap(keyMapper, valueMapper)`

The simplest form is `{java} toMap(keyMapper, valueMapper)` which is perfect if each element in the stream maps to a unique key.

```java
Stream.of(values())
      .collect(toMap(Object::toString, e -> e));
```

If multiple stream elements map to the same key, the pipeline will terminate with an `IllegalStateException`.

The more complicated forms of `toMap` as well as the `groupingBy` method deal with this.

## `toMap(keyMapper, valueMapper, mergeFunction)`

- Merging values
- Last wins

- `{java} toMap(keyMapper, valueMapper, (v1, v2) -> v2)`

- Choosing one value
	- For example if we have a stream of various albums and we want to map recording artist to best selling album:

```java
import static BinaryOperator.maxBy;

Map<Artist, Album> topHits = albums.collect(toMap(Album::artist, a -> a, maxBy(comparing(Album::Sales)));
```

**Note that we use BinaryOperator.maxBy, which converts a Comparator to a BinaryOperator that conputes the maximum implied by the specified Comparator. (Not to be confused with Collectors.maxBy which converts a Comparator into a Collector.**

```java
//BinaryOperator
static <T> BinaryOperator<T> maxBy(Comparator<? super T> comparator)
static <T> BinaryOperator<T> minBy(Comparator<? super T> comparator)
```

## `toMap(keyMapper, valueMapper, mergeFunction, mapFactory)`

The third and final version of `toMap` takes a fourth argument which is a map factory, for use when you want to specify a particular map implementation such as `EnumMap` or `TreeMap`.

## `toConcurrentMap`

There are also variant forms of the first three versions of `toMap` named `toConcurrentMap`, that can run efficiently in parallel and produce `ConcurrentHashMap` instances.

# `groupingBy`

Group elements into categories based on a classifier function.

The classifier function takes an element and returns the category into which it falls. This category serves as the element’s map key.

## `groupingBy(classifierFunction)`

The simplest version of the groupingBy method takes only a classifier and returns a map whose values are lists of all the elements in each category.

```java
words.collect(groupingBy(word -> alphabetize(word)))
```

## `groupingBy(classifierFunction, downstreamCollector)`

If you want groupingBy to return a collector that produces a map with values other than lists, you can specify a downstream collector in addition to a classifier.

A downstream collector produces a value from a stream containing all the elements in a category.

The simplest use of this parameter is to pass toSet(), which results in a map whose values are sets of elements rather than lists.

Alternatively, you can pass toCollection(collectionFactory), which lets you create the collections into which each category of elements is placed.

This gives you the flexibility to choose any collection type you want. Another simple use of the two-argument form of groupingBy is to pass counting() as the downstream collector. This results in a map that associates each category with the number of elements in the category, rather than a collection containing the elements.

```java
Map<String, Long> freq = words.collect(groupingBy(String::toLowerCase, counting()));
```

## `groupingBy(classifierFunction, mapFactory, downstreamCollector)`

The third version of groupingBy lets you specify a map factory in addition to a downstream collector.

Note that this method violates the standard telescoping argument list pattern: the mapFactory parameter precedes, rather than follows, the downStream parameter.

## `groupingByConcurrent`

The groupingByConcurrent method provides variants of all three overloadings of groupingBy.

These variants run efficiently in parallel and produce ConcurrentHashMap instances.

# `partitioningBy`

There is also a rarely used relative of groupingBy called partitioningBy. In lieu of a classifier method, it takes a predicate and returns a map whose key is a Boolean.

There are two overloadings of this method, one of which takes a downstream collector in addition to a predicate.

# Downstream Collectors

The collectors returned by the counting() method are intended only for use as downstream collectors.

The same functionality is available directly on Stream, via the count method, so there is never a reason to say collect(counting()).

There are more Collectors methods with this property. They include the nine methods whose names begin with

- `*summing`
- `*averaging`
- `*summarizing`

(whose functionality is available on the corresponding primitive stream types).

They also include all overloadings of the methods

- `*reducing`
- `*filtering`
- `*mapping`
- `*flatMapping`
- `*collectingAndThen`

Most programmers can safely ignore the majority of these methods. From a design perspective, these collectors represent an attempt to partially duplicate the functionality of streams in collectors so that downstream collectors can act as “ministreams.”

# Other Collectors Methods

There are three `Collectors` methods we have yet to mention. Though they are in `Collectors`, they don’t involve collections.

The first two are

- `minBy`
- `maxBy`

which take a comparator and return the minimum or maximum element in the stream as determined by the comparator.

They are the collector analogues of the binary operators returned by the like-named methods in BinaryOperator. Recall that we used BinaryOperator.maxBy in our best-selling album example.

The final Collectors method is

- `joining`

which operates only on streams of CharSequence instances such as Strings.

In its parameterless form, it returns a collector that simply concatenates the elements.

Its one argument form takes a single CharSequence parameter named delimiter and returns a collector that joins the stream elements, inserting the delimiter between adjacent elements. If you pass in a comma as the delimiter, the collector returns a comma-separated values string.

The three argument form takes a prefix and suffix in addition to the delimiter. The resulting collector generates strings like the ones that you get when you print a collection, for example `[came, saw, conquered]`.

# Collecting And Then – Convert a Collection

```java
Map<Dish.Type, Optional<Dish>> mostCaloricByType =
          menu.stream().collect(
                      groupingBy(Dish::getType,                            
                              maxBy(comparingInt(Dish::getCalories))));
```

The values in this Map are Optionals because this is the resulting type of the collector generated by the maxBy factory method, but in reality if there’s no Dish in the menu for a given type, that type won’t have an Optional.empty() as value; it won’t be present at all as a key in the Map.

The groupingBy collector lazily adds a new key in the grouping Map only the first time it finds an element in the stream, producing that key when applying on it the grouping criteria being used. This means that in this case, the Optional wrapper isn’t useful, because it’s not modeling a value that could be possibly absent but is there incidentally, only because this is the type returned by the reducing collector.

Because the Optionals wrapping all the values in the Map resulting from the last grouping operation aren’t useful in this case, you may want to get rid of them.

To achieve this, or more generally, to adapt the result returned by a collector to a different type, you could use the collector returned by the `Collectors.collectingAndThen` factory method.

```java
Map<Dish.Type, Dish> mostCaloricByType = menu.stream().collect(
           groupingBy(Dish::getType,
                      collectingAndThen(
                          maxBy(comparingInt(Dish::getCalories)), 
                                Optional::get)));
```

This factory method takes two arguments—the collector to be adapted and a transformation function—and returns another collector.

This additional collector acts as a wrapper for the old one and maps the value it returns using the transformation function as the last step of the collect operation. As we’ve said, here this is safe because the reducing collector will never return an Optional.empty().

# `toCollection`

By using toCollection, you can have more control over what type of collection is used for subgroups.

```java
Map<Boolean, Set<CaloricLevel>> caloricLevelsByType =
      menu.stream().collect(
            groupingBy(Dish::getType,
                       mapping(dish -> {
                                   if (dish.getCalories() <= 400)
                                       return CaloricLevel.DIET;          
                                   else if (dish.getCalories() <= 700)
                                       return CaloricLevel.NORMAL;          
                                   else
                                       return CaloricLevel.FAT;
                       },    
                       toCollection(HashSet::new) )));
```