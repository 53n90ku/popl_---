# PoPL (CS F301) — Past Midsem Papers: Complete Solutions

Two papers: **(A) 2024 Midsem (5 Oct 2024, 70 marks)** and **(B) 2025 Midsem (8 Oct 2025, 60 marks)**.

**How I checked these:** I rebuilt the 2024 modules and compiled/linked/ran them with gcc 13, and tested every 2025 Q2 addition with g++ (real linker output). Anything marked ✅ **VERIFIED** was actually run.

**How each answer is laid out:**
- **✍️ Write this in the exam:** the short answer the examiner wants.
- **Why / explanation:** so you understand it and can handle a variation of the question.

> **Pattern across both papers:** the professor tests **module independence, type information at compile time vs link time, name binding, and procedure activation (stack frames)**. Both papers are the *same idea* from the lecture: *the compiler sees one module at a time; the linker sees only global names.* Learn that sentence and you can derive most answers.

---

# PART A — 2024 MIDSEM (all 7 questions on the same two modules)

## The code, cleaned up (readable version)

**Module1.c**
```c
1  #include<malloc.h>
2  struct Abstract;
3  typedef struct Abstract * Class;
4  extern void Subroutine1(Class, const char*, int);
5  extern void Subroutine2(Class, const char*, int);
6  struct Abstract {
7     void *abstractData;
8     Class (*Abstract)(void *abstractData);
9     void (**abstractMethod)(Class this, ...);
10 };
11 #define ClassSize sizeof(struct Abstract)
12 Class object1;
13 int main(void) {
14   object1 = (Class) malloc(ClassSize);
15   object1->abstractData = malloc(256);
16   object1->abstractMethod = (void (**)(Class, ...)) malloc(2*sizeof(void (*)(Class, ...)));
17   object1->abstractMethod[0] = ((void (*)(Class, ...))&Subroutine1);
18   object1->abstractMethod[1] = (void (*)(Class, ...))&Subroutine2;
19   object1->abstractMethod[0](object1, "", 0);
20   object1->abstractMethod[1](object1, "", 0);
21   Class object2 = object1;
22   Class object1 = object2;
23   if (f(10)) {
24     Class object1 = object2;
25     object2->Abstract = (Class (*)(void*))&Subroutine1;
26     if (g(10)) {
27       Class object2 = object1;
28       Class object1 = object2;
29     }
30   }
31   object1 = object1->Abstract(object1);
32   object2 = object1->Abstract(object2);
33   return sizeof(object1);
34 }
```

**Module2.c**
```c
1  struct Abstract;
2  typedef struct Abstract * Class;
3  void Subroutine1(Class c, const char *s, int i) { return; }
4  void Subroutine2(Class c, const char *s, float f) { return; }
5  int f(int n) { if(n>0) return f(n-1)>g(n-1) ? g(n-2) : f(n-2); return 0; }
6  int g(int n) { if(n>0) return g(n-1)<f(n-1) ? f(n-2) : g(n-2); return 0; }
```

## First, understand the program (needed for every question)

**What Module1 does:**
- Line 6–10: defines a struct called `Abstract` with **three members**: `abstractData` (a `void*`), `Abstract` (a **function pointer**, same name as the struct tag, which is legal because struct tags and member names live in different "name spaces"), and `abstractMethod` (a pointer to an **array of function pointers**).
- `Class` is just a pointer to that struct.
- `main` builds an "object" by hand with `malloc`, fills the function-pointer array with `Subroutine1` and `Subroutine2`, and calls them. This is **object-orientation done manually in C** (a hand-made vtable).

**Traps hidden in the code:**
1. **Module2 never sees the struct's body.** It only sees `struct Abstract;` (an **incomplete type**) and `Class` (a pointer to it).
2. **`f` and `g` are never declared in Module1** (no prototype) → **implicit declaration**.
3. **`Subroutine2` has different signatures in the two modules**: `int` in Module1's `extern`, `float` in Module2's definition.
4. **Variable shadowing**: `object1` and `object2` are re-declared in nested blocks (lines 22, 24, 27, 28). Each new declaration creates a **new variable** that hides the older one.
5. **`f` and `g` always return 0** (every path ends at `return 0`), so the `if` blocks at lines 23 and 26 are skipped, and `Abstract` (function pointer) is never set.

