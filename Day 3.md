# Java Interview Questions – Day 3

## 1. What is the difference between a shallow copy and a deep copy in Java?

### Shallow Copy:

* Copies the object and references to the same objects (i.e., nested objects are shared).
* If the original object is modified, the changes reflect in the copied object’s shared references.
* Achieved using the `clone()` method of `Object` class unless explicitly overridden.

```java
class Address {
    String city;
    Address(String city) { this.city = city; }
}

class Person implements Cloneable {
    String name;
    Address address;
    public Object clone() throws CloneNotSupportedException {
        return super.clone(); // shallow copy
    }
}
```

### Deep Copy:

* Duplicates everything including nested objects.
* No shared references; both objects are fully independent.
* Done via custom logic, serialization/deserialization, or libraries like Apache Commons Lang.

```java
class Person implements Cloneable {
    String name;
    Address address;
    public Object clone() throws CloneNotSupportedException {
        Person cloned = (Person) super.clone();
        cloned.address = new Address(this.address.city); // deep copy
        return cloned;
    }
}
```

### Use Cases:

* Shallow Copy: Useful when reference sharing is acceptable or desirable (e.g., cache).
* Deep Copy: Needed in multi-threading or distributed systems to prevent shared state bugs.

---

## 2. What is the Java ClassLoader and how does it work?

### What is a ClassLoader?

* A component of the JVM responsible for dynamically loading `.class` files.
* Converts bytecode into Java class objects during runtime.

### Types of ClassLoaders:

1. **Bootstrap ClassLoader**: Loads core Java classes (from `rt.jar`).
2. **Extension ClassLoader**: Loads JDK extension libraries (`lib/ext`).
3. **System/Application ClassLoader**: Loads classes from application classpath.
4. **Custom ClassLoader**: Created to load classes from non-standard sources (network, DB, etc.).

### Delegation Hierarchy:

* **Parent delegation model**: Child ClassLoader first delegates the class loading request to its parent.
* Prevents class loading conflicts.

### How It Works:

```java
Class<?> cls = Class.forName("com.example.MyClass");
Object obj = cls.getDeclaredConstructor().newInstance();
```

### Use Cases:

* Java frameworks (e.g., Spring, Tomcat) use custom classloaders.
* Plugin-based systems.

---

## 3. What are the different types of inner classes in Java?

### 1. **Non-static (Member) Inner Class**:

* Belongs to an instance of the outer class.
* Can access all members of the outer class.

```java
class Outer {
    class Inner {
        void display() {
            System.out.println("Inner class");
        }
    }
}
```

### 2. **Static Nested Class**:

* Acts like a static member of outer class.
* Cannot access instance variables of the outer class.

```java
class Outer {
    static class StaticInner {
        void display() {
            System.out.println("Static inner class");
        }
    }
}
```

### 3. **Local Inner Class**:

* Defined inside a method.
* Scope is limited to the method.

```java
void outerMethod() {
    class LocalInner {
        void display() {
            System.out.println("Local inner class");
        }
    }
    new LocalInner().display();
}
```

### 4. **Anonymous Inner Class**:

* No name, used for instantiating classes/interfaces with one-time use.

```java
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Running");
    }
};
r.run();
```

---

## 4. How does the `instanceof` operator work in Java?

### Purpose:

* Used to check whether an object is an instance of a specific type (class/interface).

### Syntax:

```java
object instanceof ClassName
```

### Example:

```java
String s = "hello";
System.out.println(s instanceof String); // true
```

### Rules:

* Returns `false` if the object is `null`.
* Compile-time safety: You can’t check against unrelated types.

### Java 14+ Pattern Matching:

```java
if (obj instanceof String str) {
    System.out.println(str.toUpperCase());
}
```

### Use Cases:

* Downcasting safely.
* Filtering objects in collections.
* Runtime type identification.

---

## 5. What is the use of the `this` and `super` keywords in Java?

### `this` Keyword:

* Refers to the current class instance.
* Resolves variable shadowing.
* Calls current class constructor via `this()`.

```java
class MyClass {
    int x;
    MyClass(int x) {
        this.x = x; // Resolves conflict between parameter and field
    }
}
```

### `super` Keyword:

* Refers to immediate superclass.
* Used to access parent class members, constructors.
* Supports method overriding.

```java
class Parent {
    void show() { System.out.println("Parent"); }
}
class Child extends Parent {
    void show() {
        super.show(); // calls Parent's show
        System.out.println("Child");
    }
}
```

### Use Cases:

* Constructor chaining.
* Reusing superclass logic.
* Implementing inheritance correctly.

---

## 6. What are enums in Java and how are they used?

### What is an Enum?

* Enum (short for enumeration) is a special Java type used to define collections of constants.
* It enhances type safety compared to traditional constant variables.
* Introduced in Java 5.

