# 📝 Topic 05: Binding Times, Parameter Passing & Aliasing
### Predicted Exam Questions & Complete Professor-Approved Solutions

---

## 📌 Question 1: Complete Binding Times Taxonomy (Direct 2024 Midsem Q2 Variant)

### Problem Statement
A **binding** is an association between two entities, such as an identifier and its type, or a variable and its memory location. **Binding time** refers to the exact phase in the software lifecycle at which this association occurs.

For each of the following 14 programmatic entities/attributes, identify the **exact binding time** from the 7 standard stages:
1. *Language Design Time*
2. *Language Implementation Time*
3. *Compile Time*
4. *Link Time*
5. *Load Time*
6. *Procedure Call (Activation) Time*
7. *Run Time (Execution Time)*

| # | Entity / Attribute to be Bound | Binding Time |
| :--- | :--- | :--- |
| a | The keyword `while` meaning a loop statement | ? |
| b | The maximum value an `int` variable can store in C (`INT_MAX`) | ? |
| c | The data type of a declared variable in Java (`int count;`) | ? |
| d | The relative byte offset of a local variable from base pointer `%rbp` | ? |
| e | The memory address of a global function called across separate `.c` files | ? |
| f | The absolute virtual memory starting address of a program binary | ? |
| g | The association between an actual argument and a formal parameter | ? |
| h | The target function address of a non-virtual C++ member function call | ? |
| i | The target function address of a `virtual` method call in C++ | ? |
| j | The runtime value stored inside a variable `x` | ? |
| k | The memory size allocated to a dynamically created array `new int[n]` | ? |
| l | The precedence of the multiplication operator `*` over addition `+` | ? |
| m | The resolution of symbols in a dynamically shared library (`.so` / `.dylib`) | ? |
| n | The activation record / stack frame storage for local automatic variables | ? |

---

### 💡 Model Solution

| # | Entity / Attribute | Correct Binding Time | Detailed Explanation |
| :--- | :--- | :--- | :--- |
| **a** | Meaning of `while` keyword | **Language Design Time** | The language designers specify keywords, grammar rules, and syntactic constructs in the language specification document. |
| **b** | Max value of `int` (`INT_MAX`) | **Language Implementation Time** | The language specification allows implementation choices (e.g., 16-bit vs 32-bit vs 64-bit). The compiler vendor determines word size for the target architecture. |
| **c** | Data type of `int count;` | **Compile Time** | In statically typed languages, the compiler parses the declaration and binds the identifier to the `int` type in its symbol table. |
| **d** | Local variable offset from `%rbp` | **Compile Time** | The compiler computes memory layout of the stack frame and fixes variable offsets (e.g., `-4(%rbp)`, `-8(%rbp)`). |
| **e** | Address of external global function | **Link Time** | The linker merges multiple translation units (`.o`), matches external function calls to their definitions, and patches relocation offsets. |
| **f** | Program absolute starting address | **Load Time** | The OS loader reads the executable file, maps segments into virtual memory, and assigns base memory addresses (e.g., ASLR). |
| **g** | Formal parameter to actual argument | **Procedure Call Time** | When the function call instruction executes, incoming argument values/addresses are passed into registers/stack. |
| **h** | Non-virtual C++ member function | **Compile Time** | Standard member functions are resolved statically at compile time (mangled name symbol). |
| **i** | `virtual` method call target | **Run Time** | Dynamic dispatch looks up the pointer in the instance’s `vtable` at the moment of invocation. |
| **j** | Value inside a variable | **Run Time** | Dynamic state changes continuously during program statement execution. |
| **k** | Memory size of `new int[n]` | **Run Time** | Because `n` is computed dynamically, heap allocation (`malloc`/`new`) occurs during runtime. |
| **l** | Operator precedence (`*` over `+`) | **Language Design Time** | Grammar rules and operator precedence tables are fixed by language designers. |
| **m** | Dynamic library resolution (`.so`) | **Load Time / Run Time** | Shared libraries are bound when the program is loaded or dynamically loaded via `dlopen()`. |
| **n** | Activation record stack allocation | **Procedure Call Time** | The stack frame memory is dynamically allocated when the prologue executes upon function entry. |

---

## 📌 Question 2: Parameter Passing Semantics Comparison

### Problem Statement
Contrast the following five parameter passing mechanisms:
1. **Pass-by-Value**
2. **Pass-Pointer-by-Value (C-style "pointer passing")**
3. **Pass-by-Reference (C++ style)**
4. **Pass-by-Value-Result (Copy-In / Copy-Out)**
5. **Pass-by-Name (Algol 60 Thunks)**

Complete the comparison matrix along:
* What is copied into the callee?
* Can the callee mutate the caller's original variable?
* When are mutations reflected in the caller?
* Primary performance/safety trade-offs.

---

### 💡 Model Solution

