# 📝 Topic 01: C Modules, Linkage, Storage & Separate Compilation
### Predicted Exam Questions & Complete Professor-Approved Solutions

---

## 📌 Question 1: Separate Compilation & Symbol Resolution (Direct 2024 Midsem Variant)

### Problem Statement
Consider a C project split into two files, `file1.c` and `file2.c`, compiled separately and linked together:

```c
/* file1.c */
int data[10];

void init() {
    for (int i = 0; i < 10; i++) data[i] = i * 2;
}
```

```c
/* file2.c */
#include <stdio.h>

extern int *data;

void init();

int main() {
    init();
    printf("%d\n", data[0]);
    return 0;
}
```

1. Does `file1.c` compile without errors? Does `file2.c` compile without errors?
2. Does the linker report an error when linking `file1.o` and `file2.o`? Explain why or why not.
3. What happens at runtime when the executable runs? Does it print `0`, print garbage, or crash (Segmentation Fault)? Explain the precise memory mechanism.
4. How should `file2.c` be corrected to properly access the array without crashing?

---

### 💡 Model Solution

#### 1. Compilation Phase
* **`file1.c`:** Compiles cleanly to `file1.o`. It allocates 40 bytes (assuming 4-byte `int`) in the `.bss` (or uninitialized data) segment and exports a global symbol `data`.
* **`file2.c`:** Compiles cleanly to `file2.o`. The compiler sees `extern int *data;` and assumes that somewhere in another module, there exists a pointer variable named `data` of type `int*` (8 bytes on a 64-bit architecture). The compiler takes a **"leap of faith"** and emits an unresolved relocation entry for symbol `data`.

#### 2. Linking Phase
* **Linker Behavior:** The linker **succeeds without any error**.
* **Reason:** In standard C object files (ELF/Mach-O), the symbol table records names and segment offsets, **not type signatures or sizes**. The linker simply sees that `file2.o` needs an external symbol named `data`, and `file1.o` defines an exported symbol named `data`. The linker binds the address of `file1.c`'s `data` array to the relocation entry in `file2.o`. The linker is completely **blind to the type mismatch**.

#### 3. Runtime Phase (The Crash Mechanism)
* **Outcome:** The program crashes with a **Segmentation Fault** (or bus error).
* **Precise Mechanism:**
  1. In `file1.c`, `data` is an array: `data[0] = 0`, `data[1] = 2`, etc. Memory layout at address `&data`:
     ```text
     Bytes 0-3:   0x00000000 (data[0] = 0)
     Bytes 4-7:   0x00000002 (data[1] = 2)
     ```
  2. In `file2.c`, the compiler generated machine instructions for accessing a pointer:
     - To evaluate `data[0]`, the CPU first dereferences `data` to read the pointer value:
       `movq data(%rip), %rax` (reads the first 8 bytes of `data` as an address).
     - The first 8 bytes of the array are `0x0000000200000000` (on little-endian, combining `data[0]` and `data[1]`).
     - Next, the CPU tries to access offset 0 from that address:
       `movl (%rax), %eax` -> tries to read memory address `0x0000000200000000`.
     - Because `0x0000000200000000` is an unmapped or privileged memory address in user space, the OS raises a page fault, terminating the process with a **Segmentation Fault**.

#### 4. The Correction
In `file2.c`, declare `data` as an unsized array rather than a pointer:
```c
extern int data[]; /* Correct: Tells compiler data is an array, not an indirect pointer */
```
With this declaration, `data[0]` generates direct indexed addressing:
`movl data(%rip), %eax`, which correctly accesses the integer `0`.

---

## 📌 Question 2: Linker "Leap of Faith" & Struct Inconsistencies

### Problem Statement
A system has two modules, `producer.c` and `consumer.c`. Because of poor version control, each developer wrote their own header rather than sharing a single header:

```c
/* producer.c */
struct Task {
    int id;
    int priority;
};

struct Task current_task = { 101, 5 };
```

