---
title: Java Notes 3
date: 2022-01-29 12:06:44
categories: 
- Backend
tags: 
- Java
---

## Functional Programming

### Imperative Code

Early Java programs were usually programmed in the imperative style. Imperative code usually focuses on how a task is performed. Each line of code gives a specific procedure or operation:

```java
int getTopScore(List<Student> students) {
 int topScore = 0;
 for (Student s : students) {
   if (s == null) continue;
   topScore = Math.max(topScore, s.getScore());
 }
 return topScore;
}
```

- Focuses on **how** a task is performed.
- Each line of code gives a specific procedure or operation.

### Functional Code

Starting with version 8, Java added language features to support a more functional style of programming. You might also hear some people call it **declarative** programming.

Functional code focuses on what happens to inputs in order to produce outputs. You can think of it as describing how to get from the input to the output:

```java
int getTopScore(List<Student> students) {
 return students.stream()
     .filter(Objects::nonNull)
     .mapToInt(Student::getScore)
     .max()
     .orElse(0);
}
```

This code does the exact same thing as the last code example, but it uses a more functional programming style, because it:

- Focuses on what happens to inputs in order to produce outputs..
- Describes how to transform the input into the output.

### Lambda Expressions

```java
BinaryOperator<Integer> add =
   (Integer a, Integer b) -> { return a + b; };

System.out.println(add.apply(1, 2));
```

### Functional Interface

A **functional interface** is a Java interface with exactly one abstract method, called the **functional method**.

```java
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
  default Predicate<T> negate() { return (t) -> !test(t); }

  // Other methods left out of this example
}
```

`Predicate`'s one abstract method is `test()`. `test()` is known as the **functional method**. "Abstract" means the method is not implemented, so in order to be a functional method, it cannot have a default implementation. However, functional interfaces *can* have other default methods. In this case, the `Predicate` interface has a default method called `negate()` that returns another `Predicate`.

### Anonymous Class

An **anonymous class** is a class that is defined "in-line" and **has no name**, and so it is called "anonymous".

#### Anonymous Class vs. Lambda

This anonymous class...

```java
Predicate<String> anonymousSubclass =
   new Predicate<String>() {
     @Override
     public boolean test(String s) {
       return s.trim().isEmpty();
     }
   };
```

... does the same thing as this lambda expresssion:

```java
Predicate<String> lambdaInstead = s -> s.trim().isEmpty();
```

| **Anonymous Class**                      | **Lambdas**                              |
| :--------------------------------------- | :--------------------------------------- |
| Class generated at compile-time          | Class generated at runtime               |
| Can override **`equals()`/`hashCode()`** | Cannot override them; has no identity    |
| **`this`** refers to the anonymous class | **`this`** refers to the enclosing class |

#### What's `this` all about?

```java
public final class ThisExample {
  private final Runnable withLambda =
      () -> System.out.println("From lambda: " + this.getClass());
  private final Runnable withSubclass = new Runnable() {
    @Override
    public void run() {
      System.out.println("From subclass: " + this.getClass());
    }
  };

  public static void main(String[] args) {
    ThisExample thisExample = new ThisExample();
    thisExample.withLambda.run();
    thisExample.withSubclass.run();
  }
}
```

```java
From lambda: class com.udacitydemo.lambdas.ThisExample
From subclass: class com.udacitydemo.lambdas.ThisExample$1
```

Inside the lambda, `this.getClass()` returns the class object representing `ThisExample`. We'll cover class objects in more detail in the lesson on reflection, but for now the important part is that the `this` keyword inside the lambda references the *enclosing class*, which is `com.udacitydemo.lambdas.ThisExample`.

For the `Runnable` subclass, on the other hand, the program printed `com.udacitydemo.lambdas.ThisExample$1`. You can see that the Java compiler generated a class called `com.udacitydemo.lambdas.ThisExample$1` for the anonymous lambda (yes, the class name contains a dollar sign), and the `this` keyword refers to *that* generated class! If you compile this program yourself, you'll even notice the Java compiler produces a file called `ThisExample$1.class`, which contains the compiled byte code for the anonymous class.

### Shortcomings of Lambdas

Lambdas are very useful, but they do have some shortcomings:

- They can only be used to implement **functional interfaces**, not classes.
- Lambdas cannot implement any interface that has multiple abstract methods.
- Lambdas cannot throw checked exceptions (any subclass of `Exception`, such as `IOException`).

### Two Examples

#### Example 1

##### BinaryOperation.java

```java
@FunctionalInterface
public interface BinaryOperation<T> {
  T apply(T a, T b);
}
```

##### Main.java

```java
public final class Main {
  public static void main(String[] args) {
    BinaryOperator<Integer> add = (a, b) -> a + b;
    assert 5 == add.apply(2, 3);
  }
}
```

#### Example 2

##### Calculator.java

```java
import java.util.HashMap;
import java.util.Map;
import java.util.function.BinaryOperator; // a functional interface

public final class Calculator {
  private final Map<String, BinaryOperator<Integer>> operators = new HashMap<>();

  public void registerOperation(String symbol, BinaryOperator<Integer> operator) {
    operators.put(symbol.strip(), operator);
  }

  public int calculate(int a, String operator, int b) {
    return operators.get(operator).apply(a, b);
  }
}
```

