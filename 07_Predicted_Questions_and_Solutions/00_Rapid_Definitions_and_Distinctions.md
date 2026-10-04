# ⚡ Rapid 1-Minute Definitions & Distinctions Cheat Sheet
### High-Yield 1–2 Mark Questions for POPL Exams

---

## 🏛️ 1. Abstraction, Paradigms & Foundations

### Core Definitions
* **Abstraction:** The process of separating *what* an entity does from *how* it is implemented, hiding unnecessary mechanical details to reduce cognitive burden on the programmer.
* **Referential Transparency:** A property of expressions where an expression can always be replaced by its evaluated value without changing the program's observable behavior (guarantees no side effects).
* **Finite Expressibility:** The core requirement of programmability; a problem or state transition can be mechanically computed if and only if it can be finitely described by static rules.
* **Intellectual Manageability (Dijkstra):** The discipline of designing program structures (e.g., single-entry/single-exit blocks) so human working memory can verify correctness and reason about execution invariants.
* **Bohm-Jacopini Theorem:** Any computable algorithm can be represented using only three control structures: Sequence, Selection (`if-else`), and Iteration (`while`).
* **First-Class Function:** A language entity that can be assigned to variables, passed as arguments to other functions, returned from functions, and stored in data structures.
* **Closure:** A first-class function paired with its lexical referencing environment captured at creation time.
* **Thunk:** A zero-argument compiler-generated wrapper function that delays the evaluation of an expression until its value is explicitly demanded (implements Call-by-Name).

### Pairwise Distinctions
* **Lexical Flow vs Syntax-Directed Flow:**
  - *Lexical Flow:* Sequential execution following the physical, textual order of statements from top to bottom.
  - *Syntax-Directed Flow:* Execution dictated by grammar structures (`if`, `while`, `call`, `break`), jumping across code.
* **Formal Language vs Programming Language:**
  - *Formal Language:* A mathematical set of strings defined purely by syntax/grammar rules without inherent meaning.
  - *Programming Language:* A formal language endowed with formal operational semantics and real computational effects on machine state.
* **Procedural vs Object-Oriented (Nygaard):**
  - *Procedural:* Passive data structures operated upon by active, centralized functions (`f(data)`).
  - *Object-Oriented:* A decentralized simulation of autonomous interacting entities managing their own private state through messages.

---

## 🧬 2. Types & Type Systems

### Core Definitions
* **Type:** A set of values along with a set of valid operations permitted on those values.
* **Type Safety:** The guarantee that a program will not execute invalid operations on a type (e.g., executing a data address as code or treating an integer as an arbitrary object pointer).
* **Incomplete Type:** A declared type whose internal size and member layout are unknown to the compiler (e.g., `struct Vault;`). Pointers to incomplete types are allowed; stack allocations are not.
* **Type Punning:** Bypassing the type system by reading the raw memory bits of a value as if it were a completely different type (e.g., `*((float*)&int_var)`).
* **Opaque Pointer Pattern:** Exposing a pointer handle to an incomplete struct in a public header while keeping the struct definition inside a private `.c` file, enforcing complete information hiding in C.

### Pairwise Distinctions
* **Static Typing vs Dynamic Typing:**
  - *Static Typing:* Types are checked at **compile time** (C, C++, Java). Type errors prevent binary generation.
  - *Dynamic Typing:* Types are associated with runtime values and checked at **run time** (Python, Lisp, JavaScript).
* **Strong Typing vs Weak Typing:**
  - *Strong Typing:* The language strictly prevents operations on incompatible types without explicit, validated casting (Python, Java).
  - *Weak Typing:* The language allows implicit conversions or raw memory reinterpretation, tolerating type violations (C, assembly).
* **Nominal Typing vs Structural Typing:**
  - *Nominal Typing:* Type equivalence is determined strictly by explicit declared **type names** (Java, C++). Two identical structs with different names are incompatible.
  - *Structural Typing:* Type equivalence is determined by the **shape and contents** of the type (TypeScript, Go). If two types have identical fields/signatures, they are equivalent.
* **Value Conversion vs Representation Reinterpretation:**
  - *Value Conversion (`(float)i`):* Transforms the bit pattern so the mathematical value is preserved in IEEE 754 format.
  - *Representation Reinterpretation (`*(float*)&i`):* Leaves the original bit pattern untouched and reads the bits as floating-point.

---

## 💾 3. Memory, Linkage & Procedure Activation

### Core Definitions
* **Activation Record (Stack Frame):** The dynamically allocated block of memory on the call stack representing a single invocation of a function.
* **Frame Pointer (`%rbp`):** The register that maintains an immovable base address for the current stack frame, providing constant offsets to locals and parameters.
* **Stack Pointer (`%rsp`):** The register pointing to the lowest allocated memory address of the current stack frame, which moves during pushes/pops.
* **Dangling Pointer:** A pointer that continues to store a memory address after the allocated object has been deallocated or returned from a dead stack frame.
* **Memory Leak:** Allocated heap memory that is no longer reachable by any active pointer in the program, preventing deallocation.
* **Segmentation Fault:** A hardware-generated exception triggered when a process attempts to access an unmapped or restricted virtual memory address.

