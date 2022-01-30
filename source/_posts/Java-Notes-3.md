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

- Focuses on *how* a task is performed.
- Each line of code gives a specific procedure or operation.

### Functional Code

Starting with version 8, Java added language features to support a more functional style of programming. You might also hear some people call it *declarative* programming.

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

An **anonymous class** is a class that is defined "in-line" and *has no name*, and so it is called "anonymous".

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

- They can only be used to implement *functional interfaces*, not classes.
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