```c
/* consumer.c */
#include <stdio.h>

struct Task {
    double timestamp;
    int id;
    int priority;
};

extern struct Task current_task;

void print_task() {
    printf("Task ID: %d, Priority: %d\n", current_task.id, current_task.priority);
}
```

1. Explain why the C compiler and linker allow this code to build without any warning or error.
2. What value does `print_task()` print for `id` and `priority`? Explain in terms of byte offsets.
3. What fundamental software engineering principle described by David Parnas is violated here, and what is the standard C idiom to prevent this?

---

### 💡 Model Solution

#### 1. Why Toolchain Allows This Build
* **C Compilation Model:** In C, each `.c` source file is an independent **compilation unit (translation unit)**. The compiler translates `producer.c` into `producer.o` using only the declarations visible in `producer.c`. It translates `consumer.c` into `consumer.o` similarly.
* **Linker Blindness:** Object files (`.o`) store symbol names, binding scopes (`GLOBAL`, `LOCAL`), and memory section offsets. They do **not** store struct layouts, member names, or member offsets. The linker only matches the name `current_task`. It assumes both modules agreed on the type — this is the **"Linker Leap of Faith"**.

#### 2. Byte Offset Breakdown & Output
Assuming a standard x86-64 LP64 model:
* **Memory Allocated in `producer.o` (8 bytes total):**
  - Offset +0 to +3 (4 bytes): `id = 101` (`0x00000065`)
  - Offset +4 to +7 (4 bytes): `priority = 5` (`0x00000005`)

* **Memory Layout Expected by `consumer.o` (16 bytes total):**
  - Offset +0 to +7 (8 bytes): `double timestamp`
  - Offset +8 to +11 (4 bytes): `int id`
  - Offset +12 to +15 (4 bytes): `int priority`

* **Runtime Output:**
  - `current_task.id` looks at offset **+8**. In `producer.o`, only 8 bytes were allocated! Offset +8 reads whatever adjacent data or padding follows `current_task` in the data segment (garbage value or zero).
  - `current_task.priority` looks at offset **+12**. Reads garbage memory.
  - **Output:** `print_task()` prints unpredictable garbage values (or `Task ID: 0, Priority: 0`), never `101` and `5`.

#### 3. Principle & Standard C Prevention
* **Violated Principle:** **Parnas Information Hiding & Single Source of Truth**. The internal data structure layout must be hidden or defined in exactly one authoritative interface contract.
* **Standard C Prevention:**
  1. Define `struct Task` in **exactly one header file** (`task.h`).
  2. `#include "task.h"` in both `producer.c` and `consumer.c`.
  3. Better yet, use the **Opaque Pointer Pattern** (see Question 3).

---

## 📌 Question 3: The Opaque Pointer Pattern (`FILE *` in C)

### Problem Statement
Standard I/O in C uses `FILE *` without ever requiring the programmer to know the fields or byte size of `struct _IO_FILE`.
1. Explain how the C language allows code to use a type without knowing its size or members. What is this type called?
2. Write a complete minimal implementation of an Abstract Data Type `Vault` in C using the opaque pointer idiom:
   - Provide `vault.h` (the public interface).
   - Provide `vault.c` (the private implementation hiding a `secret_key` and `balance`).
   - Provide `main.c` showing client usage.
3. State two distinct advantages of this design pattern.

---

### 💡 Model Solution

#### 1. Mechanism: Incomplete Types
C allows a forward declaration of a structure without its body:
```c
typedef struct Vault Vault; /* Incomplete type declaration */
```
The compiler does not know `sizeof(Vault)`, so the client **cannot** allocate a `Vault` as a local stack variable or access its fields (`v.secret` causes a compiler error). However, the size of a **pointer to a struct** (`Vault*`) is always fixed (8 bytes on 64-bit systems). Thus, client code can pass, return, and store `Vault*` handles without ever knowing the internal representation.

#### 2. Code Implementation

