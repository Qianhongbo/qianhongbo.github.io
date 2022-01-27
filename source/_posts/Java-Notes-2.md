---
title: Java Notes 2
date: 2022-01-23 12:25:39
categories: 
- Backend
tags: 
- Java
---

## Common Types

### Exceptions

#### The Error Class and the Exception Class

The Java error-handling framework uses two different classes to identify abnormal software events:

- The **Error** class is used to indicate a serious problem that the application should *not* try to handle.
- The **Exception** class is used when there is a less catastrophic event that the application *should* try to handle.

#### The Throwable Class

Both *Error* and *Exception* classes inherit from the abstract class **throwable**. This means that both errors and exceptions will contain:

- **The type of problem** – the class type, either *Exception* or *Error*
- **The problem message** – whatever description you've provided within the class, such as "out of memory"
- **The stack trace where the exception occurred** – the order in which things ran and the place in that sequence where the problem occurred (this information is used by developers to track down issues and resolve problems in the code)

#### Throwing an Exception

What does it mean to throw an exception?

- When an error occurs within a method, that method creates an *Exception* object.
- Next, the method hands the Exception off to the JVM runtime system. And that's what we mean when we say that we are throwing an exception. We simply mean that a method hands off an exception to the runtime.

#### Checked vs Unchecked

In Java there are two different types of exceptions:

##### Unchecked Exceptions

- **Unchecked** exceptions are exceptions that are unknown to the compiler.
- Because these exceptions are only known at runtime, they are also referred to as *runtime exceptions*.
- They are a result of a programming error, typically arithmetic errors (such as division by 0).
- Unchecked exceptions are used when when we expect that the caller of the method cannot recover from the exception.

##### Checked Exceptions

- **Checked** exceptions are known to the compiler.
- If we are calling a method that potentially throws a checked exception, it *must* be handled (or we will get an error from the compiler).
- Checked exceptions are used when we expect that the caller of the method *can* recover from the exception.

#### Handling Exceptions

```java
try {
    read();
}
catch (FileNotFoundException ex){
    ex.getLocalizedMessage();
}
finally {
}
```

### Enum

In Java, the declaration of an Enum defines a class. This class can exist **within another class** or as a **standalone class**.

In some cases, we might only need to use the Enum type within a single class (as would probably be the case with the stoplight example). In that case it would be acceptable to define it within another class rather than as a standalone. However, if other classes are expected to use the enum type, it should be defined as a separate, standalone class.

```java
public class Main {
  enum StopLight {
    RED,
    YELLOW,
    GREEN
  }

  public static void main(String[] args) {
    StopLight myStoplight = Stoplight.RED; 
    System.out.println(myStoplight);
  }
}
```

### Scanner

The `Scanner` class can read and parse simple text. Here are some key points to keep in mind:

- It parses primitive types and String types into tokens.
- By default it uses whitespaces to delimitate each word. However, it can also use regular expressions.
- The Scanner class can read from several different types of sources, like strings, files and `System.in` (to get input from the command line).

#### Scanner Syntax

##### Example 1

We can use the `Scanner` class to get input from the command line. To do so, we instantiate a scanner object, passing in `System.in`:

```java
Scanner scanner = new Scanner(System.in);
```

##### Example 2

In this example we are using the `nextLine` method to return the full line of the input:

```java
Scanner scanner = new Scanner("This is a line");
System.out.println(scanner.nextLine());
```

**Output:**

```text
This is a line
```

##### Example 3

In this next example, we are using the `next` method to read the first token. The `next` method finds and returns the next complete token.

```java
Scanner scanner = new Scanner("This is a line");
System.out.println(scanner.next());
```

**Output:**

```text
This
```

##### Example 4

In the final example, we are using the `hasNext()` method in a `while` loop to determine if it is safe to call the next method. We only want to call the next method when we know there is a token available.

> **Note:** By default the Scanner tokenizes input by whitespaces. Let's say we have a string with the following text `"One Two Three"`. The text will be tokenized into three separate tokens, `"One"`, `"Two"` and `"Three"`.

```java
Scanner scanner = new Scanner("This is a line");

while(scanner.hasNext()) {
    System.out.println(scanner.next());
}
```

**Output:**

```text
This
is
a
line
```

#### Scanner Example

