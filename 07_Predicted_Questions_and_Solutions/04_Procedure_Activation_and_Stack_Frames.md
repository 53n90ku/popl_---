# 📝 Topic 04: Procedure Activation, Stack Frames & Runtime Environments
### Predicted Exam Questions & Complete Professor-Approved Solutions

---

## 📌 Question 1: Recursive Stack Frame Layout (Direct 2024 Midsem Q3 Variant)

### Problem Statement
Consider the following recursive C function computing the Greatest Common Divisor (GCD) using Euclid’s algorithm:

```c
int gcd(int a, int b) {
    if (a == b) return a;
    if (a > b)  return gcd(a - b, b);
    return gcd(a, b - a);
}
```

Suppose `main()` calls `gcd(6, 4)`.
1. List the chronological sequence of calls until the base case is reached.
2. Draw the exact diagram of the runtime call stack at the **moment of maximum stack depth** (when the base case is executing).
3. In your stack diagram, explicitly show for each frame:
   - Incoming arguments
   - Return address
   - Saved caller frame pointer (Saved `%rbp`)
   - Local variables / temporaries
   - Location of the base pointer (`%rbp`) and stack pointer (`%rsp`)
4. How is the return value propagated back to `main()`?

---

### 💡 Model Solution

#### 1. Chronological Call Sequence
1. `main()` calls `gcd(6, 4)`
2. `gcd(6, 4)`: `a > b` (6 > 4), calls `gcd(6 - 4, 4)` -> `gcd(2, 4)`
3. `gcd(2, 4)`: `b > a` (4 > 2), calls `gcd(2, 4 - 2)` -> `gcd(2, 2)`
4. `gcd(2, 2)`: `a == b` (2 == 2) -> **Base Case Reached!** Returns `2`.

Maximum stack depth is reached when `gcd(2, 2)` is actively executing.

#### 2 & 3. Runtime Stack Diagram at Peak Depth

```text
High Memory Addresses (Top of Diagram)
========================================================================
[ Activation Record: main() ]
|   ... local variables of main ...                                    |
|   Argument 2 for call: 4 (or passed via %esi)                        |
|   Argument 1 for call: 6 (or passed via %edi)                        |
+----------------------------------------------------------------------+
[ Activation Record: gcd(6, 4) ]
|   Return Address (points to instruction inside main after call)      |
|   Saved %rbp of main()                                               | <-- %rbp for gcd(6,4) frame
|   Local parameter storage: a = 6, b = 4                              |
+----------------------------------------------------------------------+
[ Activation Record: gcd(2, 4) ]
|   Return Address (points to instruction inside gcd(6, 4))            |
|   Saved %rbp of gcd(6, 4)                                            | <-- %rbp for gcd(2,4) frame
|   Local parameter storage: a = 2, b = 4                              |
+----------------------------------------------------------------------+
[ Activation Record: gcd(2, 2) ]  <-- CURRENT ACTIVATION (PEAK DEPTH)
|   Return Address (points to instruction inside gcd(2, 4))            |
|   Saved %rbp of gcd(2, 4)                                            | <-- %rbp (CURRENT FRAME POINTER)
|   Local parameter storage: a = 2, b = 2                              |
|   Temporaries / Scratch Space                                        | <-- %rsp (CURRENT STACK POINTER)
========================================================================
Low Memory Addresses (Stack grows DOWNWARD)
```

#### 4. Propagation of Return Value
* In standard x86-64 System V ABI:
  1. In `gcd(2, 2)`, the base case executes `return a;`. The CPU moves `2` into register `%eax` (`movl %edi, %eax`).
  2. The epilogue restores caller frame: `leave; ret`.
  3. Control returns to `gcd(2, 4)`. Since `gcd(2, 4)` returns the result directly (`return gcd(...)`), `%eax` remains `2`.
  4. Control returns to `gcd(6, 4)`. `%eax` remains `2`.
  5. Finally, `main()` reads `%eax` as the return value `2`.

---

## 📌 Question 2: Anatomy of an Activation Record & Registers

### Problem Statement
1. What is an **Activation Record (Stack Frame)**? Differentiate between the static concept of a function definition and the dynamic concept of an activation.
2. List the **6 standard fields** present inside a general-purpose procedure activation record and describe the purpose of each.
3. Contrast **Caller-Saved Registers** vs **Callee-Saved Registers** in modern calling conventions.

---

### 💡 Model Solution

#### 1. Function Definition vs Activation
* **Function Definition (Static):** The invariant program text/machine code residing in the read-only code segment (`.text`). Exists once in memory.
* **Procedure Activation (Dynamic):** The runtime execution instance of a function call. In recursive or concurrent code, a single static function definition can have multiple concurrent activations, each requiring its own independent memory storage on the stack.

#### 2. The 6 Standard Fields of an Activation Record
1. **Actual Parameters (Arguments):** Values or addresses passed by the caller to the callee (if not passed in registers).
2. **Return Address:** The program counter (IP/PC) address of the instruction in the caller immediately following the call instruction.
3. **Saved Frame Pointer (Dynamic Link / Control Link):** The caller's base pointer (`%rbp`), allowing the stack frame to be unlinked and restored upon return.
4. **Local Variables:** Storage allocated for variables declared locally within the procedure.
5. **Temporaries:** Space allocated by the compiler to hold intermediate results during complex arithmetic expression evaluation.
6. **Saved Registers:** Preservation area for registers whose values must survive the function invocation.

