# Important

- Instant is UTC by default – we can use `atZone` to change that

# Old - `Date` & `Calendar`

- In Java 1.0, the only support for date and time was the java.util.Date - a point in time with millisecond precision - the years start from 1900, whereas the months start at index 0.
- In Java 1.1 many methods of the Date class were deprecated, and the class was replaced by the alternative java.util.Calendar class. Unfortunately, Calendar has similar problems and design flaws that lead to error-prone code. Months also start at index 0. (At least Calendar got rid of the 1900 offset for the year.) Worse, the presence of both the Date and Calendar classes increases confusion among developers.
	- In addition, features such as DateFormat, used to format and parse dates or time in a language-independent manner, work only with the Date class.
	- DateFormat also isn’t thread-safe, for example, which means that if two threads try to parse a date by using the same formatter at the same time, you may receive unpredictable results.
- Finally, both Date and Calendar are mutable classes. What does it mean to mutate the 21st of September 2017 to the 25th of October?
- The consequence is that all these flaws and inconsistencies have encouraged the use of third-party date and time libraries, such as Joda-Time.

For these reasons, Oracle decided to provide high-quality date and time support in the native Java API. As a result, Java 8 integrates many of the Joda-Time features in the java.time package.

---
# LocalDate

- `{java} LocalDate date = LocalDate.of(2017, 9, 21);
- `{java} LocalDate date = LocalDate.parse("2017-09-21");
- `{java} LocalDate date = LocalDate.now();

- `{java} int year = date.getYear();
- `{java} Month month = date.getMonth();
- `{java} int day = date.getDayOfMonth();
- `{java} DayOfWeek dow = date.getDayOfWeek();
- `{java} int len = date.lengthOfMonth();
- `{java} boolean leap = date.isLeapYear();

Or use TemporalField (ChronoField is the implementation):

- `{java} int year = date.get(ChronoField.YEAR);
- `{java} int month = date.get(ChronoField.MONTH_OF_YEAR);
- `{java} int day = date.get(ChronoField.DAY_OF_MONTH);

---
# LocalTime

- `{java} LocalTime time = LocalTime.of(13, 45, 20);
- `{java} LocalTime time = LocalTime.parse("13:45:20");
- `{java} LocalTime time = LocalTime.now();

- `{java} int hour = time.getHour();
- `{java} int minute = time.getMinute();
- `{java} int second = time.getSecond();

---
# LocalDateTime

- `{java} LocalDateTime dt1 = LocalDateTime.of(2017, Month.SEPTEMBER, 21, 13, 45, 20);
- `{java} LocalDateTime dt2 = LocalDateTime.of(date, time);
- `{java} LocalDateTime dt3 = date.atTime(13, 45, 20);

## To/From LocalDate/LocalTime

- `{java} LocalDateTime dt4 = date.atTime(localDateTime);
- `{java} LocalTime time1 = dt4.toLocalTime();

- `{java} LocalDateTime dt5 = time.atDate(localDate);
- `{java} LocalDate date1 = dt5.toLocalDate();

---
# Instant

- `{java} Instant.ofEpochSecond(3);
- `{java} Instant.ofEpochSecond(3, 0);
- `{java} Instant.ofEpochSecond(2, 1_000_000_000); //One billion nanoseconds (1 second) after 2 seconds
- `{java} Instant.ofEpochSecond(4, -1_000_000_000); //One billion nanoseconds (1 second) before 4 seconds

Instant is intended for use only by a machine. It consists of a number of seconds and nanoseconds. As a consequence, it doesn’t provide any ability to handle units of time that are meaningful to humans. A statement like

```java
int day = Instant.now().get(ChronoField.DAY_OF_MONTH);
```
```
java.time.temporal.UnsupportedTemporalTypeException: Unsupported field:
     DayOfMonth
```

But you can work with Instants by using the Duration and Period classes.

---
# Duration

All the above classes implement the `Temporal` interface, which defines how to read and manipulate the values of an object modeling a generic point in time. The next natural step is creating a duration between two temporal objects.

- `{java} Duration d1 = Duration.between(time1, time2);
- `{java} Duration d1 = Duration.between(dateTime1, dateTime2);
- `{java} Duration d2 = Duration.between(instant1, instant2);

If you try to create a duration between `LocalDateTime` and `Instant`, you’ll only obtain a `DateTimeException`.

Moreover, you can’t pass a `LocalDate` to the between method.

You can also create a `Duration` using of methods:

- `{java} Duration threeMinutes = Duration.ofMinutes(3);
- `{java} Duration threeMinutes = Duration.of(3, ChronoUnit.MINUTES);

---
# Period

When you need to model an amount of time in terms of years, months, and days, you can use the Period class. You can find out the difference between two LocalDates with the between factory method of that class:

- `{java} Period tenDays = Period.between(LocalDate.of(2017, 9, 11),

                                     LocalDate.of(2017, 9, 21));

You can also create a Period using of methods:

- `{java} Period tenDays = Period.ofDays(10);
- `{java} Period threeWeeks = Period.ofWeeks(3);
- `{java} Period twoYearsSixMonthsOneDay = Period.of(2, 6, 1);

---
# Period & Duration Common Methods

![[Pasted image 20250904231749.png]]

---
# Manipulating Dates and Times

All the classes are immutable, but we can create modified versions of them.

The most immediate and easiest way to create a modified version of an existing LocalDate/LocalTime/LocalDateTime/Instant is to change one of its attributes, using one of its withAttribute methods.