```java
import java.util.Scanner;

public class UserInputTester {

    public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);
        try {
            System.out.println("Enter a String");
            String userInput = scanner.nextLine();
            System.out.println("User Input: " + userInput);

        } catch (Exception ex) {
            ex.getLocalizedMessage();
        } finally {
            scanner.close(); // we have to close the scanner
        }

    }
}
```

### RegEx

```java
public class RegExTester {

  public static void main(String[] args){
      String emailRegex = "^(.+)@(.+).(.+)$";
      Pattern pattern = Pattern.compile(emailRegex);
      String email = "jeff@gmail.com";

      System.out.println(pattern.matcher(email).matches());
  }
}
```

### String

Java provides advanced memory management for String objects by using a *String pool*. A **String pool** is a way of storing only one copy of a String.

To understand this, we have to look at what is happening in memory when we create Strings and assign them to variables. Strings in Java are *immutable*, meaning they cannot be changed after they are created. When we "change" the String in a variable, what actually happens is that, behind the scenes, Java creates a new String in the String pool—and changes the variable's reference value to point to this new String. The old String object may remain in place, unchanged.

Also note that multiple variables may use the same String reference. This means that if we have two variables with exactly the same String (e.g., `"Hello"`) they will all reference the same String object.

![image-20220123155351170](https://tva1.sinaimg.cn/large/008i3skNgy1gyofi80gpej31h70u0who.jpg)

## Generics and Collections

### Generics

With generics, we can identify what types are stored in the list. The syntax for a generic is very simple—it's just bracket with the type inside, `< type >`. So in this example, we can add `<String>` right next to `List` to identify what types are stored in the list:

```java
List<String> myList = new ArrayList<String>();

myList.add("one");
myList.add("two");
myList.add("three");

// We could also remove the element "one" 
// by using the index 0 instead of the value "one".
myList.remove("one");
```

### Collections

#### Use iterator with collections

```java
List<String> names = new LinkedList<String>();
names.add("Mike");
names.add("Bob");
names.add("Alice");

Iterator<String> iterator = names.iterator();

while(iterator.hasNext()){
  System.out.println(iterator.next());
}
```

#### for each loop

```java
public class CollectionsExercise {

    public static void main(String[] args) {

        List<String> listOfItems = new LinkedList<String>();
        listOfItems.add("Mike");
        listOfItems.add("Bob");
        listOfItems.add("Alice");

        for (String name : listOfItems) {
            System.out.println(name);
        }
    }
}
```

### Sorting collections

#### Strings

A list of Strings can be sorted by simply passing the list to the `Collections.sort()` method. In the example bellow, the names will be sorted in ascending order.

```java
List<String> names = new LinkedList<String>();

names.add("Mike");
names.add("Bob");
names.add("Alice");

Collections.sort(names);
```

#### Wrapper Objects

Wrapper objects were introduced in Java to wrap primitive variable types into objects. In the example below, the primitive `int` values are being converted to `Integer` objects and then sorted.

```java
List<Integer> numbers = new LinkedList<Integer>();

numbers.add(201);
numbers.add(100);
numbers.add(101);

Collections.sort(numbers);
```

#### User-Defined Classes

User-defined classes will need to implement the **Comparable** Interface in order to use the `Collections.sort()` method. The *Comparable* Interface provides a method, `compareTo`, which is used to compare two objects of the same type. In the example below we are using the Person object's `name` field to compare Person objects. This line of code, `name.compareTo(person.name)`, handles all of the hard work.

```java
import java.util.*;  

class Person implements Comparable<Person> {  
  public String name;  
  public Person(String name) {  
    this.name = name;  
  }
  // implement compareTo function
  public int compareTo(Person person) {  
    return name.compareTo(person.name);  

  }   
}  
public class PersonSort {
  public static void main(String[] args) {
    ArrayList<Person> people = new ArrayList<Person>();
    people.add(new Person("Same"));
    people.add(new Person("Mike"));
    people.add(new Person("Apple"));

    Collections.sort(people);
    for (Person person : people) {
      System.out.println(person.name);
    }
  }
}
```

## Advanced Collections

### Map

#### Creating a Map Object

```java
Map<String, Person> mapOfPeople = new HashMap<String, Person>();
```

#### Adding to a Map

```java
Person mike = new Person("Mike", "mike@email.com");
Person shaun = new Person("Shaun", "shaun@email.com");
Person sally = new Person("Sally", "sally@email.com");
Person cesar = new Person("Cesar", "cesar@email.com");

mapOfPeople.put(mike.getEmail(), mike);
mapOfPeople.put(shaun.getEmail(), shaun);
mapOfPeople.put(sally.getEmail(), sally);
mapOfPeople.put(cesar.getEmail(), cesar);
```

#### Retrieving an object from a Map

In this example, we will be looking at the syntax to retrieve a Person object from the Map using an email as the key.

```java
mapOfPeople.get("mike@email.com");
```

#### Iterating over a Map

We can iterate over our Map using a `for-each` loop. Remember that each Map has three distinct views for seeing the data. We can get the keys, the values, or key-value mapping. In the example below we are displaying all of the keys for our Map.

```java
for (String email : mapOfPeople.keySet()) {
    System.out.println(email);
}
```

#### Example

```java
public class MapExcercise {

    public static void main(String[] args) {

        Map<String, Person> mapOfPeople = new HashMap<String, Person>();
        Person mike = new Person("Mike", "mike@email.com");
        Person shaun = new Person("Shaun", "shaun@email.com");
        Person sally = new Person("Sally", "sally@email.com");
        Person cesar = new Person("Cesar", "cesar@email.com");

        ArrayList<Person> people = new ArrayList<Person>();
        people.add(mike);
        people.add(shaun);
        people.add(sally);
        people.add(cesar);

        for (Person person : people) {
            MapExcercise.addToMap(mapOfPeople, person);
        }

        for (String email : mapOfPeople.keySet()) {
            System.out.println(email);
        }

        for (Person person : mapOfPeople.values()) {
            System.out.println(person);
        }

        System.out.println("Get Mike: " + mapOfPeople.get("mike@email.com"));
        System.out.println("Get Jeff: " + mapOfPeople.get("jeff@email.com"));
        System.out.println("Contains Mike: " + mapOfPeople.containsKey("mike@email.com"));
        System.out.println("Contains Jeff: " + mapOfPeople.containsKey("jeff@email.com"));

    }

    private static void addToMap(Map<String, Person> map, Person person) {
        map.put(person.getEmail(), person);
    }

}

```

```java
public class Person {

    private final String name;
    private final String email;

    public Person(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    @Override
    public String toString() {
        return name + " " + email;
    }

}
```

### Sets

#### Concrete Implementations

There are three concrete implementations for the Set interface:

- HashSet
- TreeSet
- LinkedHashSet

Each of these implementations vary, but all of them enforce the no-duplicate-values requirement of the Set interface.

#### Creating a Set Object



```Java
Set<String> mySet = new HashSet<String>();
```

#### Adding to a Set

```java
mySet.add("Hello");
mySet.add("Hello");
mySet.add("Hellos");
```

Even though we used `add` three times, this will actually only add two items, since one of the items is a duplicate.

#### Retrieving an Object From a Set

Using for each loop to retrieve an object.

```java
String foundObject;

for (String text : mySet) {
   if(text.equals("Hello"){
      foundObject  = text;
   }
}
```

#### Example

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class SetExcercise {

    public static void main(String[] args) {
        List<String> numbers = new ArrayList<String>();

        // We have a list of numbers and some are duplicates
        numbers.add("123-222-3333");
        numbers.add("123-222-3333");
        numbers.add("555-222-3333");

        // Add them to a Set to get a unique list
        Set<String> uniqueNumbers = new HashSet<String>(numbers);

        for (String number : uniqueNumbers) {
            System.out.println(number);
        }
    }
}
```

### Queues

**Queues** are data structures that allow us to process items in a **First-In, First-Out** (**FIFO**) order. The first item placed in a queue is also the first one removed from the queue.

Some key points about Queues in Java:

- Queue is an *interface* that implements the *Collection* interface
- We *put* newly added elements at the *end* of the queue
- We *pop* elements off the *front* of the queue

#### Creating a Queue object

```java
Queue<String> myQueue = new LinkedList<String>();
```

#### Adding to a Queue

```java
myQueue.add("Hi");
myQueue.add("There");
```

#### Retrieving an Object From a Queue

This method both *retrieves* the first element from the Queue and *removes* it from the Queue.

```java
myQueue.poll()
```

#### Iterating Over a Queue

```java
while (!myQueue.isEmpty()) {
    System.out.println(myQueue.poll());
}
```