### Scope table (needed for Q3(a), Q5, Q6)
| Name | Declared at | Which variable it means |
|---|---|---|
| `object1` (global) | line 12 | used on lines 14–21 (before the local one exists) |
| `object2` (local to `main`) | line 21 | used from line 21 to the end of `main` (except where shadowed at 27) |
| `object1` (local to `main`) | line 22 | initialised with `object2` (= same pointer as global `object1`); this is the `object1` used on lines **31, 32, 33** |
| `object1` (inner block of first `if`) | line 24 | only inside lines 24–30; **never used afterwards** |
| `object2` (inner-inner block) | line 27 | only inside lines 27–29 |
| `object1` (inner-inner block) | line 28 | only inside lines 28–29; **unused** (this is the compiler's "unused variable" warning) |

> Line 25 uses `object2`, which at that point is the `main`-level `object2` from line 21 (the one on line 27 does not exist yet).

---

## Q1 [10] Will each module compile independently?

**✍️ Write this in the exam:**
**Yes, both compile independently** (`gcc -c Module1.c`, `gcc -c Module2.c`), producing object files, with only **warnings** (implicit declaration of `f` and `g`; unused variable).

**Why (explain):**
1. **Compilation only needs static type information that is available in each module.**
   - **Module1** has the full definition of `struct Abstract` (so `sizeof(struct Abstract)` and `->abstractData` etc. are fine). `Subroutine1/2` are declared with `extern` prototypes. `malloc` is declared in `<malloc.h>`.
   - **Module2** never creates an `Abstract` and never dereferences a `Class`. It only receives a `Class` **pointer**. **Passing a pointer needs only the pointer's size, not the size/layout of the thing pointed to.** The storage size/layout of a type is needed **only when you dereference the pointer or pass/allocate the value itself**. So the **incomplete type** is enough.
2. **Calls to `f` and `g` with no prototype (Module1 lines 23, 26; Module2 line 5 for `g`)** are treated as **implicit declarations** (`int f()` assumed). The compiler warns but generates a normal call. The definitions (in Module2) are compatible (`int` return).
3. **How each module is processed** (the "how if yes" part):
   - **Preprocessing:** `#include<malloc.h>` is pasted in; `ClassSize` is replaced by `sizeof(struct Abstract)`.
   - **Compilation:** checks types using only what is visible in that file; converts names of locals/parameters into **frame offsets** and member names into **struct offsets** (0, 8, 16).
   - **Assembly → object file:** names that are **not defined here** (`f`, `g`, `Subroutine1`, `Subroutine2`, `malloc` in Module1) become **undefined symbols**, to be resolved by the linker. Names defined here (`main`, `object1` in Module1; `Subroutine1/2`, `f`, `g` in Module2) become **exported global symbols**.

✅ **VERIFIED** with `gcc -c -Wall`: warnings only: *implicit declaration of `f` (23), `g` (26), unused variable `object1` (28)* in Module1, and *implicit declaration of `g`* in Module2 line 5. Object files produced. `nm` shows:
- `Module1.o`: **U** Subroutine1, Subroutine2, f, g, malloc; **T** main; **B** object1
- `Module2.o`: **T** Subroutine1, Subroutine2, f, g

> **Examiner's note (from the official solution):** the professor explicitly says that students who **argue from a particular ISO standard** ("implicit declaration is illegal in C99") or from **missing headers** *may not get credit*. He wants the **practical, type-system/modularity** answer. **Do not argue about syntax or standards. Say: compiles with warnings.** (Side note: gcc 14+ makes implicit declaration a hard error by default; the exam answer is still "compiles, with warnings", as the transcript in the official solution shows.)

---

## Q2 [10] Will `gcc Module1.c` alone give an executable? Will the two object files link?

**✍️ Write this in the exam:**
- **`gcc Module1.c` alone: NO.** The linker reports **undefined references** to the four functions `f`, `g`, `Subroutine1`, `Subroutine2` (their bodies are not in Module1).
- **Module1.o + Module2.o together: YES**, they link into an executable.

**Why (explain):**
- Linking is **name matching only**. The linker looks at the **global symbol names** (not types).
  - Module1 needs: `f`, `g`, `Subroutine1`, `Subroutine2` (+ `malloc` from libc, resolved automatically).
  - Module2 provides exactly these four, **once each**, so every undefined reference gets exactly one definition: no "undefined" and no "multiple definition".
  - `main` is in Module1 (the entry point exists). `object1` is defined only in Module1.
- **Type mismatches are invisible at link time.** In C, symbols are not mangled with types, so Module1's `Subroutine2(Class, const char*, int)` and Module2's `Subroutine2(Class, const char*, float)` match by the name `Subroutine2` alone. **Neither the compiler (one module at a time) nor the linker (names only) can detect the mismatch.** (It would cause wrong-argument-register bugs at run time. This is the same lesson as the "partially hidden class" in the lecture.)

✅ **VERIFIED:** linking `Module1.c` alone gives `undefined reference to Subroutine1, Subroutine2, f, g` (and ld stops with exit status 1). Linking both objects succeeds.

> The official solution has the same answer: *"alone, the linker will give four errors … the two modules together are a complete program."* (Four distinct symbols; `Subroutine1` is referenced twice, so you may see five error lines.)

---

## Q3 [5+5]

### (a) What replaces `ClassSize`? What does `main` return?

**✍️ Write this in the exam:**
- **`ClassSize` = `sizeof(struct Abstract)` = 3 × `sizeof(void*)`** (three pointer members: `abstractData`, `Abstract`, `abstractMethod`), i.e. **24 bytes** on a 64-bit machine, assuming all pointers are the same size.
- **`main` returns `sizeof(object1)` = `sizeof(Class)` = 1 × `sizeof(void*)`** = **8 bytes** (on 64-bit).

**Why:**
- A struct's size is the sum of its members (plus padding): here **three pointers**. A data pointer and a function pointer are the same size on typical machines.
- **The trap:** line 33 is `sizeof(object1)`, and `object1` is a **`Class`, which is a pointer**, **not** the struct. So it is **one pointer** (8 bytes), **not** `ClassSize` (24). Whichever `object1` is meant (global or local), both are `Class`, so the answer is the same.
- Always **state the unit** (bytes, or multiples of `sizeof(void*)`), as the question demands. The *numeric* exit status is therefore 8, if the program gets to the end (see Q6: it does only after the `return 1` change).

✅ **VERIFIED:** `sizeof(struct)` = 24, pointer = 8; modified program exits with status 8.

### (b) One rule for when a space is needed in C source

**✍️ Write this in the exam (one sentence):**
> **A space is needed only between two adjacent tokens that would otherwise be read as one longer token (e.g. between two identifiers/keywords/constants like `int x`, `return 0`), i.e. after an identifier or literal constant that is not already followed by a non-space delimiter (a punctuator, bracket, quote or operator).**

**Why:**
- The lexer uses **"maximal munch"**: it takes the longest possible token. `intx` would be one identifier, so we write `int x`. But `x=5;` needs no spaces because `=` and `;` are delimiters. This is exactly what the official solution says: *after each identifier and literal constant that does not end in a non-space delimiter, whitespace is needed.*
- Extra cases that follow from the same rule: `a+ +b` (otherwise `++`), `a- -b`, `/ *` (otherwise a comment start).

---

## Q4 [10] Which lines produce imperative action code?

**✍️ Write this in the exam:**
- **Module1.c: lines 13–28 and 31–33.** (Line 13 = function entry/preamble; lines 14–20 = calls/assignments; 21, 22, 24, 27, 28 = **initialisations, which are executed assignments**; 23, 26 = conditions; 25 = assignment; 31–33 = assignments and the return.) *(Closing braces 29, 30 only mark jump targets; the closing brace on 34 is where the epilogue `leave; ret` is attributed in `gcc -g -S` output.)*
- **Module2.c: lines 3–6** (each is a function definition with a body).

**Why only these:**
- **Imperative code = code executed when control reaches it** (the lecture's definition).
- **Not imperative** (compile-time/static information only): `#include`, **forward declarations** (`struct Abstract;`), **typedefs**, **`extern` prototypes**, **struct definitions**, **`#define`**, and the **global variable definition `Class object1;` (line 12)**: static storage is reserved in the data/bss area, no instruction runs for it. In Module2, lines 1–2 are only type declarations.
- **Local declarations with initialisers (21, 22, 24, 27, 28) *do* generate code**, because they copy a value at run time each time the block is entered.

---

## Q5 [10] Binding times and which identifiers appear in the assembly

**Meaning of the times (recap):** *preprocessing* (macros) → *compile time* (names of types/members turned into sizes/offsets) → *linking time* (global names matched to addresses/definitions) → *loading time* (final addresses, dynamic libraries) → *procedure activation time* (parameters and locals get real storage on each call) → *run time* (values/pointers that depend on execution).

**✍️ Write this in the exam (the table, matching the official answer):**

| Identifier | Where declared | Binding time |
|---|---|---|
| `Abstract` (struct tag) | M1 lines 2, 6; M2 line 1 | **Compile time** |
| `Class` (typedef) | M1 line 3; M2 line 2 | **Compile time** |
| `abstractData` (member) | M1 line 7 | **Compile time** (name → offset 0) |
| `abstractData` (parameter name inside the function-pointer type) | M1 line 8 | **Procedure activation time** |
| `Abstract` (member, function pointer) | M1 line 8 | **Compile time** (name → offset 8); its *value* is bound at **run time** |
| `abstractMethod` (member) | M1 line 9 | **Compile time** (name → offset 16); its *contents* bound at **run time** (lines 16–18) |
| `this` (parameter name) | M1 line 9 | **Procedure activation time** |
| `ClassSize` (macro) | M1 line 11 | **Preprocessing time** (before compile) |
| `object1` (global) | M1 line 12 | **Compile time** per the official answer (type and static allocation); *address fixed at link/load time* |
| `object1` (locals) | M1 lines 22, 24, 28 | **Procedure activation time** |
| `object2` (locals) | M1 lines 21, 27 | **Procedure activation time** |
| `Subroutine1`, `Subroutine2` | M1 lines 4, 5; M2 lines 3, 4 | **Linking time** |
| `c`, `s`, `i`, `n` (parameters) | M2 lines 3–6 | **Procedure activation time** |
| `f` as the `float` parameter of `Subroutine2` | M2 line 4 | **Procedure activation time** |
| `f` (function) | M2 line 5 | **Linking time** |
| `g` (function) | M2 line 6 | **Linking time** |
| `main` | M1 line 13 | **Linking time** (entry symbol) |
| `malloc` | `<malloc.h>` | **Linking time** (and **loading time** if libc is a shared library) |

**Notes that earn extra marks:**
- **Run time:** the *binding of a name to a value*, e.g. which function `abstractMethod[0]` and `Abstract` actually point to, is decided at run time (lines 17, 18, 25). This is **dynamic binding via function pointers**.
- **Loading time:** if the program is dynamically linked, `malloc`'s address (and the relocation of globals in a position-independent executable) is settled when the loader starts the program.
- Notice the **trap**: in Module2 the name `f` appears **twice with different roles** (a *function* on line 5, a *parameter* on line 4).

**Which identifiers will be seen in the assembly output?**

**✍️ Write this in the exam:**
> Only the **names the linker needs**: **global variables and global function names**: **`object1`, `main`, `f`, `g`, `Subroutine1`, `Subroutine2`, `malloc`.** All **types, members, parameters and local variables** disappear: members become offsets (0, 8, 16), locals/parameters become offsets from `%rbp`, `ClassSize` becomes the constant 24.

✅ **VERIFIED:** in `gcc -S` output: `.globl object1`, `.globl main`, `call malloc@PLT`, `call f@PLT`, `call g@PLT`, `Subroutine1@GOTPCREL(%rip)`, etc.; `nm` shows exactly those seven names. (If compiled with `-g`, the **debug sections** also contain all other names as *strings* for the debugger, but they are not part of the executable logic.)

---

## Q6 [10] What changes if `return 0;` becomes `return 1;` in both `f` and `g`?

**✍️ Write this in the exam:**
- **Now (with `return 0`):** `f(10)` and `g(10)` return 0, so the `if` blocks are skipped. The function-pointer member **`Abstract` is never initialised** (malloc'd memory), so line 31 calls through an **invalid address → segmentation fault.**
- **After the change (`return 1`):** `f(10)` and `g(10)` return 1, **both `if` blocks run**; line 25 sets `Abstract = Subroutine1`, so lines 31–32 make **valid calls** (`Subroutine1` just returns). **The program runs to the end and returns `sizeof(pointer)`** (8).

**Full explanation:**

**1. What `f` and `g` really compute.** Every path ends with a base-case `return` of the constant (0 or 1). By **induction**:
- Base (n ≤ 0): `f(n) = g(n) = c` (c = 0 or 1).
- Step: for n > 0, `f(n-1) > g(n-1)` compares `c > c` → **false**, so `f(n) = f(n-2) = c`. Likewise `g(n-1) < f(n-1)` is `c < c` → **false**, so `g(n) = g(n-2) = c`.
- **So `f(n) = g(n) = c` for every n.** The recursion pattern is **identical** in both versions; **only the final value differs.**

**2. Original (c = 0).** `if (f(10))` is false → skip. At line 31, `object1->Abstract(object1)`:
- `object1` here means the **local** `object1` from line 22 (it holds the same pointer as the global one).
- Its `Abstract` member was never assigned (line 25 not executed), and `malloc` does not clear memory → it is **NULL or garbage** → call to an invalid text address → **SIGSEGV**. ✅ **VERIFIED:** exit status 139 (segmentation fault).

**3. Modified (c = 1).**
- `if (f(10))` true → block entered. Line 24 declares a **new** `object1` for this block (shadowing, unused afterwards). **Line 25 `object2->Abstract = &Subroutine1`** writes into the **shared struct** (object2 points to the same malloc'd block).
- `if (g(10))` true → lines 27–28 declare new inner variables that **shadow** the outer ones and are **discarded at line 29**: **they do not affect the outer `object1`/`object2`** (scope ≠ the data they point to).
- Line 31/32 now call `Subroutine1` through the pointer, with fewer arguments than its prototype (technically undefined behaviour, but `Subroutine1` ignores everything and just returns). Line 33 returns `sizeof(object1)` = **pointer size**. ✅ **VERIFIED:** exit status 8.

**Key lesson to state:** the change flips a **control-flow** decision in `main` (which `if` blocks run), and that decides whether an **uninitialised function pointer** is ever called.

> Honest caveat: calling a `void` function through a pointer declared to return `Class`, and passing fewer arguments than declared, is **undefined behaviour**. It works on x86-64 gcc because the pointer-return register still holds the old value. The official answer ("executes to return the size of a pointer") treats it as working.

---

## Q7 [10] Maximum total stack-frame size starting from root call `f(n)`

**✍️ Write this in the exam:**
> **Maximum number of live frames = n + 1** (f and g together). Each frame holds: **1 `int` parameter + 3 addresses** (return address, saved stack/frame base pointer, saved state/control link). So
> **Total max stack size = (n + 1) × ( sizeof(int) + 3 × sizeof(void*) )**

**Derivation (so you can reproduce it):**
1. **Depth of recursion.** `f(n)` calls `f(n-1)`, `g(n-1)`, then `f(n-2)` or `g(n-2)`. Every call has an argument **smaller by at least 1**, and the calls with argument ≤ 0 return immediately. The deepest chain is n, n−1, …, 1, 0 → **n + 1 frames** alive at once (callers wait for callees; **sibling calls reuse the same stack space**, since each finishes and pops before the next starts).
2. **`f` and `g` have the same frame layout** (one `int n`, no locals), so any mix of f/g frames costs the same.
3. **Frame content (the lecture's activation-record model):** parameter `n` = `sizeof(int)`; **return address** (pushed by `call`), **saved frame/base pointer** (control link) and **saved state/stack pointer** = 3 × `sizeof(void*)`.
4. Multiply: **(n+1)(sizeof(int) + 3·sizeof(void*))**. For the actual call `f(10)`: 11 × (4 + 24) = **308 bytes** (on a 64-bit machine).

**Optional extra (only if the question asks about the real machine):** the actual gcc `-O0` code for `f` uses **48 bytes per frame** (8 return address + 8 saved `%rbp` + 8 saved `%rbx` + 24 reserved by `subq $24`), because it keeps `f(n-1)`'s result in a **temporary** (`%rbx`) while `g(n-1)` runs, and aligns the stack to 16 bytes. The exam answer expects the **model** formula above.

---

## Part A — quick recap (2024)
| Q | One-line answer |
|---|---|
| Q1 | Both modules compile independently (warnings only): the compiler needs only what's visible in that module; a pointer to an incomplete type needs no layout |
| Q2 | `Module1.c` alone: **no** (undefined `f`, `g`, `Subroutine1`, `Subroutine2`); both objects together: **yes** (linker matches names only, never types) |
| Q3a | `ClassSize` = 3·sizeof(void\*) (24 bytes); `main` returns sizeof(void\*) (8 bytes) |
| Q3b | A space is needed only between two adjacent tokens that would merge into one (identifier/constant followed directly by identifier/constant) |
| Q4 | M1: 13–28, 31–33. M2: 3–6. Only executed statements/initialisers/function bodies generate code, not declarations |
| Q5 | types/members → compile; macro → preprocessing; locals/params → procedure activation; global functions → linking; only global names appear in assembly |
| Q6 | `return 0` → `if` blocks skipped → uninitialised function pointer called → **segfault**; `return 1` → blocks run → no crash, returns pointer size |
| Q7 | (n+1)·(sizeof(int) + 3·sizeof(void\*)) |

---

# PART B — 2025 MIDSEM

## Q1 [15 = 3×5] "Many declarations, one unique definition"

**The principle:** a thing may be **declared in many modules**, but it is **defined once**. The declarations may *look* different from module to module, **but only as far as the compiler/linker can't tell the difference from the definition's real machine-level meaning.** The **definition fixes the imperative** (the actual code and data layout).

**The core test to apply to each item:** *"Is this thing baked into the machine code of every module that uses it (as a number: a size, an offset, a symbol name)? If yes → must be consistent. If it's only a compile-time convenience for the module → can differ."*

**✍️ Write this in the exam (exactly one line of reason each):**

| SrNo. | Independent? | Reason |
|---|---|---|
| (i) Size of storage of a data structure | **No** | Every module allocates/passes the object using the size from *its own* declaration, so it must equal the definition's size or memory gets corrupted. |
| (ii) Layout of its members | **No** | Member names are compiled into fixed offsets in each module's machine code, so the order/offsets must match the definition. |
| (iii) Names and number of member functions | **Yes** | Non-virtual methods take no space in the object and are separate global functions linked by (mangled) name, so a module need declare only the ones it calls. |
| (iv) Signatures (prototypes) of member functions | **No** | The signature determines the mangled link name and the parameter layout in the activation record, so a mismatch gives an undefined reference (or a crash in C). |
| (v) Sequence of signatures and bodies of methods | **Yes** | The order of declarations is irrelevant to the generated code, and the bodies live only in the single definition ("many declarations, one definition"). |

### Understanding each (read this; don't write it all)
- **(i) and (ii) are the "partially hidden class" lesson from the lecture.** The user module that left out the private members computed a *smaller size* and *different layout*; the compiler and linker both accepted it and the program crashed at run time. **Size and layout are data-structure facts that every module hard-codes.**
- **(iii)**: ✅ **VERIFIED.** A module that declared only *some* methods of class `C`, in a *different order*, linked and ran correctly. Why: `C::m1()` and `C::m2()` are just global functions with **mangled names** (like `_ZN1C2m1Ev`); the linker matches those names and never looks at the class declaration.
- **(iv)**: ✅ **VERIFIED.** Declaring `int m2(int)` where the definition has `int m2()` gave **`undefined reference to C::m2(int)`**, because the parameter types are part of the mangled name. (In plain C, there is no mangling, so a wrong prototype would link silently and crash. That was the `Subroutine2` `int`/`float` issue in 2024.)
- **(v)**: bodies are not repeated in declarations at all; the **definition** is where the imperative lives.
- **Caveat (good for a longer answer, or if the examiner's key differs):** if the methods are **`virtual`**, the **number and order** of declarations decide the **vtable slot index** and add a hidden **vptr** to the object (changing its size). Then (iii) and (v) must **also** be consistent. ✅ **VERIFIED:** swapping the order of two `virtual` declarations in one module made `p->a()` call `b()` (exit status 2 instead of 1) with **no compiler or linker error.**

---

## Q2 [30 = 3×10] Which of the 10 additions are Necessary / Sufficient / Harmful?

**The setup:**
- Module1 **defines** `void function(Class1 c)`.
- Module2 **declares** `extern void function(Class2)` and **calls** `function(c)` with `c` of type `Class2`.

### The one idea that solves the whole question
C++ **mangles** function names with their parameter types. So these are **two different symbols**:
- Module1 defines `_Z8function6Class1` = `function(Class1)`
- Module2 needs `_Z8function6Class2` = `function(Class2)` ← **nobody defines this**

✅ **VERIFIED** with `nm`: `m1.o` has `T _Z8function6Class1`; `m2.o` has `U _Z8function6Class2`. Without additions the linker says: **`undefined reference to function(Class2)`.**

**So linking succeeds if and only if `function(Class2)` is defined *exactly once* in the program.** Everything else (derivation, extra prototypes) is irrelevant to the linker, because the linker sees only names.

### The answer table
**✍️ Write this in the exam** (a tick = ✓; leave a *very white blank* for "no"):

| No. | Necessary | Sufficient | Harmful |
|---|:-:|:-:|:-:|
| 1 | | ✓ | |
| 2 | | ✓ | |
| 3 | ✓ | ✓ | |
| 4 | | | ✓ |
| 5 | | | ✓ |
| 6 | | | |
| 7 | | | |
| 8 | | | |
| 9 | | | |
| 10 | | | |

### Reason for each (verified by actually linking with g++)
| No. | Addition | Result | Why |
|---|---|---|---|
| **1** | Body `function(Class2)` in Module1 | ✅ Links | Supplies the missing definition. **Sufficient**, but not necessary (Module2 could hold it instead). |
| **2** | Body `function(Class2)` in Module2 | ✅ Links | Same, other module. **Sufficient**, not necessary. |
| **3** | Body in **any one (and only one)** of the two | ✅ Links | This is exactly the condition for linkability: **exactly one definition must exist.** It is **necessary** (no successful linking without it, whatever else you add) **and sufficient.** |
| **4** | Body in **each** of Module1 and Module2 | ❌ `multiple definition of function(Class2)` | Two definitions of the same global symbol. **Harmful** (the necessary body exists but is duplicated). |
| **5** | **Identical** body in each module | ❌ same error | "Identical except whitespace" doesn't help: for a normal (non-`inline`) function the linker still sees **two definitions of one symbol**. **Harmful.** (Identical repeats are only allowed for `inline`/templates/in-class definitions: the ODR exception.) |
| **6** | `Class2` derived from `Class1` | ❌ still undefined | Derivation is a **compile-time** relationship; it doesn't create a `function(Class2)` symbol. Not necessary, not sufficient, not harmful. |
| **7** | `extern void function(Class1);` at top of Module1 | ❌ still undefined | A **redundant redeclaration** of something Module1 already defines. Harmless; doesn't define `function(Class2)`. |
| **8** | Same prototype at top of Module2 | ❌ still undefined | Adds an **overload** `function(Class1)`; the call `function(c)` with a `Class2` still picks the **exact match** `function(Class2)` (even if Class2 derives from Class1: exact beats derived-to-base conversion), so it still needs the missing definition. Harmless. |
| **9** | Prototype in either module, not both | ❌ still undefined | Same as 7/8. |
| **10** | Prototype in both modules | ❌ still undefined | Same. **"Many declarations" are always allowed**; only definitions are limited to one. |

✅ **VERIFIED results (g++):** base → undefined reference; add 1 → links; add 2 → links; add 4 (and 5, identical) → multiple definition; add 6, 7, 8, 9, 10 each alone → undefined reference; add **6 + 8 together** → *still* undefined (exact match wins).

### Notes about marking and assumptions
- **Consistency rule in the paper:** something can't be *necessary* and *harmful*. Our table obeys it (4 and 5 are harmful; they are not necessary because the necessary thing is "exactly one" = addition 3).
- **Assumption:** wherever a body of `function(Class2 c)` is added, that module can see the full definition of `Class2` (a function definition taking `Class2` **by value** needs the complete type). The question says the unseen code is "valid", so assume it is.
- **Possible difference from the examiner's key (be aware):** some keys may tick **only "Sufficient" for 3**. I tick **Necessary + Sufficient** because "exactly one definition of `function(Class2)` somewhere" is required in *every* successful link, and 3 states precisely that. If you are unsure in the exam, the combined tick is the more defensible one; just don't tick "Necessary" for 1 or 2 individually.
- **Common mistake to avoid:** thinking "Class2 extends Class1, so the existing `function(Class1)` can be used." That would work only if Module2's *declaration* were `function(Class1)` **instead of** `function(Class2)`; here the `function(Class2)` declaration stays, and it wins.

---

## Q3 [15 = 2+5+8] Storage structure: base class or member? Which is better?

*(This asks about **your own** assignment, so first line must match what you actually did. Both versions are below; the "which is better" part is the same either way.)*

**The design in question:** the assignment had a **storage structure** (the aggregate that physically holds the list elements: e.g. array-based or linked-list-based) and **top-level access classes** (`Queue`, `Stack`, `DeQue`, `PriorityQ`). Two ways to connect them:
1. **Inheritance:** `class Stack extends Storage` (storage is a **base/super class**).
2. **Contain-and-delegate (composition):** `class Stack { private Storage store; push(x){ store.addLast(x); } ... }` (storage is a **member**, and the methods **forward** to it).

### (2 marks) What you did — pick one and state it plainly
- **If you used composition:** *"I made the storage a **member** of each access class (contain-and-delegate), and each access class's methods delegate to it."*
- **If you used inheritance:** *"I made the storage a **base class** of `Queue`, `Stack`, `DeQue`, `PriorityQ`."*

### (5 marks) Why you did it
**If composition (the better choice), say:**
- A `Stack`/`Queue` **is not a** storage structure; it **uses** one. Inheritance would model a false is-a relationship.
- Inheriting would **expose the storage's whole public interface** (e.g. insert/remove anywhere) through `Stack`, **breaking the abstraction** (a stack must only allow push/pop at one end).
- The storage can be **replaced** (array → linked list → circular buffer) **without touching** the access classes' code.
- Java allows **only one superclass**, so inheritance would block using the base for something else.

**If inheritance, say honestly:** *"It gave direct code reuse, no forwarding boilerplate, and `protected` access to the storage internals; it was quick to implement."* (Then, in the next part, concede its limits.)

### (8 marks) Which is better for the two-way flexibility? Justify.

> **Answer: contain-and-delegate (composition) is better.**

**What "two-way flexibility" means:** (1) you can **change the storage implementation** without changing the access classes, and (2) you can **change or add access classes** without changing the storage: the two **vary independently**.

| | **Inheritance (storage as base class)** | **Contain-and-delegate (storage as member)** |
|---|---|---|
| **Coupling** | Access class is **permanently tied** to one storage class, **fixed at compile time** | Access class talks only to a **storage interface (contract)**; any implementation can be plugged in (even at run time, e.g. via the constructor) |
| **Number of classes** for M storage variants × N access types | **M × N** (`ArrayStack`, `LinkedStack`, `ArrayQueue`, `LinkedQueue`, ...): class explosion | **M + N** (M storage classes + N access classes, combined freely) |
| **Change storage** | Edit the base class (affects all subclasses) or write new subclasses | **Replace one member**; access code untouched |
| **Encapsulation** | **Leaks** storage methods into the public interface of `Stack`/`Queue` | Interface of `Stack` is exactly push/pop; storage fully **hidden** (Parnas' information hiding) |
| **Is-a correctness** | Wrong (a Stack is not a storage) | Right (a Stack *has* a storage) |
| **Cost** | Less code | **Forwarding (delegation) boilerplate**, one extra method-call indirection |
| **Risk (from the lecture)** | The **"Banana Monkey Jungle" problem**: you wanted a banana, got a gorilla holding the banana and the whole jungle, i.e. inheriting drags in the entire base class and its dependencies | Delegation boilerplate and keeping the contract stable (the lecture also discusses *"the problem with the contain-and-delegate solution"*) |

**Justification paragraph you can write:**
> With inheritance, the choice of storage is hard-wired into the class hierarchy, so the two hierarchies (storage variations and access-class variations) are entangled: every new storage type needs a new subclass of every access class, and a change in the storage base class ripples into all access classes. With contain-and-delegate, the access classes depend only on an abstract storage *contract*. The storage implementation is a hidden design decision (information hiding, Parnas), so either side can change independently. This gives the two-way independence sought, at the price of some forwarding boilerplate and one indirection. Hence composition is better. (This follows the lecture's themes: encapsulation as the concrete form of independence, and the tension between *polymorphism, contracts and flexibility*.)

**Tiny code sketch (optional, helps if you have time):**
```java
interface Storage { void addFirst(int x); void addLast(int x);
                    int removeFirst(); int removeLast(); boolean isEmpty(); }
class ArrayStorage  implements Storage { /* array-based */ }
class LinkedStorage implements Storage { /* linked-list-based */ }

class Stack {                       // top-level access class
    private final Storage store;    // contain ...
    Stack(Storage s) { store = s; } // ... any implementation can be injected
    void push(int x) { store.addLast(x); }   // ... and delegate
    int  pop()       { return store.removeLast(); }
}
// new Stack(new ArrayStorage())  or  new Stack(new LinkedStorage())
```

---

# PART C — What both papers teach (use for tomorrow)

## The five sentences that answer most of these questions
1. **The compiler sees one module at a time; the linker sees only global names.** (Types, members, locals never reach the linker.)
2. **A pointer to a type needs only the pointer size; the type's size/layout is needed only to dereference or pass/allocate the value.**
3. **Declarations can be many and different (up to what the machine code hard-codes); the definition is unique and fixes the imperative.** Size, layout and signatures are hard-coded; extra/ordered non-virtual method declarations are not.
4. **C++ mangles names with parameter types; C does not.** So C++ catches signature mismatches at link time; C silently links and crashes.
5. **Names disappear into offsets:** members → struct offsets; locals/parameters → frame offsets; only global variables/functions keep names.

## Likely question types (from these two papers)
| Type | How to attack it |
|---|---|
| "Will it compile / link? Why?" | Say what each module needs (compile: visible declarations; link: matching global names, each defined once). Never argue syntax/standards. |
| "Classify by binding time" | preprocessing (macros) → compile (types, members → offsets) → linking (global names) → loading (final addresses, dynamic libs) → activation (parameters, locals) → run (values/pointers). |
| "Which lines make code?" | Only executed statements, initialisers and function bodies. Never declarations, typedefs, prototypes, `#define`, static data definitions. |
| "Stack frame size" | frames alive at once (depth) × (parameters + locals + return address + saved pointers/links). |
| "Necessary / sufficient / harmful" | Work out the *single linkability condition* first (here: exactly one definition of the mangled name), then test each addition against it. |
| "Which design is better?" | Name the two options, say what flexibility is sought, tabulate coupling/class count/encapsulation, conclude composition, mention the cost. |

## Tactics
- **Use the exact format asked** (tables, "exactly 1 line", ticks vs very white blanks). The paper penalises messy answers.
- **Give units** for sizes (bytes or multiples of `sizeof(void*)`).
- **Don't overwrite or cross out** in tick-box tables.
- **Trace code with the scope table** before answering anything about `object1`/`object2`-style shadowing.

---
*End of solutions.*
