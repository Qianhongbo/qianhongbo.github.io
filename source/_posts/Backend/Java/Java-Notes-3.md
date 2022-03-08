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

## Working with Files & I/O

### I/O (Input/Output)

**Input** is data the program receives from external processes and systems, such as from the local file system or the Internet.

**Output** is data the program sends to external processes and systems.

### Program Memory vs. Persistent Storage

#### Program Memory

In Java programs and other computer programs, short-term storage happens in the program memory.

- Variables, objects and data structures are stored in the heap and stack. * Memory access is fast.
- Memory is erased when the program is done running.

#### Persistent Storage

If we want to store data for longer periods of time, we have to use long-term storage, also known as persistent storage.

- *Files*, stored on disk, are one of the most common forms of persistent storage. Your PC, laptop, and phone all have their own file systems that can store different kinds of files.
- Files stick around, or *persist*, after the program is done executing, so can also be read by humans or other programs.
- Persistent storage access is usually *much* slower than memory access.
- Databases are another common form of persistent storage.

### File Open Options

When you create, read, or write a file, there are standard modes that you can use to do so. Java uses the [`StandardOpenOptions`](https://docs.oracle.com/javase/10/docs/api/java/nio/file/StandardOpenOption.html) class to encapsulate all these modes.

Here are some of the common modes:

| **Option**   | **Description**                                        |
| :----------- | :----------------------------------------------------- |
| `READ`       | Open a file for reading, fail if it doesn't exist.     |
| `CREATE`     | Create a file.                                         |
| `CREATE_NEW` | Same as `CREATE`, but fail if the file already exists. |
| `WRITE`      | Open a file for writing.                               |
| `APPEND`     | Same as `WRITE`, but write to the end of the file.     |

You are allowed to use more than one option at a time.

### Path API

A [`Path`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Path.html) is Java's way to refer to a file on a file system:

```
Path p = Path.of("your/path/here");
```

All paths either refer to *files* or *directories*.

- A *file* contains stored data or bytes.
- A *directory* contains zero or more files.

All paths are either *absolute* or *relative*.

- *Absolute paths* start with a forward-slash (`/`) (known as the *root directory* on Mac and Linux), or a drive name on Windows.
- *Relative paths* are only meaningful relative to some other starting point. They do not start with a forward slash or drive name.

Note that the `Path` object in Java always uses forward-slashes to delimit the parts of the path `String`, even if the underlying file system uses backslashes (such as the NTFS file system on Windows).

```java
// Absolute path to a directory on Mac or Linux
Path p = Path.of("/home/user/Documents/workspaces");
// Absolute path to a file on Mac or Linux
Path p = Path.of("/home/user/Documents/workspaces/main.java");
// Relatuve path to a directory
Path p = Path.of("user/Documents/workspaces");
// Path parts as separate parameters
Path p = Path.of("user", "Documents", "workspaces");
```

```java
import java.nio.file.Path;

public class PathApiDemo {
    public static void main(String[] args) {
        Path p1 = Path.of(".");
        System.out.println(p1);
        // output: .
        Path p2 = Path.of(".");
        System.out.println(p2.toAbsolutePath());
        // output: /Users/qianhongbo/Documents/Mycode/JAVA/Java Code/Java Notes 4/.
        Path p3 = Path.of(".");
        System.out.println(p3.toAbsolutePath().normalize());
        // output: /Users/qianhongbo/Documents/Mycode/JAVA/Java Code/Java Notes 4
        Path p4 = Path.of(".");
        System.out.println(p4.toAbsolutePath().resolve(".."));
        // output: /Users/qianhongbo/Documents/Mycode/JAVA/Java Code/Java Notes 4/./..
        Path p5 = Path.of(".");
        System.out.println(p5.toAbsolutePath().resolve("..").normalize());
        // output: /Users/qianhongbo/Documents/Mycode/JAVA/Java Code
    }
}
```

### Reading and Writing data in Java

In Java, the utilities for reading and writing data are built on top of each other. This diagram shows you what that means. When you use a [`BufferedReader`](https://docs.oracle.com/javase/10/docs/api/java/io/BufferedReader.html) to read lines of text, that buffered reader is reading from another underlying [`Reader`](https://docs.oracle.com/javase/10/docs/api/java/io/Reader.html) that provides characters of text. That `Reader`, in turn, is itself using an [`InputStream`](https://docs.oracle.com/javase/10/docs/api/java/io/InputStream.html) behind the scenes to read the raw data.

![stream](https://video.udacity-data.com/topher/2020/November/5faf3729_screen-shot-2020-11-13-at-5.45.56-pm/screen-shot-2020-11-13-at-5.45.56-pm.png)

#### `InputStream` and `OutputStream`

Input and output streams are the lowest level utilities Java provides. They give you access to the raw data, in the form of bytes. This data can come from a file, from user input on the command-line, or from a network or other source. These are the lowest level APIs Java offers for reading or writing a stream of bytes.

#### `InputStream` Example

```java
InputStream in =
   Files.newInputStream(Path.of("test"), StandardOpenOption.READ);
byte[] data = new byte[10];
while (in.read(data) != -1) {  // Returns the number of bytes read
  useData(data);
}
in.close();  // Close the "test" file
```

This code creates a file called "test" using `newInputStream()` method of the Files API. The code calls the `read()` method, which reads the data into a `byte[]` and returns the number of bytes that were read. If no bytes were read, it returns `-1`. This code will read the entire file, 10 bytes at a time, until the loop reaches the end of the file.

#### `OutputStream` Example

```java
OutputStream out = Files.newOutputStream(Path.of("test"));
out.write("Hello, world!".getBytes());
out.close();  // Close the "test" file
```

The basic `write()` method only deals with bytes. It's pretty self-explanatory: you give the `write()` method a `byte[]`, and it writes those bytes to the output stream.

Both code examples call the `close()` method, which we'll cover in more detail later in this lesson.

#### Demo Code: Ways to Copy a File

##### Reading and Writing the Data Directly

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class CopyFile {
    public static void main(String[] args) throws IOException {
        InputStream in = Files.newInputStream(Path.of(args[0]));
        OutputStream out = Files.newOutputStream(Path.of(args[1]));

        byte[] data = new byte[10];
        while (in.read(data) != -1) {
            out.write(data);
        }
        in.close();
        out.close();
    }
}
```

##### Using `InputStream.transferTo(OutputStream)`

```java
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Path;

public class InputOutputStreams {
    public static void main(String[] args) throws IOException {
        InputStream in = Files.newInputStream(Path.of(args[0]));
        OutputStream out = Files.newOutputStream(Path.of(args[1]));
        in.transferTo(out);
        in.close();
        out.close();
    }
}
```

##### Using the Files API

```java
import java.io.IOException;
import java.nio.file.Path;

public final class CopyFile {
    public static void main(String[] args) throws IOException {
        Files.copy(Path.of(args[0]), Path.of(args[1]));
    }
}
```

#### Readers / Writers

Readers and Writers are the next level of abstraction built on top of input and output streams. These interfaces read and write text characters.

##### Reader Example

```java
char[] data = new char[10];
Reader reader =
   Files.newBufferedReader(Path.of("test"), StandardCharsets.UTF_8);
while (reader.read(data) != -1) {
 useData(data);
}
reader.close();
```

Just like input streams, `Reader`s are usually created with the Files API. But instead of reading `byte`s, we are reading `char`s. There's also a `StandardCharset`, which we'll cover that in more detail in the next video.

##### Writer Example

```java
Writer writer =
   Files.newBufferedWriter(Path.of("test"),
                           StandardCharsets.UTF_8);
writer.write("hello, world");
writer.close();  // Close the "test" file
```

The `Writer` is pretty much what you would expect. This time we are writing encoded `String`s of data instead of raw `byte`s.

#### Buffered Strams

Buffered streams reduce the number of I/O operations performed by your program. This can significantly shrink the amount of time your program spends doing I/O!

When your code calls `BufferedReader.read()`, the `BufferedReader` reads ahead, and fetches more data than you asked for. Whatever it reads is stored in an array, which is also called a *buffer*.

The next time you call `read()`, if the data you requested is already in the buffer, the BufferedReader will give you that cached data, without having to do another read from disk!

##### `BufferedReader` Example

```java
BufferedReader reader =
   Files.newBufferedReader(Path.of("test"), StandardCharsets.UTF_8);
String line;
while ((line = reader.readLine()) != null) {
 useString(line);
}
reader.close();
```

The main difference between this API and the `Reader` API is the addition of the `readLine()` method, which returns a full line of text. Reading lines this way is a lot easier than reading the individual characters one by one!

```
BufferedWriter writer =
   Files.newBufferedWriter(Path.of("test"),
                           StandardCharsets.UTF_8);
writer.write("Hello, ");
writer.write("world!");
writer.flush();  // Writes the contents of the buffer
writer.close();  // Flushes the buffer and closes "test"
```

`BufferedWriter` also uses an in-memory buffer to store writes, and then periodically writes contents of the buffer in batches.

In this code, the `write()` method is called twice, but there is only one actual write to the underlying output stream.

### Java Object Serialization

**Serialization** is the process of converting an object into a data format that can later be **deserialized** back into the original object.

![](https://video.udacity-data.com/topher/2020/November/5faf3bcd_screen-shot-2020-11-13-at-6.06.13-pm/screen-shot-2020-11-13-at-6.06.13-pm.png)

#### Example

`UdacisearchClient.java`

```java
public final class UdacisearchClient implements Serializable {
  ... // Everything else stays the same!
}
```

`Main.java`

```java
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.nio.file.Files;
import java.nio.file.Path;
import java.time.Duration;
import java.time.Instant;
import java.time.ZoneId;

public final class Main {
  public static void main(String[] args) throws Exception {
    if (args.length != 1) {
      System.out.println("Usage: Main [file path]");
      return;
    }

    UdacisearchClient client =
        new UdacisearchClient(
            "CatFacts LLC",
            17,
            8000,
            5,
            Instant.now(),
            Duration.ofDays(180),
            ZoneId.of("America/Los_Angeles"),
            "555 Meowmers Ln, Riverside, CA 92501");

    Path outputPath = Path.of(args[0]);
    try (ObjectOutputStream out = new ObjectOutputStream(Files.newOutputStream(outputPath))) {
      out.writeObject(client);
    }
    System.out.println("Wrote to " + outputPath.toAbsolutePath().toString());

    try (ObjectInputStream in = new ObjectInputStream(Files.newInputStream(outputPath))) {
      UdacisearchClient deserialized = (UdacisearchClient) in.readObject();
      System.out.println("Deserialized " + deserialized);
    }
  }
}
```

#### JSON and XML

JSON (**J**ava**S**cript **O**bject **N**otation) and XML (E**x**tensible **M**arkup **L**anguage) are two common text formats for serializing data.

##### JSON Example

```java
{
  "id": 17,
  "name": "George Washington",
  "emails": ["george.w@gmail.com", "potus.ftw@yahoo.com"]
}
```

##### XML Example

```
<?xml version="1.0" encoding="UTF-8" ?>
<client>
  <id> 17 </id>
  <name> George Washington </name>
  <emails>
    <email> george.w@gmail.com </email>
    <email> potus.ftw@yahoo.com </email>
  </emails>
</client>
```

##### Jackson example for JSON

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;

import java.nio.file.Files;
import java.nio.file.Path;
import java.time.Duration;
import java.time.Instant;
import java.time.ZoneId;

public final class Main {
  public static void main(String[] args) throws Exception {
    if (args.length != 1) {
      System.out.println("Usage: Main [file path]");
      return;
    }

    UdacisearchClient client =
        new UdacisearchClient(
            "CatFacts LLC",
            17,
            8000,
            5,
            Instant.now(),
            Duration.ofDays(180),
            ZoneId.of("America/Los_Angeles"),
            "555 Meowmers Ln, Riverside, CA 92501");

    Path outputPath = Path.of(args[0]);
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.registerModule(new JavaTimeModule());
    objectMapper.writeValue(Files.newBufferedWriter(outputPath), client);

    System.out.println("Wrote to " + outputPath.toAbsolutePath().toString());

    UdacisearchClient deserialized =
        objectMapper.readValue(Files.newBufferedReader(outputPath), UdacisearchClient.class);
    System.out.println("Deserialized " + deserialized);
  }
}
```

## Dependency Injection

### What is a Dependency?

A **dependency** is anything your code needs to work, such as an external library, an environment variable, a remote website, or a database.

In the context of dependency injection, a dependency usually refers to an object, class, or interface that your code imports, creates, or uses.

### What is Dependency Injection?

**D**ependency **I**njection, or DI, is a design pattern that moves the creation of dependencies to outside of your code. Instead of creating objects, you tell the DI framework to create the objects for you, and then you inject those objects into your class.

### Using `@Inject` Annotations

To inject objects from a DI framework, you can add `@Inject` annotations to your code. You can add them directly to instance fields:

```java
class CourseRegistrar {
  @Inject private Database db;
  @Inject private Clock clock;
  @Inject private RegistrationFactory factory;

  boolean registerStudentForCourse(Student s, int courseId) {
    Course c = db.getCourse(courseId);
    if (clock.instant().isAfter(c.getRegistrationDeadline())) return false;
    if (!s.getPassedCourses().containsAll(c.getPrereqs())) return false;
    db.createRegistration(factory.create(courseId, s.getId()));
    return true;
  }
}
```

... or, you can add `@Inject` annotations to constructors:

```java
class CourseRegistrar {
  private final Database db;
  private final Clock clock;
  private final RegistrationFactory factory;

  @Inject
  CourseRegistrar(Database db, Clock clock, RegistrationFactory factory) {
    this.db = db;
    this.clock = clock;
    this.factory = factory;
  }

  boolean registerStudentForCourse(Student s, int courseId) {
    Course c = db.getCourse(courseId);
    if (clock.instant().isAfter(c.getRegistrationDeadline())) return false;
    if (!s.getPassedCourses().containsAll(c.getPrereqs())) return false;
    db.createRegistration(factory.create(courseId, s.getId()));
    return true;
  }
}
```
