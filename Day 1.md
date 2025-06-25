# Java Interview Questions DAY 1

## 1. What is the difference between `==` and `.equals()` in Java?

### `==` Operator:

- Compares references (memory address) when used with objects.
- Compares actual value when used with primitive types.

### `.equals()` Method:

- Compares content (state) of two objects.
- Defined in `Object` class and overridden in classes like `String`, `List`, etc.

### Example:

```java
String s1 = new String("Hello");
String s2 = new String("Hello");
System.out.println(s1 == s2);        // false (different objects)
System.out.println(s1.equals(s2));   // true (same content)

int a = 10;
int b = 10;
System.out.println(a == b);          // true (primitive value check)
```

## 2. What are the key features of Java?

### Key Features:

- **Object-Oriented**: Everything is modeled as an object.
- **Platform Independent**: Compiled code (bytecode) runs on any OS with JVM.
- **Robust**: Automatic garbage collection, exception handling, and type-checking.
- **Secure**: Provides runtime security through classloaders and bytecode verification.
- **Multithreaded**: Supports concurrent execution using threads.
- **High Performance**: JIT (Just-In-Time) compiler boosts performance.
- **Portable**: Independent of architecture with consistent behavior.
- **Dynamic**: Classes can be loaded at runtime.
- **Distributed**: Built-in support for remote method invocation (RMI).

### Example:

```java
class Dog {
    String name;

    Dog(String name) {
        this.name = name;
    }

    void bark() {
        System.out.println(name + " says Woof!");
    }
}

public class Main {
    public static void main(String[] args) {
        Dog d = new Dog("Buddy");
        d.bark();
    }
}
```

## 3. What is the difference between ArrayList and LinkedList?

### ArrayList:

- Backed by a resizable array.
- Fast random access (`get` is O(1)).
- Inserting/deleting in middle is slow (O(n)).

### LinkedList:

- Backed by a doubly linked list.
- Slow random access (`get` is O(n)).
- Fast insertion/deletion at head or tail (O(1)).

### Example:

```java
List<String> arrayList = new ArrayList<>();
List<String> linkedList = new LinkedList<>();

arrayList.add("A");
linkedList.add("B");

System.out.println(arrayList.get(0)); // A
System.out.println(linkedList.get(0)); // B
```

## 4. How does Java memory management work?

### Memory Areas:

- **Heap**: Stores objects.
- **Stack**: Stores local variables and method calls.
- **Method Area**: Stores class metadata.
- **PC Register**: Stores address of current instruction.

### Garbage Collection (GC):

- JVM automatically clears unused objects.
- Types: Minor GC (young gen), Major GC (old gen).

### Example:

```java
public class Example {
    public static void main(String[] args) {
        String a = new String("Java");
        a = null;  // eligible for GC
        System.gc();
    }

    protected void finalize() {
        System.out.println("Object is being garbage collected");
    }
}
```

## 5. What is the difference between `final`, `finally`, and `finalize()`?

### `final`:

- Variable: Cannot be changed.
- Method: Cannot be overridden.
- Class: Cannot be extended.

### `finally`:

- Block that always executes after try/catch.
- Used for cleanup.

### `finalize()`:

- Method called by GC before object is destroyed (deprecated in newer Java).

### Example:

```java
final int x = 100; // constant

try {
    int a = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Error");
} finally {
    System.out.println("This always runs");
}

@Override
protected void finalize() throws Throwable {
    System.out.println("Object collected");
}
```

## 6. How does a HashMap work internally in Java?

### Structure:

- HashMap uses an array of buckets.
- Each bucket is a linked list (or red-black tree in Java 8+).
- Keys are hashed using `hashCode()` and mapped to an index.

### Process:

1. Key's `hashCode()` is called.
2. Index = hash % capacity.
3. If bucket is empty, insert.
4. If bucket has entries, use `.equals()` to find match.
5. If found, replace value. Else, add new node.

### Example:

```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 1);
map.put("banana", 2);
System.out.println(map.get("apple")); // 1
```

## 7. What are checked and unchecked exceptions in Java?

### Checked Exceptions:

- Checked at compile-time.
- Must be declared in method signature or handled.
- Example: `IOException`, `SQLException`

### Unchecked Exceptions:

- Subclass of `RuntimeException`.
- Occur at runtime.
- Example: `NullPointerException`, `ArithmeticException`

### Example:

```java
try {
    BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
} catch (IOException e) {
    e.printStackTrace();
}

int[] arr = new int[3];
System.out.println(arr[5]); // Unchecked: ArrayIndexOutOfBoundsException
```

## 8. How is multithreading implemented in Java?

### Ways:

- Extend `Thread` class.
- Implement `Runnable` interface.
- Use `ExecutorService` for thread pool.

### Synchronization:

- Use `synchronized` to prevent race conditions.
- Use `Lock`, `volatile`, `wait/notify` for advanced control.

### Example:

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}

Runnable r = () -> System.out.println("Runnable running");

Thread t1 = new MyThread();
Thread t2 = new Thread(r);
t1.start();
t2.start();
```

## 9. What is the difference between an abstract class and an interface in Java?

### Abstract Class:

- Can have both abstract and concrete methods.
- Can have fields and constructors.
- Single inheritance.

### Interface:

- Methods are abstract by default (Java 7).
- Java 8+: Can have default and static methods.
- Multiple interfaces can be implemented.

### Example:

```java
abstract class Animal {
    abstract void makeSound();
    void eat() { System.out.println("Eating"); }
}

interface Flyable {
    void fly();
}

class Bird extends Animal implements Flyable {
    void makeSound() { System.out.println("Chirp"); }
    public void fly() { System.out.println("Flying"); }
}
```

## 10. What are the SOLID principles in object-oriented programming?

### 1. Single Responsibility Principle (SRP):

- A class should have only one reason to change.
- Break large classes into smaller, more focused ones.

### 2. Open/Closed Principle (OCP):

- Code should be open for extension, closed for modification.
- Use abstract classes/interfaces.

### 3. Liskov Substitution Principle (LSP):

- Subclasses must be substitutable for base classes.
- Don't override behavior in a way that breaks expectations.

### 4. Interface Segregation Principle (ISP):

- No class should be forced to implement unused interfaces.
- Prefer small, client-specific interfaces.

### 5. Dependency Inversion Principle (DIP):

- High-level modules should not depend on low-level modules.
- Use interfaces or abstract classes for dependencies.

### Example:

```java
interface Keyboard {
    void type();
}

class WiredKeyboard implements Keyboard {
    public void type() { System.out.println("Typing on wired keyboard"); }
}

class Computer {
    private Keyboard keyboard;

    Computer(Keyboard keyboard) {
        this.keyboard = keyboard;
    }

    void use() {
        keyboard.type();
    }
}

public class Main {
    public static void main(String[] args) {
        Keyboard kb = new WiredKeyboard();
        Computer comp = new Computer(kb);
        comp.use();
    }
}
```