##### Calculate.java

```java
public final class Calculate {
  public static void main(String[] args) {
    if (args.length != 3) {
      System.out.println("Usage: Calculate [int] [operator] [int]");
      return;
    }

    Calculator calculator = new Calculator();
    calculator.registerOperation("+", (a, b) -> a + b);
    calculator.registerOperation("-", (a, b) -> a - b);
    calculator.registerOperation("/", (a, b) -> a / b);
    calculator.registerOperation("*", (a, b) -> a * b);

    int a = Integer.parseInt(args[0]);
    String operator = args[1];
    int b = Integer.parseInt(args[2]);

    System.out.println(calculator.calculate(a, operator, b));
  }
}
```

### Capturing variables

#### Captured variables

Lambdas can **capture** variables from the surrounding code. If a lambda uses any variables from the surrounding code, those variables are **captured**. Variables can only be captured if they are **effectively final**.

An **effectively final** variable is a variable whose value does not change after it is initialized.

#### Example

```java
Map<Year, Integer> getClassSizes(List<Student> students) {
  final Map<Year, Integer> classSizes = new HashMap<>();
  students.stream().forEach(s ->
      classSizes.compute(
          s.getGraduationYear(),
          (k, v) -> (v == null) ? 1 : 1 + v));
  return classSizes;
}
```

A good test to figure out if a variable is effectively final is to add the `final` keyword to it. If the code still compiles, that variable is effectively final!

In the example, the `classSizes` variable is effectively final because the value of the variable itself does not change after it's initialized. Remember that in Java, objects are passed by reference. Even though the `HashMap` changes, the variable's value is the `HashMap`'s location in memory, and that location never changes.

### Method Reference

A **method reference** is a short lambda expression that refers to a method that is already named.

Generally speaking, if a method is already defined that you can use, you should use a method reference instead of writing a brand new lambda. It's usually a good idea in software development to not write new code if there's already existing code that does the same thing you're trying to do!

Method references cannot capture surrounding variables, though. If you find yourself in this situation, you should use a custom lambda instead.

```java
import java.util.List;
import java.util.function.Predicate;

public final class LambdasMain {

  /**
   * Returns the number of strings that match a given condition.
   *
   * @param input the strings that should be tested.
   * @param condition the condition that strings should be tested against.
   * @return the number of strings in the input that match the condition.
   */
  public static long countMatchingStrings(List<String> input, Predicate<String> condition) {
    return input.stream().filter(condition).count();
  }

  public static void main(String[] args) {

    List<String> input = List.of("hello", "\t   ", "world", "", "\t", " ", "goodbye", "  ");
		
    // Using lambda expression:
    long numberOfWhitespaceStrings =
            countMatchingStrings(input, s -> s.trim().isEmpty());
    // Using method reference:
    long numberOfWhitespaceStrings = countMatchingStrings(input, String::isBlank);

    System.out.println(numberOfWhitespaceStrings + " whitespace strings");
  }
}
```

### Stream API

A **stream** is a sequence of elements.

Streams are useful because they allow us to process collection, one element at a time. They can process elements in many ways, such as (but not limited to) filtering or transforming elements, sorting elements, or computing statistics such as the sum or average.

A stream pipeline consists of creating a stream, calling intermediate operations on the stream, and then terminating the stream using a terminal operation.

#### Example

```java
void printScores(List<Student> students) {
 return students.stream()
     .filter(Objects::nonNull)
     .mapToInt(Student::getScore)
     .max()
     .orElse(0);
}
```

First, the `stream()` method creates a stream from the students list.

This stream pipeline has two intermediate methods: the `filter()` method removes the elements of the stream that are `null`, and `mapToInt()` transforms each student into an `int`. Notice that each of these methods returns another `Stream`: `filter()` returns a `Stream<Student>`, and `mapToInt()` returns an `IntStream`.

Finally, the terminal operation `max()` computes the maximum value in the `IntStream`. This terminal method actually returns an `OptionalInt` instead of an `int`. If the `students` parameter is empty or contains only `null` elements, it's possible the final stream will be empty. In this case, we need to tell the program to return a default value of `0`.

> If `max()` returns an `OptionalInt` with a value, that value will be used. However, if `max()` returns `OptionalInt.empty()`, the call to `orElse()` makes sure that a default value of `0` will be returned.

#### Collector

A `Collector` is a terminal stream operation that accumulates stream elements into a container.

The `collect()` method is a terminal operation that aggregates streams of elements. Collectors can be passed to `collect()` to determine what kind of collection is created.

```java
Set<String> s = stringList.stream().collect(Collectors.toSet());
```

Collectors can be used to perform reduction operations such as adding or counting.

```java
Map<Year, Long> graduatingClassSizes = studentList.stream()
    .collect(Collectors.groupingBy(
        Student::getGraduationYear, Collectors.counting());
```

Here, `groupingBy()` is used to collect elements into a `Map`. `Collectors.counting()` counts the number of values for each key, so, in this example, it will count how many students there are for each graduation year.

