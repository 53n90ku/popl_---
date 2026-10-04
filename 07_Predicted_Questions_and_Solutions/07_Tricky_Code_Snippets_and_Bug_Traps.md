# 🐛 15 Tricky Code Snippets, "What Prints?" & Bug Traps
### High-Yield Exam Code Walkthroughs with Exact Explanations

---

## 💣 Trap 1: The Missing Virtual Destructor (Resource Leak)

### Code Snippet
```cpp
#include <iostream>

class Base {
public:
    Base() { std::cout << "Base constructed\n"; }
    ~Base() { std::cout << "Base destroyed\n"; } // NOT VIRTUAL!
};

class Derived : public Base {
private:
    int* buffer;
public:
    Derived() { 
        buffer = new int[100]; 
        std::cout << "Derived constructed\n"; 
    }
    ~Derived() { 
        delete[] buffer; 
        std::cout << "Derived destroyed\n"; 
    }
};

int main() {
    Base* ptr = new Derived();
    delete ptr; // WHAT HAPPENS HERE?
    return 0;
}
```

### Output
```text
Base constructed
Derived constructed
Base destroyed
```

### The Explanation
* Because `~Base()` is **not virtual**, the compiler uses **static binding** based on the static type of `ptr` (`Base*`).
* It statically calls `Base::~Base()` directly, completely skipping `Derived::~Derived()`.
* **The Bug:** `delete[] buffer` is never called, resulting in an immediate **heap memory leak** and undefined behavior.
* **The Fix:** Always declare base class destructors as `virtual ~Base() {}`.

---

## 💣 Trap 2: Object Slicing in C++

### Code Snippet
```cpp
#include <iostream>

class Animal {
public:
    virtual void speak() const { std::cout << "Generic Animal\n"; }
};

class Dog : public Animal {
public:
    void speak() const override { std::cout << "Woof!\n"; }
};

void describe(Animal a) { // PASS BY VALUE!
    a.speak();
}

int main() {
    Dog d;
    describe(d); // What prints?
    return 0;
}
```

### Output
```text
Generic Animal
```

### The Explanation
* `describe(Animal a)` takes parameter `a` **by value**, not by reference or pointer.
* When `d` is passed, the compiler invokes `Animal`'s copy constructor, slicing off the `Dog` parts.
* Parameter `a` has the dynamic and static type `Animal` with its `vptr` pointing to `Animal`'s vtable.
* **The Fix:** Pass by reference: `void describe(const Animal& a)`.

---

## 💣 Trap 3: Returning Address of Local Automatic Stack Variable

### Code Snippet
```c
#include <stdio.h>

int* create_integer() {
    int val = 42;
    return &val; // SPOT THE BUG!
}

void overwrite_stack() {
    int garbage[10] = {99, 99, 99};
}

int main() {
    int *p = create_integer();
    overwrite_stack();
    printf("%d\n", *p); // What happens?
    return 0;
}
```

### Output / Behavior
* **Result:** Prints garbage (e.g., `99`) or crashes with a Segmentation Fault.
* **The Explanation:**
  - `val` is allocated on the activation record (stack frame) of `create_integer()`.
  - When `create_integer()` returns, its stack frame is popped (`leave; ret`). The memory address is now dead and subject to overwrite.
  - Calling `overwrite_stack()` claims that exact memory region on the stack, corrupting the pointee.
  - **Concept:** Pointer `p` becomes a **Dangling Pointer**.

---

## 💣 Trap 4: Pointer to Array vs Array of Pointers

### Code Snippet
```c
#include <stdio.h>

int main() {
    int arr[3] = {10, 20, 30};

    int *p1[3];     // Type A
    int (*p2)[3];   // Type B

    p2 = &arr;
    printf("%d\n", (*p2)[1]); // What prints?
    return 0;
}
```

### Output
```text
20
```

### The Explanation
* `int *p1[3]` is an **Array of 3 integer pointers** (subscript `[]` binds tighter than `*`).
* `int (*p2)[3]` is a **Pointer to an array of 3 integers** (parentheses override precedence).
* `p2 = &arr` binds `p2` to the address of the entire array block.
* `(*p2)[1]` dereferences `p2` to get array `arr`, then accesses index `1` (`20`).

---

## 💣 Trap 5: Calling a Virtual Function Inside a C++ Constructor

### Code Snippet
```cpp
#include <iostream>

class Base {
public:
    Base() { test(); } // Virtual call in constructor!
    virtual void test() { std::cout << "Base::test\n"; }
};

class Derived : public Base {
public:
    Derived() {}
    void test() override { std::cout << "Derived::test\n"; }
};

int main() {
    Derived d; // Does it print Base::test or Derived::test?
    return 0;
}
```

