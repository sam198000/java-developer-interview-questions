# Java Interview Questions – Day 2

## 1. What is the difference between String, StringBuilder, and StringBuffer?

### String

* **Immutable**: Once created, the value of a String object cannot be changed.
* **Thread-safe by design**: As it is immutable, it is safe to use in a multithreaded environment.
* **Stored in String Pool**: JVM uses a pool of strings to reduce memory usage.
* **Performance**: Poor performance for heavy modifications (concatenation, etc.).

```java
String str = "Hello";
str.concat(" World"); // Creates a new object, doesn't change str
System.out.println(str); // Output: Hello
```

### StringBuilder

* **Mutable**: Modifications happen in the same object.
* **Not synchronized**: Not thread-safe.
* **Faster than StringBuffer and String** for single-threaded environments.

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");
System.out.println(sb); // Output: Hello World
```

### StringBuffer

* **Mutable**: Like StringBuilder.
* **Synchronized**: Thread-safe but slower due to synchronization overhead.

```java
StringBuffer sbf = new StringBuffer("Hello");
sbf.append(" World");
System.out.println(sbf); // Output: Hello World
```

### Summary Table

| Feature         | String | StringBuilder | StringBuffer |
| --------------- | ------ | ------------- | ------------ |
| Mutable         | No     | Yes           | Yes          |
| Thread Safe     | Yes    | No            | Yes          |
| Performance     | Low    | High          | Medium       |
| Synchronization | No     | No            | Yes          |

---

## 2. How does the Garbage Collector work in Java?

### Introduction

Garbage Collection (GC) is the process by which Java programs perform automatic memory management. The JVM automatically deletes objects that are no longer referenced to free up heap memory.

### JVM Memory Areas

* **Young Generation**:

  * Contains Eden and two Survivor spaces.
  * Most objects are created here.
  * Minor GC cleans this space.
* **Old Generation (Tenured)**:

  * Long-living objects are moved here.
  * Cleaned by Major GC.
* **MetaSpace**:

  * Stores class metadata.
  * Replaced PermGen since Java 8.

### Garbage Collection Types

* **Serial GC**: Single-threaded, for small applications.
* **Parallel GC**: Multi-threaded, good for medium to large applications.
* **CMS (Concurrent Mark Sweep)**: Minimizes pause time.
* **G1 GC**: Replaces CMS in modern applications. Divides heap into regions.
* **ZGC / Shenandoah**: Low-latency collectors in latest Java versions.

### How GC Works

1. **Mark**: Identifies which objects are reachable.
2. **Sweep**: Removes unreferenced objects.
3. **Compact**: Optional step to reorganize memory.

### Finalization

Deprecated method `finalize()` was called before GC.

```java
protected void finalize() throws Throwable {
    System.out.println("Object is garbage collected");
}
```

### Triggering GC Manually

```java
System.gc();
```

Note: JVM may or may not honor this request.

---

## 3. What is the significance of the `transient` keyword?

### Purpose

* Prevents fields from being serialized.
* Useful when you don’t want sensitive or temporary data to be saved during serialization.

### Use Case

* In serialization, all non-transient fields are saved to a stream.
* `transient` fields are skipped.

### Example

```java
class User implements Serializable {
    String username;
    transient String password;
}
```

### Why Use It?

* Security: Avoid storing passwords.
* Performance: Skip large objects not needed post-serialization.

---

## 4. What is method overloading and method overriding in Java?

### Method Overloading

* Multiple methods with same name but different parameters.
* Resolved at **compile-time** (static binding).

```java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
}
```

### Method Overriding

* Child class provides specific implementation of a method in the parent class.
* Resolved at **runtime** (dynamic binding).

```java
class Animal {
    void sound() { System.out.println("Animal Sound"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}
```

### Rules for Overriding

* Method signature must match.
* Access level can be less restrictive.
* Cannot override `final` or `static` methods.

---

## 5. What is the purpose of the `volatile` keyword in Java?

### Purpose

* Ensures that changes to a variable are immediately visible to other threads.
* Prevents caching of variable value in thread-local memory.

### Without `volatile`

* One thread may update a value.
* Other threads may keep reading a stale cached value.

### With `volatile`

* All threads read the variable directly from main memory.

### Example

```java
class SharedData {
    volatile boolean running = true;

    void runLoop() {
        while (running) {
            // do something
        }
    }
}
```

## 6. How does synchronization work in Java?

### Purpose

Synchronization ensures that only one thread can access a shared resource at a time. This helps prevent **race conditions** and ensures **data consistency** in multithreaded programs.

### Ways to Synchronize

1. **Synchronized Method**:

   ```java
   public synchronized void increment() {
       count++;
   }
   ```
2. **Synchronized Block**:

   ```java
   public void increment() {
       synchronized (this) {
           count++;
       }
   }
   ```
3. **Static Synchronization**:

   ```java
   public static synchronized void log() { }
   ```

### Java Concurrency API Alternatives:

* `Lock` interface (`ReentrantLock`)
* `Semaphore`, `CountDownLatch`, `CyclicBarrier`

### Synchronization Issues:

* Thread starvation
* Deadlock
* Reduced performance

---

## 7. What is the difference between Comparable and Comparator interfaces?

### Comparable

* Found in `java.lang`.
* Used to define **natural order**.
* Defines `compareTo()` method.
* Class must implement `Comparable<T>`.

```java
class Student implements Comparable<Student> {
    int marks;
    public int compareTo(Student other) {
        return this.marks - other.marks;
    }
}
```

### Comparator

* Found in `java.util`.
* Used for **custom orderings**.
* Defines `compare()` method.
* Used when multiple ways to sort are needed.

```java
Comparator<Student> byName = (s1, s2) -> s1.name.compareTo(s2.name);
Collections.sort(studentList, byName);
```

### Key Differences

| Feature         | Comparable        | Comparator           |
| --------------- | ----------------- | -------------------- |
| Package         | java.lang         | java.util            |
| Method          | compareTo()       | compare()            |
| Single/Multiple | Single sort logic | Multiple sort logics |
| Modified Class  | Yes               | No                   |

---

## 8. What is a static block and when is it executed?

### Definition

A **static block** is used for static initializations of a class. It is executed **once when the class is loaded into memory**.

### Use Cases

* Initializing static variables.
* Logging during class load.

### Example

```java
class App {
    static int counter;
    static {
        System.out.println("Static block executed");
        counter = 100;
    }
}
```

### Execution Order

1. Static variables and static blocks (in order).
2. Constructor during object creation.

---

## 9. How does exception handling work in Java using try, catch, finally, and throw?

### Keywords

* `try`: Defines a block of code that may throw exceptions.
* `catch`: Catches specific exceptions.
* `finally`: Executes whether or not an exception occurs.
* `throw`: Used to manually throw an exception.
* `throws`: Declares exceptions a method can throw.

### Example

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Divide by zero!");
} finally {
    System.out.println("Cleanup");
}

throw new IllegalArgumentException("Invalid input");
```

### Checked vs Unchecked

* Checked: Must be declared/handled (`IOException`).
* Unchecked: Runtime exceptions (`NullPointerException`).

---

## 10. What are lambda expressions and functional interfaces in Java 8?

### Lambda Expressions

* Introduced in Java 8.
* Syntax: `(params) -> expression` or `(params) -> { statements }`
* Enables treating behavior as data.

### Functional Interface

* Interface with **exactly one abstract method**.
* Annotated with `@FunctionalInterface` (optional but recommended).

### Example

```java
@FunctionalInterface
interface Calculator {
    int operation(int a, int b);
}

Calculator add = (a, b) -> a + b;
System.out.println(add.operation(10, 5));

Runnable r = () -> System.out.println("Running thread");
r.run();
```

### Common Built-in Functional Interfaces (java.util.function)

| Interface | Description                    |
| --------- | ------------------------------ |
| Predicate | Accepts T, returns boolean     |
| Function  | Accepts T, returns R           |
| Consumer  | Accepts T, returns void        |
| Supplier  | Returns a value, takes nothing |

---

### Limitations

* Does not guarantee atomicity.
* Use `synchronized` or `Atomic` classes for compound actions.

---
