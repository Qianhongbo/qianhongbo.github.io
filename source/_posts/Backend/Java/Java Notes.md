---
title: Java Notes
date: 2022-01-22 21:48:53
categories: 
- Backend
tags: 
- Java
---

## Java Programing Basics

### Java is static typing

Notice that, in Java, we must specify the data type (e.g., `int`). This is becouse Java is a *statically typed* language. Here are the key points to remember about **static typing**:

- The data type is bound to the variable when the variable is first declared.
- The data type is checked when the code is compiled.
- The data type for a variable cannot later be changed.

In contrast, some languages, such as Python, are dynamically typed. In **dynamic typing**:

- The data type is bound to the *value* itself, but not to the variable.
- The type is checked during runtime.
- The data type of a variable can be changed after it is declared; since the type is associated with the *value*, assigning a new value may mean changing the data type.

### Hello world!

````java
public class GettingStarted{

    public static void main(String[] args) {
        // Add your code here:
        System.out.println("Hello world!!");
    }

}

// Use commond line to compile and run
javac GettingStarted.java
java GettingStarted.java
````

### Type casting

#### Automatic Type Casting

**Automatic** type casting converts a smaller type into a larger type. For example:

```java
int intNumber = 3;
double doubleNumber = intNumber;
System.out.println(doubleNumber);
```

When we print `doubleNumber`, the value will be `3.0`. Notice that there is no precision lost going from a smaller type into a larger type. We started with `3` and ended up with `3.0`.

#### Manual Type Casting

**Manual** type casting is necessary when we want to do either of these things:

- Convert a larger type into a smaller type
- Convert one object type into another

For example, here we are converting from a larger type (double) to a smaller type (int):

```
double doubleNumber = 3.5;
int intNumber = (int)doubleNumber;
System.out.println(intNumber);
```

The resulting value will be `3`, not `3.5`. When we go from a larger type into a smaller type, precision is lost. Java cuts off additional data that will not fit in the casted type. So when we go from a double to an int, any values that are not integers will be removed. This is called *truncation*.

### Methods

#### Method syntax

```java
public double findTheArea(double length, double width) {
    double area = length * width;
    return area;
}
```



#### Methods vs Functions

You'll often see the words **function** and **method** used interchangeably. Although the terms are sometimes used loosely, here's the distinction:

- A *function* is any block of reusable/callable code.
- A *method* is a block of reusable/callable code that is attached to a class or object.

So a method is a type of function, but it's one that is associated with a class or object, whereas other functions can be executed from anywhere.

### Stack and Heap