### Output
```text
Base::test
```

### The Explanation
* In C++, while `Base`'s constructor is executing, the `Derived` part of the object has **not yet been constructed**.
* Therefore, the C++ runtime sets the object's `vptr` to point to `Base`'s vtable during `Base`'s construction.
* Virtual dispatch behaves as **static dispatch** during constructors/destructors. (Contrast with Java, where this would polymorphically call `Derived.test()`!).

---

## 💣 Trap 6: Undefined Evaluation Order of Arguments in C

### Code Snippet
```c
#include <stdio.h>

void print_two(int a, int b) {
    printf("%d %d\n", a, b);
}

int main() {
    int i = 5;
    print_two(i++, i++); // WHAT DOES THIS PRINT?
    return 0;
}
```

### Output / Behavior
* **Result:** **Undefined Behavior (Unspecified Order of Evaluation).**
* **The Explanation:**
  - The C standard explicitly states that the order in which function arguments are evaluated is **unspecified**.
  - On some compilers (evaluating right-to-left), it prints `6 5`. On others (left-to-right), it prints `5 6`.
  - Furthermore, modifying a scalar variable twice between sequence points induces undefined behavior.

---

## 💣 Trap 7: Pass-by-Reference Aliasing Bug (The XOR Swap Trap)

### Code Snippet
```cpp
#include <iostream>

void xor_swap(int& a, int& b) {
    a = a ^ b;
    b = a ^ b;
    a = a ^ b;
}

int main() {
    int x = 15;
    xor_swap(x, x); // ALIASING TRAP!
    std::cout << x << "\n"; // What prints?
    return 0;
}
```

### Output
```text
0
```

### The Explanation
* `a` and `b` both alias the **exact same memory location** `x`.
* Step 1: `a = a ^ b` evaluates `x = x ^ x`, which is `0`.
* Step 2: `b = a ^ b` evaluates `0 ^ 0 = 0`.
* Step 3: `a = a ^ b` evaluates `0 ^ 0 = 0`.
* **The Lesson:** Aliasing in pass-by-reference can silently violate algebraic invariants.

---

## 💣 Trap 8: Static Local Variable Initialization Timing

### Code Snippet
```c
#include <stdio.h>

int counter() {
    static int count = 10; // When is this bound and initialized?
    count += 5;
    return count;
}

int main() {
    printf("%d ", counter());
    printf("%d ", counter());
    printf("%d\n", counter());
    return 0;
}
```

### Output
```text
15 20 25
```

### The Explanation
* `static int count = 10;` is allocated in the global `.data` segment at **compile/load time**, NOT on the stack frame.
* It is initialized **exactly once** before program execution begins.
* When `counter()` exits, `count` retains its value in memory across invocations.

---

## 💣 Trap 9: C++ Shallow Copy Double-Free Disaster

### Code Snippet
```cpp
#include <iostream>

class DynamicArray {
public:
    int* data;
    DynamicArray() { data = new int(100); }
    ~DynamicArray() { delete data; } // No custom copy constructor!
};

void process(DynamicArray copy) { // Pass by value
    // copy goes out of scope here -> calls ~DynamicArray() -> deletes data!
}

int main() {
    DynamicArray arr;
    process(arr);
    // arr goes out of scope here -> calls ~DynamicArray() -> CRASH!
    return 0;
}
```

### Output / Behavior
* **Result:** Program crashes with `free(): double free detected`.
* **The Explanation:**
  - Because no custom copy constructor was written, C++ provides a default **bitwise shallow copy**.
  - `copy.data` holds the exact same pointer address as `arr.data`.
  - When `process()` ends, `copy` is destroyed, freeing `data`.
  - When `main()` ends, `arr` tries to delete `data` a second time, triggering an immediate abort.
  - **Rule of Three / Five:** If a class manages a raw resource, you must define a Copy Constructor, Copy Assignment Operator, and Destructor.

---

## 💣 Trap 10: Java Reference Passing Semantics

### Code Snippet
```java
class Box { int val; }

public class Main {
    public static void modify(Box b) {
        b.val = 50;           // Mutation 1
        b = new Box();        // Reassignment
        b.val = 100;          // Mutation 2
    }

    public static void main(String[] args) {
        Box box = new Box();
        box.val = 10;
        modify(box);
        System.out.println(box.val); // What prints?
    }
}
```

### Output
```text
50
```

### The Explanation
* **Java is strictly Pass-by-Value!**
* Object variables hold reference handles. When `box` is passed to `modify(Box b)`, the **reference handle is copied by value**.
* `b.val = 50` dereferences the shared heap object, successfully changing its value to `50`.
* `b = new Box()` reassigns the local parameter copy `b` to point to a new heap object. The caller's `box` variable in `main()` still points to the original object!
* `b.val = 100` modifies only the newly created object.