### Why Use Enums?

* Prevent invalid values (strongly typed).
* Group related constants in a class-like structure.
* Better readability, maintainability, and compile-time checking.

### Enum Example:

```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

public class TestEnum {
    public static void main(String[] args) {
        Day today = Day.MONDAY;
        switch (today) {
            case MONDAY -> System.out.println("Start of work week");
            case SUNDAY -> System.out.println("Weekend!");
        }
    }
}
```

### Enum with Fields and Methods:

```java
enum Level {
    LOW("Low Level"), MEDIUM("Medium Level"), HIGH("High Level");

    private String description;

    Level(String description) {
        this.description = description;
    }

    public String getDescription() {
        return description;
    }
}
```

### Additional Methods:

* `values()`: returns an array of enum constants.
* `valueOf(String name)`: returns the enum constant with the specified name.

---

## 7. What is the significance of default and static methods in interfaces (Java 8+)?

### Before Java 8:

* Interfaces could have only abstract methods.
* Any new method addition would break existing implementing classes.

### Java 8+ Enhancements:

#### Default Methods:

* Allow method implementation inside interfaces.
* Help in interface evolution without breaking backward compatibility.

```java
interface Vehicle {
    default void start() {
        System.out.println("Vehicle is starting");
    }
}
```

#### Static Methods:

* Utility or helper methods tied to interface, not instance.

```java
interface MathUtils {
    static int add(int a, int b) {
        return a + b;
    }
}
```

#### Example Use:

```java
Vehicle car = new Vehicle() {}; // Anonymous implementation
car.start(); // Calls default method
System.out.println(MathUtils.add(2, 3)); // Calls static method
```

### Conflicts and Resolution:

If a class implements two interfaces with the same default method, it must override it.

---

## 8. What are the different access modifiers in Java and their scopes?

Java has four access modifiers:

### 1. **private**:

* Accessible only within the class.
* Best for encapsulating sensitive data.

### 2. **default** (no modifier):

* Accessible within the same package.
* Often used for package-private classes/methods.

### 3. **protected**:

* Accessible within the package and in subclasses (even outside the package).
* Suitable for inheritance.

### 4. **public**:

* Accessible everywhere.
* Use it for APIs or widely reusable components.

### Comparison Table:

| Access Modifier | Same Class | Same Package | Subclass | Outside Package              |
| --------------- | ---------- | ------------ | -------- | ---------------------------- |
| private         | ✔          | ✖            | ✖        | ✖                            |
| default         | ✔          | ✔            | ✖        | ✖                            |
| protected       | ✔          | ✔            | ✔        | ✖ (only through inheritance) |
| public          | ✔          | ✔            | ✔        | ✔                            |

### Example:

```java
public class Example {
    private int a;
    protected int b;
    int c; // default
    public int d;
}
```

---

## 9. How does the try-with-resources statement work?

### Traditional Resource Handling:

```java
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("file.txt"));
    System.out.println(reader.readLine());
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        reader.close();
    }
}
```

### try-with-resources (Java 7+):

```java
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    System.out.println(reader.readLine());
} catch (IOException e) {
    e.printStackTrace();
}
```

### Benefits:

* Simplified code.
* Automatic resource management.
* Fewer chances of memory leaks.

### Requirements:

* Resource must implement `AutoCloseable` or `Closeable` interface.

---

## 10. What is the difference between `wait()`, `notify()`, and `notifyAll()` in Java concurrency?

### Purpose:

They are used for **inter-thread communication**, part of every Java object.

### Definitions:

* `wait()`: Causes current thread to wait until another thread calls `notify()` or `notifyAll()` on the same object.
* `notify()`: Wakes up one waiting thread.
* `notifyAll()`: Wakes up all waiting threads.

### Usage Rules:

* Must be used within synchronized block/method.
* Throws `IllegalMonitorStateException` if used without lock.

### Example:

```java
class SharedResource {
    synchronized void produce() throws InterruptedException {
        System.out.println("Producing...");
        wait();
        System.out.println("Resumed");
    }

    synchronized void consume() {
        notify();
        System.out.println("Notified producer");
    }
}
```

### Differences:

| Method      | Wakes Up    | Releases Lock | Requires Synchronized |
| ----------- | ----------- | ------------- | --------------------- |
| wait()      | -           | ✔             | ✔                     |
| notify()    | 1 thread    | ✖             | ✔                     |
| notifyAll() | All threads | ✖             | ✔                     |

### When to Use:

* Use `wait()` to pause a thread.
* Use `notify()` if only one thread needs a wake-up.
* Use `notifyAll()` when multiple threads are waiting and any can proceed.

### Common Use Case:

* Producer-Consumer problem
* Thread coordination

---