### Pairwise Distinctions
* **Declaration vs Definition:**
  - *Declaration:* Informs the compiler of an identifier’s name and type interface (*what* it is) without allocating storage (`extern int x;`, `void f();`).
  - *Definition:* Allocates actual storage in memory or provides the executable code body (*how* it is implemented) (`int x = 10;`, `void f() { ... }`).
* **Static Link (Access Link) vs Dynamic Link (Control Link):**
  - *Dynamic Link:* Points to the activation record of the **caller**; used to pop frames upon return (follows runtime execution history).
  - *Static Link:* Points to the activation record of the **lexically enclosing scope**; used to access non-local variables (follows static source nesting).
* **Deep Binding vs Shallow Binding:**
  - *Deep Binding:* A passed function’s referencing environment is captured when the function is **passed as an argument** (or defined).
  - *Shallow Binding:* A passed function’s referencing environment is bound when the function is **actually called** at runtime.
* **Caller-Saved vs Callee-Saved Registers:**
  - *Caller-Saved (`%rax`, `%rdi`, `%rsi`):* Scratch registers; the caller must push them before issuing `call` if their values must survive.
  - *Callee-Saved (`%rbx`, `%rbp`, `%r12-%r15`):* Preserved registers; the callee must save them in its prologue and restore them in its epilogue.

---

## ☕ 4. Object-Oriented Design & C++ Mechanics

### Core Definitions
* **Encapsulation:** Grouping data and the operations that mutate that data into a cohesive unit while hiding internal implementation details behind an authorized public interface.
* **Object Slicing:** A phenomenon in C++ where passing a derived class instance by value to a base class parameter copies only the base sub-object, silently stripping away all derived member variables and virtual behavior.
* **Virtual Method Table (vtable):** A compiler-generated static array of function pointers created for each class containing virtual methods, enabling runtime dynamic dispatch.
* **Virtual Table Pointer (vptr):** A hidden pointer embedded in every instance of a polymorphic class pointing to the class's vtable.
* **Fragile Base Class Problem:** A structural flaw where modifications to base class methods or internal fields silently break the correctness and invariants of derived subclasses across large codebases.
* **Pure Virtual Function:** A virtual function declared with `= 0` in C++ that has no implementation in the base class, making the class abstract and obligating subclasses to provide an implementation.

### Pairwise Distinctions
* **Static Binding vs Dynamic Binding:**
  - *Static Binding (Early):* Function call target is resolved by the compiler at compile time based on the declared static type of the variable.
  - *Dynamic Binding (Late):* Method call target is resolved at runtime via the object's actual instance type using vtable lookup.
* **Class Inheritance ("is-a") vs Composition ("has-a"):**
  - *Inheritance:* White-box reuse; derived classes are tightly coupled to base class representation.
  - *Composition:* Black-box reuse; classes contain references to other independent components, favoring loose coupling and interface-based design.
* **Pass-by-Reference vs Pass-Pointer-by-Value:**
  - *Pass-Pointer-by-Value (C):* Copies the address into a local pointer variable; modifying `*p` alters caller memory, but reassigning `p` does not affect the caller.
  - *Pass-by-Reference (C++):* Creates a true syntactic and semantic alias for the caller variable; no copy of any kind is made conceptually.

---

## 🔮 5. Metaprogramming, Reflection & CLOS

### Core Definitions
* **Reflection:** The ability of a software system to observe, inspect, and dynamically modify its own structure and execution behavior at runtime.
* **Metaobject Protocol (MOP):** An object-oriented interface that models the language's own internal execution machinery (classes, slots, methods, generic dispatch) as first-class objects that programmers can customize.
* **Generic Function (CLOS):** A first-class function object that does not belong to any single class; it contains a collection of methods and uses multiple dispatch across all arguments.
* **Multiple Dispatch (Multimethods):** Selecting the concrete method implementation at runtime based on the dynamic types of **all** actual arguments, rather than just the receiver (`this`).

### Pairwise Distinctions
* **Introspection vs Intercession:**
  - *Introspection:* Read-only reflection; observing runtime types and metadata without modifying language behavior (Java `getClass()`, `instanceof`).
  - *Intercession:* Read-write reflection; dynamically altering execution semantics, method combinations, or class structures from within the running program (CLOS, Smalltalk).
* **Single Dispatch vs Multiple Dispatch:**
  - *Single Dispatch:* Only the first argument (receiver/`this`) determines method selection (C++, Java).
  - *Multiple Dispatch:* All arguments participate symmetrically in determining the most specific method (CLOS, Julia).
* **CLOS `:before` vs `:after` Method Combination:**
  - *`:before` Methods:* Executed from **most specific** (child) to **least specific** (parent) before the primary method.
  - *`:after` Methods:* Executed from **least specific** (parent) to **most specific** (child) after the primary method.