| Passing Mode | What is Copied? | Can Callee Mutate Caller Original? | When Mutation Reflects in Caller? | Trade-offs & Notes |
| :--- | :--- | :--- | :--- | :--- |
| **Pass-by-Value** | The bitwise representation (value) of the actual argument. | **NO.** Callee operates only on a private local copy. | Never (caller value unchanged). | Safe from unintended side effects; expensive copying for large structs/objects. |
| **Pass-Pointer-by-Value (C)** | The memory address of the caller's variable. | **YES**, by dereferencing `*ptr`. Callee cannot change caller's pointer itself. | **Immediately** as soon as `*ptr = val` executes. | Efficient (8-byte pointer); risks dangling pointers, null dereferencing, and unintentional mutations. |
| **Pass-by-Reference (C++)** | Nothing conceptually. Callee creates a language-level **alias** (reference) to caller's object. | **YES.** Directly modifying the parameter modifies the caller variable. | **Immediately** in real time. | Clean syntax (no `*` dereference needed), zero copying cost; creates aliasing vulnerabilities. |
| **Pass-by-Value-Result (Ada)** | Value copied into local variable on entry (**Copy-In**); final value copied back on exit (**Copy-Out**). | **YES.** But local operations do not affect caller during execution. | **Only upon procedure return** (function epilogue). | Protects against intermediate race conditions; behaves differently from pass-by-reference when aliasing exists. |
| **Pass-by-Name (Algol 60)** | An unevaluated parameter expression wrapped in a compiler-generated function called a **Thunk**. | **YES**, evaluated textually every single time the formal parameter is accessed. | Immediately upon every evaluation. | Enables Jensen’s Device; notoriously difficult to implement, unpredictable side effects. |

---

## 📌 Question 3: The Aliasing Trap (Direct 2025 Midsem Q3 Variant)

### Problem Statement
Consider the following pseudo-code procedure:

```pascal
var global_x: integer;

procedure Modify(var a: integer; var b: integer);
begin
    a := a + 1;
    global_x := global_x * 2;
    b := b + 3;
end;

begin
    global_x := 10;
    Modify(global_x, global_x);
    print(global_x);
end.
```

Determine the printed value of `global_x` under:
1. **Pass-by-Reference**
2. **Pass-by-Value-Result** (assuming parameters are copied out left-to-right: `a` then `b`)
3. **Pass-by-Value-Result** (assuming parameters are copied out right-to-left: `b` then `a`)
4. **Pass-by-Value**

Explain the exact step-by-step state of memory in each case.

---

### 💡 Model Solution

#### 1. Under Pass-by-Reference (Language-Level Aliasing)
* In pass-by-reference, `a`, `b`, and `global_x` all refer to the **exact same memory location**:
  - Initial: `global_x = 10`.
  - Step 1: `a := a + 1` -> Memory becomes `10 + 1 = 11`. (`global_x` is now 11).
  - Step 2: `global_x := global_x * 2` -> Memory becomes `11 * 2 = 22`. (`global_x` is now 22).
  - Step 3: `b := b + 3` -> Memory becomes `22 + 3 = 25`. (`global_x` is now 25).
* **Final Output: `25`**

#### 2. Under Pass-by-Value-Result (Copy-Out: Left-to-Right `a`, then `b`)
* On Procedure Entry (Copy-In):
  - Local `a = 10`
  - Local `b = 10`
  - `global_x = 10`
* Inside Procedure Body:
  - Step 1: `a := a + 1` -> Local `a` becomes `11`.
  - Step 2: `global_x := global_x * 2` -> `global_x` becomes `20`.
  - Step 3: `b := b + 3` -> Local `b` becomes `13`.
* On Procedure Exit (Copy-Out):
  - Copy `a` back to `global_x`: `global_x = a = 11`.
  - Copy `b` back to `global_x`: `global_x = b = 13` (overwrites `11`!).
* **Final Output: `13`**

#### 3. Under Pass-by-Value-Result (Copy-Out: Right-to-Left `b`, then `a`)
* On Procedure Exit (Copy-Out):
  - Copy `b` back to `global_x`: `global_x = b = 13`.
  - Copy `a` back to `global_x`: `global_x = a = 11` (overwrites `13`!).
* **Final Output: `11`**

#### 4. Under Pass-by-Value
* Local copies `a` and `b` receive `10`.
* `a := a + 1` modifies local `a` only.
* `global_x := global_x * 2` mutates `global_x` from `10` to `20`.
* `b := b + 3` modifies local `b` only.
* No values copied out upon return.
* **Final Output: `20`**

---

## 📌 Question 4: Deep Binding vs Shallow Binding

### Problem Statement
When a procedure/function is passed as a first-class parameter to another procedure:
1. What is the fundamental difference between **Deep Binding** and **Shallow Binding**?
2. Consider the following pseudo-code with dynamic scoping:

```pascal
program BindingDemo;
  var x: integer;

  procedure Sub1;
  begin
    print(x);
  end;

  procedure Sub2(procedure ParamFunc);
    var x: integer;
  begin
    x := 50;
    ParamFunc;
  end;

  procedure Sub3;
    var x: integer;
  begin
    x := 100;
    Sub2(Sub1);
  end;

begin
  x := 10;
  Sub3;
end.
```

What is printed if the language uses:
- **Deep Binding**?
- **Shallow Binding**?
- **Static Scoping**?

---

### 💡 Model Solution

#### 1. Definitions
* **Deep Binding:** The referencing environment of a passed procedure is bound at the time the procedure is **passed as an actual parameter** (or created as a closure).
* **Shallow Binding:** The referencing environment of a passed procedure is bound at the time the procedure is **actually invoked/called**.

#### 2. Analysis of the Example
* **Under Deep Binding:**
  - `Sub1` is passed inside `Sub3`.
  - At the moment `Sub1` is passed, the active environment has `x = 100` (declared in `Sub3`).
  - Thus, when `Sub1` executes inside `Sub2`, it uses the environment captured when it was passed:
  - **Output: `100`**

* **Under Shallow Binding:**
  - `Sub1` is called inside `Sub2`.
  - At the moment `Sub1` is actually called, the most recent binding on the stack is `x = 50` (declared in `Sub2`).
  - **Output: `50`**

* **Under Static (Lexical) Scoping:**
  - `Sub1` is lexically defined at the root program level (enclosed only by `BindingDemo`).
  - It resolves `x` textually from `BindingDemo` where `x = 10`.
  - **Output: `10`**