![image-20220122102441454](https://tva1.sinaimg.cn/large/008i3skNgy1gynk6gr9opj31fl0u0wiv.jpg)

### Types of Access Modifiers

There are four types of access modifiers in Java:

- **Public** means the class can be accessed from everywhere. If you have a method on a class that you want to expose to all other classes, then use this access modifier.
- **Private** means only the defining class can access the data. This provides security, by not allowing other classes to change the data directly. Instead, they must make changes to the data via the provided methods only.
- **Protected** means that access is restricted to the defining class, package, or subclass. This will be useful when we get into subclasses and inheritance in a later lesson, as it will allow our subclasses to use variables and methods from the parent class.
- **Default** means access is restricted to the defining class or the package. This can be used when we have classes inside the same package that we may want to expose data and methods too.

| Access        | Inside class | Inside package | Outside package by subclass | Outside package |
| :------------ | :----------- | :------------- | :-------------------------- | :-------------- |
| **Private**   | Yes          | No             | No                          | No              |
| **Default**   | Yes          | Yes            | No                          | No              |
| **Protected** | Yes          | Yes            | Yes                         | No              |
| **Public**    | Yes          | Yes            | Yes                         | Yes             |

### Array

#### Creating an Array

Here's an example of some code that creates an array of size four, containing four integer values:

```java
int [] numbers  = {1, 2, 3, 4};
```

Notice that creating an array involves three steps:

1. Declare the type of the array, using brackets (as in `int []`).
2. Name the array (in this example, the name is `numbers`).
3. Add values to the array.

#### Another Way to Create an Array

Here's another way we can create an array and add values to it:

```java
int [] numbers = new int[4];
numbers[0] = 1;
numbers[1] = 2;
numbers[2] = 3;
numbers[3] = 4;

int[] numbers = new int[] { 1, 2, 3, 4 };
```

This approach uses the `new` keyword to create a new array object of size 4, and then we assign values to the four spaces created in the array.

### Loop

```java
public class LoopExercise {

    public static void main(String[] args) {

        int[] numbers = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

        // Add for Loop Here
        for (int i = 0; i < numbers.length; i++) {
            System.out.println(numbers[i]);
        }

        // Add while Loop Here
        int index = 0;
        while (index < numbers.length) {
            System.out.println(numbers[index]);
            index++;
        }

        // Add Do while Loop Here
        int counter = 0;
        do {
            System.out.println(numbers[counter]);
            counter++;
        } while (counter < numbers.length);

    }

}
```

### Documentation

#### Syntax

JavaDoc comments use a simple syntax that supports multi-line HTML format documentation. Here's what the syntax looks like:

```java
/** documentation */
```

And here's an example:

```java
/** This program HelloWorld produces a standard output
 *  displaying "Hello World"
 * 
 * @author The author of the class
 * @see A reference to another class
 */
```

#### Parts of a JavaDoc Comment

Notice that JavaDoc comments are broken down into two parts:

1. The description
2. Block tags

In the above example, the description is the first part of the comment, and the block tags are the last part (`@author` and `@see`).

In this example, we have a doc comment for a method:

```java
/**
 * This method displays a simple text output to a provided name
 * 
 *
 * @param name The name of the person we want to say “Hi” too
 * @return results Returns true if the name was printed or
 * false if it failed 
*/
```

## Objecte-Oriented Programming

### Define a class

```java
public class Person {
    private String firstName;
    private String lastName;

    public Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getFirstName() {
        return this.firstName;
    }

    public String getLastName() {
        return this.lastName;
    }

    @Override
    public String toString() {
        return this.firstName + " " +this.lastName;
    }
}
```

### Garbage Collection

Every new object that we instantiate is added to the heap and consumes memory. In programming languages like C or C++, developers are responsible for creating and destroying objects. In contrast, Java has a background process called **Garbage Collection (GC)** that checks all instantiated objects, and destroys them if they do not have any references—thus freeing up memory, even without any active management on the part of the developer.

### Abstract Classes

An **abstract class** has the following key characteristics:

- It defines the behavior for each of the subclasses, but we cannot directly instantiate the abstract class itself.
- It allows us to create abstract methods
  - An abstract method is a method that does not contain an implementation body. Instead, it simply provides a header for the method.
  - Subclasses that extend an abstract class are required to override all abstract methods and provide a specific implementation.

#### Example: Abstract `Vehicle` Class

Here's the example we looked at in the video. To ensure that we will *not* be able to directly instantiate `Vehicle` objects from this class, we define it using the `abstract` keyword:

```java
public abstract class Vehicle {
    protected String start;
    protected String stop;
    protected String direction;

    public Vehicle(String start, String stop, String direction) {
        this.start = start;
        this.stop = stop;
        this.direction = direction;
    }

    public abstract void speed();    

}
```

Next, we use the `Extends` keyword to have our `Car` class extend the `Vehicle` class:

```java
public class Car extends Vehicle {

    public Car() {
    super("Car start", "Car stop", "Car direction");
    }
    @Override
    public void speed() {
        System.out.println("55");
    }
}
```

### Interfaces

**Interfaces** allow us to avoid hardcoding features in an application. We can move specific implementation details into subclasses, and then use an interface to communicate between the application and the subclasses.



```java
public interface Vehicle {

    public String getType();

    public String getSpeed();

    public String getColor();

}
```

```java
public class Car implements Vehicle {
    private String type;
    private String speed;
    private String color;

    public Car(String type, String speed, String color) {
        super();
        this.type = type;
        this.speed = speed;
        this.color = color;
    }

    @Override
    public String getType() {
        return type;
    }

    @Override
    public String getSpeed() {
        return speed;
    }

    @Override
    public String getColor() {
        return color;
    }

}
```

### Interfaces vs Abstract Classes

Here are some of the similarities and differences between abstract classes and interfaces:

**Abstract class**

- Can have class(static) variables.
- Can have both **abstract** methods and **concrete** methods that are shared with the subclasses.
- **Can have instance variables**, i.e. variables that are specific to individual subclasses.
- Subclasses can only extend one class.

**Interfaces**

- Can have class(static) variables.
- Every method in an interface is **abstract**.
- **Cannot have instance variables**. Variables in an interface must be the same for every class implementing the interface.
- Classes can implement more than one interface and **have multiple inheritance**.

> Abstract classes are used for **Modeling** a class hierarchy of similar looking classes (For example Animal can be abstract class and Human , Lion, Tiger can be concrete derived classes)
>
> Interface is used for **Communication** between 2 similar / non similar classes which does not care about type of the class implementing Interface(e.g. Height can be interface property and it can be implemented by Human , Building , Tree. It does not matter if you can eat , you can swim you can die or anything.. it matters only a thing that you need to have Height (implementation in you class) ).