```c
/* ==================== vault.h (Public Interface) ==================== */
#ifndef VAULT_H
#define VAULT_H

/* Opaque Handle: Client knows Vault exists, but NOT what is inside it */
typedef struct Vault Vault;

Vault* vault_create(int initial_balance, int secret_key);
int    vault_deposit(Vault* v, int amount, int key);
int    vault_get_balance(const Vault* v, int key);
void   vault_destroy(Vault* v);

#endif
```

```c
/* ==================== vault.c (Private Implementation) ==================== */
#include "vault.h"
#include <stdlib.h>

/* Full definition: Visible ONLY inside vault.c */
struct Vault {
    int balance;
    int secret_key;
};

Vault* vault_create(int initial_balance, int secret_key) {
    Vault* v = (Vault*)malloc(sizeof(struct Vault));
    if (v) {
        v->balance = initial_balance;
        v->secret_key = secret_key;
    }
    return v;
}

int vault_deposit(Vault* v, int amount, int key) {
    if (!v || v->secret_key != key) return -1; /* Authorization failure */
    v->balance += amount;
    return v->balance;
}

int vault_get_balance(const Vault* v, int key) {
    if (!v || v->secret_key != key) return -1;
    return v->balance;
}

void vault_destroy(Vault* v) {
    free(v);
}
```

```c
/* ==================== main.c (Client Code) ==================== */
#include <stdio.h>
#include "vault.h"

int main(void) {
    Vault* my_vault = vault_create(5000, 1234);

    /* Direct field access is IMPOSSIBLE:
       printf("%d\n", my_vault->balance); // COMPILER ERROR: dereferencing pointer to incomplete type
    */

    vault_deposit(my_vault, 2000, 1234);
    printf("Balance: %d\n", vault_get_balance(my_vault, 1234)); // Prints: 7000

    vault_destroy(my_vault);
    return 0;
}
```

#### 3. Advantages
1. **Binary Compatibility & Decoupled Compilation:** The implementation of `struct Vault` in `vault.c` can be changed (e.g., adding encryption keys, audit logs, changing `int balance` to `double`) **without forcing `main.c` to recompile**. Only relinking is required.
2. **True Invariant Protection:** Client code cannot bypass security rules or corrupt internal states by directly writing to fields.

---

## 📌 Question 4: Tentative Definitions vs Duplicate Symbols

### Problem Statement
What will happen in each of the following scenarios when linking `a.o` and `b.o`?

* **Case 1:**
  `a.c`: `int count;`
  `b.c`: `int count;`
* **Case 2:**
  `a.c`: `int count = 10;`
  `b.c`: `int count = 20;`
* **Case 3:**
  `a.c`: `static int count = 10;`
  `b.c`: `int count = 20;`

Explain the concepts of **Strong Symbols**, **Weak Symbols (Tentative Definitions)**, and **Static Linkage Scope**.

---

### 💡 Model Solution

* **Case 1 (Two uninitialized definitions):**
  * **Result:** **Links successfully.** Both variables share the same memory location in the `.bss` / common block.
  * **Reason:** In C, an uninitialized global variable (`int count;`) is treated as a **tentative definition (weak symbol)**. The linker merges multiple weak symbols into a single common memory storage.

* **Case 2 (Two initialized definitions):**
  * **Result:** **Linker Error: Multiple definition of `count` (duplicate symbol).**
  * **Reason:** An initialized global variable (`int count = 10;`) is a **strong symbol**. Linker rules dictate that a strong symbol cannot be defined more than once in the same global namespace across translation units.

* **Case 3 (One static, one initialized global):**
  * **Result:** **Links successfully without conflict.**
  * **Reason:** The `static` keyword in C at file scope changes the linkage of `count` from **external linkage** to **internal linkage**. The symbol in `a.o` is marked `LOCAL` in the symbol table and is invisible to the linker when resolving external symbols for `b.o`. Both variables have distinct, independent memory locations.
