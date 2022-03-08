---
title: Java Notes 4
date: 2022-02-24 21:03:49
categories: 
- Backend
tags: 
- Java
---

## Reflection

### What is Reflection?

**Reflection**, which is sometimes called **introspection**, is the ability of a program to examine its own structure at runtime.

### Static vs Dynamic Code

#### Static Code

Normally, when you write a program, all the class names, method names, and variable names are known *statically*, when the code is being written and compiled:

```java
Foo myObject = new Foo();
```

Static code gives you the benefit of **static analysis**, which includes compile-type checking of static symbols like class and method names, and useful IDE features like auto-completion.

The Java compiler will return an error if you used an invalid class or method name.

#### Dynamic Code

You can also create a `Foo` object *without* static symbol names. This is sometimes called *dynamic* coding:

```java
Object myObject = Class.forName("Foo").getConstructor().newInstance();
```

Writing this kind of code, you won't get any static or compile-time checks to make sure you got the class name correct. If you run this code and there is no class named "Foo", a `ClassNotFoundException` will be thrown.

### What are Annotations?

**Annotations** are a way to provide extra metadata about your program. Annotations only provide metadata — they have no other effect on execution of the annotated code.

#### Common built-in annotations

| Annotations            | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `@Override`            | Tells the compuler that a method is inteded to iverride a superclass method. |
| `@Deprecated`          | Indicated that a method is deprecated an should not be used. Using it will result in a compiler warning. |
| `@FunctionalInterface` | Tells the compuler that the annotated interface is intended to be a functional interface. |
| `@SuppressWarnings`    | Stops the compuler from generating warnings such as for type casting or deprecation warnings. |

### Defining custom annotations

#### Example

```java
@Retention(RetentionPolicy.SOURCE)
@Target(ElementType.TYPE)  // Applies to class, interface, or enum
public @interface ConvertsTo {
  Class<?> targetClass();
  String setterPrefix() default "set";
}
```

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test {
}
```

#### Retention Policies

Here are the possible retention policies for an annotation:

| **Retention Policy** | **Description**                                              |
| :------------------- | :----------------------------------------------------------- |
| `SOURCE`             | Annotation only exists in the source code.                   |
| `RUNTIME`            | Annotation exists in the .class bytecode file and is available at runtime to be used with reflection. |
| `CLASS`              | Annotation exists in the `.class` bytecode file but not exist while the program is running. |

#### Annotation Targets

The target types determine which parts of the program can be given a particular annotation. Here are the possible target types:

| **Element Type**  | **Description**                                              |
| :---------------- | :----------------------------------------------------------- |
| `ANNOTATION_TYPE` | Annotation type declarations (for annotations that apply to other annotations). |
| `CONSTRUCTOR`     | Constructor declarations                                     |
| `FIELD`           | Field declarations, including enum constants.                |
| `LOCAL_VARIABLE`  | Local variable declarations.                                 |
| `METHOD`          | Method declarations.                                         |
| `PACKAGE`         | Package declarations.                                        |
| `PARAMETER`       | Method parameter declarations.                               |
| `TYPE`            | Type declarations, such as classes, interfaces, annotation types, and enum declarations. |

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test {
}
```

### Reflection API

Every class, interface, and type (including primitive types) has a corresponding `Class` object that accesses metadata about that type.

`Class` objects are the main entry point into Java's **Reflection API**.

#### Obtaining `Class` Objects

There are many ways to get `Class` objects:

- Call `getClass()` on an object:

  ```java
  Class<String> c = "Hello world!".getClass();
  ```

- Use `.class` to create a class literal:

  ```java
  Class<String> c = String.class;
  Class<int[]>  c = int[].class;
  ```

- Create classes dynamically using `Class.forName()`:

  ```java
  Class<String> c = Class.forName("java.lang.String");
  ```

#### Working with Methods

Using the `Class` API, you can also obtain [`Method`](https://docs.oracle.com/javase/10/docs/api/java/lang/reflect/Method.html) objects.

`Method`s have some smiliar functionality as `Class`es, such as finding annotations and listing qualifiers, but methods can also be invoked by calling `Method.invoke()`.

```java
Method m = String.class.getMethod("equals", Object.class);
m.invoke("foo", "bar");
// This is the same as `"foo".equals("bar")` return false
```

#### Example

```java
public final class TestRunner {

  private static final List<Class<?>> TESTS = List.of(CalculatorTest.class);

  public static void main(String[] args) throws Exception {

    List<String> passed = new ArrayList<>();
    List<String> failed = new ArrayList<>();

    for (Class<?> klass : TESTS) {
      if (!UnitTest.class.isAssignableFrom(klass)) {
        throw new IllegalArgumentException("Class " + klass.toString() + " must implement UnitTest");
      }

      for (Method method : klass.getDeclaredMethods()) {
        if (method.getAnnotation(Test.class) != null) {
          try {
            UnitTest test = (UnitTest) klass.getConstructor().newInstance();
            test.beforeEachTest();
            method.invoke(test);
            test.afterEachTest();
            passed.add(getTestName(klass, method));
          } catch (Throwable throwable) {
            failed.add(getTestName(klass, method));
          }
        }
      }
    }
    System.out.println("Passed tests: " + passed);
    System.out.println("FAILED tests: " + failed);
  }

  private static String getTestName(Class<?> klass, Method method) {
    return klass.getName() + "#" + method.getName();
  }
}
```

