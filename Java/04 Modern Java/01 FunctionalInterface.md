## Predicate

- `and(other)`
- `or(other)`
- `negate()`
- `static Predicate<T> isEqual(Object targetRef)`

|   |   |
|---|---|
|Predicate<T>|boolean test(T t)|
|IntPredicate|boolean test(int value)|
|DoublePredicate|boolean test(double value)|
|LongPredicate|boolean test(long value)|
|BiPredicate<T,U>|boolean test(T t, U u)|

## Consumer

- andThen(other)

|   |   |
|---|---|
|Consumer<T>|void       accept(T t)|
|IntConsumer|void accept(int value)|
|DoubleConsumer|void accept(double value)|
|LongConsumer|void accept(long value)|
|BiConsumer<T,U>|void accept(T t, U u)|

|   |   |
|---|---|
|ObjIntConsumer<T>|void accept(T t, int value)|
|ObjDoubleConsumer<T>|void accept(T t, double value)|
|ObjLongConsumer<T>|void accept(T t, long value)|

## Supplier

|   |   |
|---|---|
|Supplier<T>|T get()|
|IntSupplier|int getAsInt()|
|DoubleSupplier|double getAsDouble()|
|LongSupplier|long getAsLong()|
|BooleanSupplier|boolean getAsBoolean()|

## Function

- andThen(after)
- compose(before
- static Function<T,T> identity()

|   |   |
|---|---|
|Function<T,R>|R apply(T t)|
|IntFunction<R>|R apply(int value)|
|DoubleFunction<R>|R apply(double value)|
|LongFunction<R>|R apply(long value)|
|ToIntFunction<T>|int applyAsInt(T value)|
|ToDoubleFunction<T>|double applyAsDouble(T value)|
|ToLongFunction<T>|long applyAsLong(T value)|
|IntToDoubleFunction|double applyAsDouble(int value)|
|IntToLongFunction|long applyAsLong(int value)|
|DoubleToIntFunction|int applyAsInt(double value)|
|DoubleToLongFunction|long applyAsLong(double value)|
|LongToIntFunction|int applyAsInt(long value)|
|LongToDoubleFunction|double applyAsDouble(long value)|

## BiFunction

- `andThen(after)`

|   |   |
|---|---|
|BiFunction<T,U,R>|R apply(T t, U u)|
|ToIntBiFunction<T,U>|int applyAsInt(T t, U u)|
|ToDoubleBiFunction<T,U>|double applyAsDouble(T t, U u)|
|ToLongBiFunction<T,U>|long applyAsLong(T t, U u)|

## Operator

- `andThen(after)`
- `compose(before)`

|   |   |
|---|---|
|UnaryOperator<T>|T apply(T t1)|
|IntUnaryOperator|int applyAsInt(int operand)|
|DoubleUnaryOperator|double applyAsDouble(double operand)|
|LongUnaryOperator|long applyAsLong(long operand)|

## BinaryOperator

- `andThen(after)`
- `static UnaryOperator<T> identity()`
- `static IntUnaryOperator identity()`
- `static DoubleUnaryOperator identity()`
- `static LongUnaryOperator identity()`

|   |   |
|---|---|
|BinaryOperator<T>|T apply(T t1, T t2)|
|IntBinaryOperator|int applyAsInt(int left, int right)|
|DoubleBinaryOperator|double applyAsDouble(double d1, double d2)|
|LongBinaryOperator|long applyAsLong(long left, long right)|

- `andThen(after)`
- `static BinaryOperator<T> maxBy(Comparator<? super T> comparator)`    
- `static BinaryOperator<T> minBy(Comparator<? super T> comparator)`