- `{java} LocalDate date1 = LocalDate.of(2017, 9, 21);
- `{java} LocalDate date2 = date1.withYear(2011);
- `{java} LocalDate date3 = date2.withDayOfMonth(25);
- `{java} LocalDate date4 = date3.with(ChronoField.MONTH_OF_YEAR, 2);

They throw an UnsupportedTemporalTypeException if the requested field isn’t supported by the specific Temporal, such as a ChronoField.MONTH_OF_YEAR on an Instant or a ChronoField.NANO_OF_SECOND on a LocalDate.

We can also use plus and minus:

- `{java} LocalDate date1 = LocalDate.of(2017, 9, 21);
- `{java} LocalDate date2 = date1.plusWeeks(1);
- `{java} LocalDate date3 = date2.minusYears(6);
- `{java} LocalDate date4 = date3.plus(6, ChronoUnit.MONTHS);

![[Pasted image 20250904231851.png]]

## TemporalAdjusters - Advanced Adjustments

```java
import static java.time.temporal.TemporalAdjusters.*;

LocalDate date1 = LocalDate.of(2014, 3, 18);
LocalDate date2 = date1.with(nextOrSame(DayOfWeek.SUNDAY));
LocalDate date3 = date2.with(lastDayOfMonth());
```

![[Pasted image 20250904232030.png]]

Moreover, it’s relatively simple to create your own custom TemporalAdjuster implementation if you can’t find a predefined TemporalAdjuster that fits your needs.
 
```java
@FunctionalInterface
public interface TemporalAdjuster {
    Temporal adjustInto(Temporal temporal);
}
```

For example:

```java
public class NextWorkingDay implements TemporalAdjuster {
    @Override
    public Temporal adjustInto(Temporal temporal) {
        DayOfWeek dow = DayOfWeek.of(temporal.get(ChronoField.DAY_OF_WEEK)); 
        int dayToAdd = 1;
        if (dow == DayOfWeek.FRIDAY) dayToAdd = 3; 
        else if (dow == DayOfWeek.SATURDAY) dayToAdd = 2;
        return temporal.plus(dayToAdd, ChronoUnit.DAYS);
    }
}
date = date.with(new NextWorkingDay());
```

Or define using a lambda:

```java
TemporalAdjuster nextWorkingDay = TemporalAdjusters.ofDateAdjuster(
    temporal -> {
       ...
       return ...;
    });
date = date.with(nextWorkingDay);
```

---
# Formatting & Parsing

The new java.time.format package is devoted to formatting & parsing.

The most important class of this package is DateTimeFormatter. All instances are thread safe.

The easiest way to create a formatter is through its static factory methods and constants. 

```java
LocalDate date = LocalDate.of(2014, 3, 18);
String s1 = date.format(DateTimeFormatter.BASIC_ISO_DATE);
String s2 = date.format(DateTimeFormatter.ISO_LOCAL_DATE);
```


You can also parse a String representing a date or a time in that format to re-create the date object itself.

```java
LocalDate date1 = LocalDate.parse("20140318",
                                  DateTimeFormatter.BASIC_ISO_DATE);
LocalDate date2 = LocalDate.parse("2014-03-18",
                                  DateTimeFormatter.ISO_LOCAL_DATE);
```


## Creating a `DateTimeFormatter` from a Pattern

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
LocalDate date1 = LocalDate.of(2014, 3, 18);
String formattedDate = date1.format(formatter);
LocalDate date2 = LocalDate.parse(formattedDate, formatter);
```
 
 
## Creating a Localized `DateTimeFormatter`

```java
DateTimeFormatter italianFormatter =
               DateTimeFormatter.ofPattern("d. MMMM yyyy", Locale.ITALIAN);
LocalDate date1 = LocalDate.of(2014, 3, 18);
String formattedDate = date.format(italianFormatter); // 18. marzo 2014
LocalDate date2 = LocalDate.parse(formattedDate, italianFormatter);
```
 
 
## Building a `DateTimeFormatter`

```java
DateTimeFormatter italianFormatter = new DateTimeFormatterBuilder()
        .appendText(ChronoField.DAY_OF_MONTH)
        .appendLiteral(". ")
        .appendText(ChronoField.MONTH_OF_YEAR)
        .appendLiteral(" ")
        .appendText(ChronoField.YEAR)
        .parseCaseInsensitive()
        .toFormatter(Locale.ITALIAN);
```

---
# Time Zones

The new **`java.time.ZoneId`** class is the replacement for the old` java.util.TimeZone` class. It aims to better shield you from the complexities related to time zones, such as dealing with Daylight Saving Time (DST).

```java
ZoneId romeZone = ZoneId.of("Europe/Rome");
```

All the region IDs are in the format "{area}/{city}", and the set of available locations is the one supplied by the Internet Assigned Numbers Authority (IANA) Time Zone Database (see https://www.iana.org/time-zones).

When you have a ZoneId object, you can combine it with a LocalDate, a LocalDateTime, or an Instant to transform it into ZonedDateTime instances, which represent points in time relative to the specified time zone:

```java
LocalDate date = LocalDate.of(2014, Month.MARCH, 18);
ZonedDateTime zdt1 = date.atStartOfDay(romeZone);

LocalDateTime dateTime = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45);
ZonedDateTime zdt2 = dateTime.atZone(romeZone);

Instant instant = Instant.now();
ZonedDateTime zdt3 = instant.atZone(romeZone);
```

![[Pasted image 20250904232137.png]]