#### 3. Caller-Saved vs Callee-Saved Registers
* **Caller-Saved Registers (`%rax`, `%rcx`, `%rdx`, `%rsi`, `%rdi`, `%r8-%r11`):**
  - "Scratch registers". The caller must assume the callee will overwrite these registers.
  - If the caller needs their values after the function call, the **caller must push them onto the stack** before issuing `call`.
* **Callee-Saved Registers (`%rbx`, `%rsp`, `%rbp`, `%r12-%r15`):**
  - Preserved registers. The callee promises that their values will remain unchanged upon function return.
  - If the callee needs to use them, the **callee must push them in the prologue** and pop/restore them in the epilogue.

---

## 📌 Question 3: x86-64 Assembly Prologue and Epilogue

### Problem Statement
Given the following C function:
```c
int compute(int x, int y) {
    int temp = x * 2 + y;
    return temp;
}
```
1. Write the standard x86-64 assembly instructions for the **Function Prologue** and **Function Epilogue**.
2. Explain why compilers use a dedicated Base Pointer (`%rbp`) to access variables rather than addressing everything directly via the Stack Pointer (`%rsp`).

---

### 💡 Model Solution

#### 1. Assembly Prologue and Epilogue
```assembly
compute:
    # ========== FUNCTION PROLOGUE ==========
    pushq   %rbp            # 1. Save caller's base pointer on stack
    movq    %rsp, %rbp      # 2. Establish current frame pointer (%rbp = %rsp)
    subq    $16, %rsp       # 3. Reserve 16 bytes on stack for locals (aligned to 16 bytes)

    # Store incoming register arguments into stack frame
    movl    %edi, -4(%rbp)  # Save parameter x (from %edi) at offset -4
    movl    %esi, -8(%rbp)  # Save parameter y (from %esi) at offset -8

    # ========== FUNCTION BODY ==========
    movl    -4(%rbp), %eax  # Load x
    addl    %eax, %eax      # x * 2
    addl    -8(%rbp), %eax  # (x * 2) + y
    movl    %eax, -12(%rbp) # temp = result
    movl    -12(%rbp), %eax # Return value placed in %eax

    # ========== FUNCTION EPILOGUE ==========
    leave                   # Macro instruction equivalent to:
                            #   movq %rbp, %rsp  (collapses local stack)
                            #   popq %rbp        (restores caller's %rbp)
    ret                     # Pops return address from stack and jumps to caller
```

#### 2. Why `%rbp` Provides Stable Offsets vs Fluctuating `%rsp`
* During expression evaluation and argument preparation for nested function calls, values are dynamically pushed and popped from the stack.
* This causes the stack pointer (`%rsp`) to **fluctuate continuously**. If the compiler addressed a local variable using `%rsp`, the offset to that variable would change after every `push` or `pop` instruction!
* By copying `%rsp` into `%rbp` once during the prologue and freezing it, `%rbp` provides a **stationary, immovable reference anchor**. Local variables always sit at fixed negative offsets (e.g., `-4(%rbp)`, `-8(%rbp)`), and incoming stack parameters sit at fixed positive offsets (e.g., `+16(%rbp)`).

---

## 📌 Question 4: Static Links (Access Links) vs Dynamic Links (Control Links)

### Problem Statement
In languages that support nested function definitions (such as Pascal, Ada, or Python), functions can access non-local variables from enclosing lexical scopes.
1. Define **Dynamic Link (Control Link)** and **Static Link (Access Link)**.
2. Consider the following nested pseudo-code:
```pascal
program Main;
  var x: integer;
  
  procedure P;
    procedure Q;
    begin
      x := x + 1; // Accesses x declared in Main
    end;
  begin
    Q;
  end;
  
  procedure R;
  begin
    P;
  end;
begin
  R;
end.
```
Trace the call sequence: `Main` -> `R` -> `P` -> `Q`.
Draw the stack showing both the **Dynamic Chain** and the **Static Chain** when `Q` is executing. Explain how `Q` accesses `x`.

---

### 💡 Model Solution

#### 1. Definitions
* **Dynamic Link (Control Link):** A pointer stored in the current activation record pointing to the activation record of the **caller** (the function that called this one at runtime). It is used to deallocate the frame upon function return. Follows dynamic call history.
* **Static Link (Access Link):** A pointer stored in the current activation record pointing to the activation record of the **immediate lexical/static parent** (the block that lexically encloses this function in the program source code). It is used to resolve non-local variable accesses.

#### 2. Chain Diagram when `Q` is Executing

```text
Dynamic Call Order: Main -> R -> P -> Q
Static Enclosing Nesting: Main encloses P and R; P encloses Q.

[ Frame: Main ] (defines x) <--------------------+  <-------------------+
       ^                                         |                      |
       | Dynamic Link                            |                      |
[ Frame: R ]                                     |                      |
       ^                                         |                      |
       | Dynamic Link                            | Static Link          | Static Link
[ Frame: P ] ------------------------------------+                      |
       ^                                                                |
       | Dynamic Link                                                   |
[ Frame: Q ] (currently executing) -------------------------------------+
```

* **The Dynamic Chain:** `Q -> P -> R -> Main` (shows the order of runtime returns).
* **The Static Chain for `Q`:** `Q -> P -> Main`.
* **How `Q` accesses `x`:**
  - `Q`'s compiler knows from the static syntax tree that `x` is defined in `Main`, which is **2 nesting levels up** from `Q`.
  - The compiled machine code in `Q` traverses **two static links**:
    `CurrentFrame -> static_link (P) -> static_link (Main) + offset(x)`.
  - Notice that `Q` completely bypasses `R` on the static chain, even though `R` is on the dynamic call chain!