---

## 💣 Trap 11: Struct Memory Alignment & Padding

### Code Snippet
```c
#include <stdio.h>

struct MixedData {
    char a;     // 1 byte
    int b;      // 4 bytes
    char c;     // 1 byte
};

int main() {
    printf("%lu\n", sizeof(struct MixedData)); // What prints?
    return 0;
}
```

### Output
```text
12
```

### The Explanation
* Naive arithmetic suggests $1 + 4 + 1 = 6$ bytes. But x86-64 hardware requires 4-byte alignment for integers:
  - Offset 0: `char a` (1 byte)
  - Offset 1-3: **3 bytes of padding** (so `int b` aligns on a 4-byte boundary)
  - Offset 4-7: `int b` (4 bytes)
  - Offset 8: `char c` (1 byte)
  - Offset 9-11: **3 bytes of trailing padding** (so array elements align to a multiple of largest member size: 4)
* Total size: $1 + 3 + 4 + 1 + 3 = \mathbf{12\text{ bytes}}$.

---

## 💣 Trap 12: The Classic Macro Precedence Trap

### Code Snippet
```c
#include <stdio.h>

#define SQUARE(x) x * x

int main() {
    int result = SQUARE(2 + 3); // What prints?
    printf("%d\n", result);
    return 0;
}
```

### Output
```text
11
```

### The Explanation
* Preprocessor macros perform **blind textual substitution** before compilation:
  `SQUARE(2 + 3)` expands textually to:
  `2 + 3 * 2 + 3`
* Due to operator precedence, multiplication occurs first:
  `2 + (3 * 2) + 3 = 2 + 6 + 3 = 11` (NOT $5^2 = 25$!).
* **The Fix:** Always wrap macro parameters and the full expression in parentheses:
  `#define SQUARE(x) ((x) * (x))`.

---

## 💣 Trap 13: Dynamic Scoping vs Lexical Scoping Output

### Code Snippet
```pascal
program ScopeDemo;
  var x: integer = 10;

  procedure print_x;
  begin
    writeln(x);
  end;

  procedure caller;
    var x: integer = 99;
  begin
    print_x;
  end;

begin
  caller;
end.
```

### Output Comparison
* **Under Lexical (Static) Scoping (C, Java, Pascal, Python):**
  - Prints: **`10`**
  - `print_x` searches its static parent (`ScopeDemo`), where `x = 10`.
* **Under Dynamic Scoping (Classic Lisp, Bash, Perl `local`):**
  - Prints: **`99`**
  - `print_x` searches up the runtime call stack to its caller (`caller`), where `x = 99`.

---

## 💣 Trap 14: Java Polymorphic Array Covariance Trap

### Code Snippet
```java
public class ArrayTrap {
    public static void main(String[] args) {
        String[] strArray = new String[5];
        Object[] objArray = strArray; // Legal in Java (Array Covariance)

        objArray[0] = Integer.valueOf(100); // What happens here?
    }
}
```

### Output / Behavior
* **Result:** Compiles cleanly, but throws runtime **`java.lang.ArrayStoreException`**.
* **The Explanation:**
  - Java arrays are **covariant** (`String[]` is considered a subtype of `Object[]`).
  - However, the underlying heap object is still a `String[]`.
  - The JVM performs a runtime type check on every array store instruction (`aastore`). Storing an `Integer` into a `String[]` triggers an immediate runtime exception.

---

## 💣 Trap 15: CLOS Method Combination Trace

### Code Snippet
```lisp
(defclass vehicle () ())
(defclass car (vehicle) ())

(defgeneric start (obj))

(defmethod start :before ((c car)) (format t "Car before~%"))
(defmethod start :before ((v vehicle)) (format t "Vehicle before~%"))

(defmethod start ((c car)) (format t "Car primary~%"))

(defmethod start :after ((c car)) (format t "Car after~%"))
(defmethod start :after ((v vehicle)) (format t "Vehicle after~%"))
```
When `(start (make-instance 'car))` is called:

### Output
```text
Car before
Vehicle before
Car primary
Vehicle after
Car after
```

### The Explanation
* **`:before` methods:** Executed from **most specific** to **least specific**:
  `car` is more specific than `vehicle` -> prints `Car before`, then `Vehicle before`.
* **Primary method:** Most specific primary method executes -> prints `Car primary`.
* **`:after` methods:** Executed from **least specific** to **most specific**:
  `vehicle` is less specific than `car` -> prints `Vehicle after`, then `Car after`.
