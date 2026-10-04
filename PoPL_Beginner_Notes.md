# PoPL (CS F301) — Complete Beginner-Friendly Notes
*Principles of Programming Languages · Prof. Ramprasad S. Joshi · Week 9 (Functional-OO) material*

**How to use these notes**
1. Read **Part 0** first. It explains every basic word you need. Do NOT skip it.
2. Then read Parts 1 to 8 in order. Each part has: **the idea in one line → explanation from scratch → example → what to write in the exam.**
3. Last part = **cheat sheet + practice questions with answers**.
4. Things marked ⭐ are the most likely exam questions.
5. If something says *(my example)*, I made it up to help you understand. The lecture didn't have it.

> **Scope warning:** these notes cover everything in the lecture PDF. If your midsem syllabus has other topics (e.g. lambda calculus, Prolog details), check your class notes for those.

---

# PART 0 — Basics you must know first (from zero)

## 0.1 Program, source code, machine code
- A **program** = a list of instructions telling a computer what to do.
- **Source code** = the program as a human writes it (e.g. a `.c` or `.java` file).
- **Machine code** = the 0s and 1s the CPU actually understands.
- **Assembly** = machine code written with readable names (`mov`, `add`, `jmp`). One assembly line ≈ one machine instruction.
- A **high-level language** (C, Java, Python) is closer to human thinking. A **low-level language** (assembly, machine code) is closer to the hardware.

## 0.2 The journey of a program (compile → link → load → run)
Think of writing a **group project report**:

| Step | What happens | Analogy |
|---|---|---|
| **Compilation** | A **compiler** translates your source code into machine/assembly code (an "object file") | Each team member translates their chapter into the final language |
| **Linking** | A **linker** joins all object files + library code into one runnable file | Binding all chapters + borrowed references into one book |
| **Loading** | The OS **loader** copies the program into memory and gets it ready | Placing the book on the stage |
| **Execution / Run** | The CPU executes it | Reading it aloud |

An **interpreter** (like Python) reads and executes code line by line without producing a separate file first.

## 0.3 CPU, memory, registers
- **CPU** does the work. **Memory (RAM)** stores programs and data.
- **Registers** = tiny super-fast storage slots inside the CPU. Names you'll see: `%rax/%eax` (the **accumulator**, often holds a return value), `%rbp` (**base/frame pointer**), `%rsp` (**stack pointer**), `%edi`, `%esi` (hold the first two arguments).
- The **von Neumann machine** = the standard model of a computer: CPU + one memory that stores both instructions and data. The CPU repeats: **fetch an instruction → decode → execute → next**. (This is called the **instruction cycle**.)

## 0.4 Variables, types, functions
- **Variable** = a named box in memory holding a value. `int i;` creates a box named `i` that holds integers.
- **Type** = what kind of values a variable can hold and what you can do with them (`int`, `float`, `char`...).
- **Function / procedure / subroutine / method** = a named, reusable block of code. In the lecture: **"subroutine"** is the general word for all of them.
  - **Parameter (formal parameter)** = the variable name in the function definition: `int gcd(int a, int b)` → `a`, `b`.
  - **Argument (actual parameter)** = the value you pass when calling: `gcd(12, 18)` → `12`, `18`.
  - **Return value** = what the function gives back.
  - **Signature / prototype** = name + parameter types + return type.
- **Recursion** = a function that calls itself. Example (*my example*):
  ```c
  int fact(int n) { if (n <= 1) return 1; return n * fact(n-1); }
  ```
  `fact(3)` calls `fact(2)` which calls `fact(1)` which returns 1; then it unwinds: 1 → 2 → 6.

## 0.5 The stack (very important for Part 6)
- A **stack** is like a **pile of plates**: you add on top (**push**), remove from the top (**pop**). Last in, first out.
- The program's **call stack** keeps track of function calls. Every time a function is called, a new block of memory (a **stack frame** or **activation record**) is pushed holding that call's parameters and local variables. When the function ends, its frame is popped.
- That is why recursion works: each call gets its **own** frame, so `fact(3)`, `fact(2)`, `fact(1)` each have their own `n`.

## 0.6 Class, object, method
- **Class** = a blueprint (e.g. `Car`: has `color`, can `drive()`).
- **Object / instance** = one actual thing built from the blueprint (`myCar`).
- **Method** = a function that belongs to a class.
- **Encapsulation** = bundling data and the methods that work on it into one unit, and hiding internals (`private`) from outside.
- **Inheritance** = a **subclass** automatically gets everything its **superclass** has (a `SportsCar` is-a `Car`).
- **Polymorphism** = the same method name behaving differently depending on the object's actual class.

## 0.7 Module, file, header
- **Module** = a separate piece of a program (in C, usually one `.c` file) that can be developed and compiled on its own. Each such file is a **compilation unit**.
- **Header file (`.h`)** = a file with declarations (names/types) that several `.c` files `#include` so they agree on what things look like.
- **Global** = visible everywhere. **Local** = visible only inside a function/block. **Static local variable** = a local variable that keeps ONE shared copy that lasts for the whole program run (not recreated per call).

## 0.8 Static vs dynamic
- **Static** = fixed before the program runs / known at compile time (the code text, types, sizes).
- **Dynamic** = happens while running (actual values, which branch is taken).
- Lecture one-liner: **Program = static. Computation (the run) = dynamic.**

## 0.9 Syntax vs semantics
- **Syntax** = the grammar/spelling rules (is this a legal sentence?). `int x = ;` is a **syntax error**.
- **Semantics** = the meaning (what does it do?). `x = 5/0;` is syntactically fine but its meaning is a problem at run time.
- English analogy: "Colourless green ideas sleep furiously" is grammatical (syntax OK) but meaningless (semantics fails).

## 0.10 Scope
- **Scope of a name** = the part of the source code where that name can be used and refers to one specific thing.
- Example: a variable declared inside a function is only usable inside that function.

## 0.11 Paradigm
- **Programming paradigm** = a style / way of thinking about programs. The four in this course:
  **imperative** (do steps), **object-oriented** (interacting objects), **functional** (compose functions), **logic** (facts and rules).

## 0.12 Abstraction
- **Abstraction** = focusing on **what** something does and ignoring **how** it does it.
- Analogy: you press a car pedal to speed up (the *what*); you don't need to know about fuel injection (the *how*).

---

# PART 1 — Preliminaries

## 1.1 All computation comes from a few primitives
**Idea:** every program in the world is built from a small, fixed set of basic operations combined in endless ways.

- A CPU only knows a **finite** list of tiny instructions (add, compare, jump, load, store...).
- A **few elementary operations** can be combined into **infinitely many** programs.
- The model machine is the **von Neumann machine** (0.3).
- **Analogy:** 26 letters → infinitely many books. Or: a few LEGO brick types → any model.

## 1.2 Higher-level languages are abstractions
**Idea:** a language's "height" tells you how close it is to how humans think.

- **Height of a language = how close it is to the human level = its expressive power.**
- A good language should:
  1. let us **express our intention** clearly,
  2. come with a **toolchain** (compiler, linker, etc.) that turns the intention into a real computation,
  3. put the **least burden on the programmer for the "how"**,
  4. have **fidelity**: what actually gets computed should match what we intended.
- **Highest level** = like natural language. **Lowest level** = machine code.
- *(My example)*: "sort this list" (high level) vs dozens of `cmp / jmp / mov` instructions (low level).

## 1.3 Formal languages and natural languages
**Idea:** a programming language is a formal language *plus meaning*.

- **Formal language** = a mathematically defined **set of strings** that follow some structure.
- Other formal-language-like things: logical expressions (propositions, predicates) and number systems (N, Q, Z, groups, rings, fields...).
- If you collect all the **valid programs** of a language, that set is a formal language.
- **BUT**, once you add **semantics** (meaning), a programming language is **much more than** a formal language.
  > **Memorise:** formal language = just strings. Programming language = strings + meaning.
- **"Programming languages are as human as English":**
  - Most languages are **discrete and linear (1-D)**: a line of symbols.
  - The real world they describe is **neither discrete nor finite-dimensional**.
  - So we use languages (formal, natural, programming) to **squeeze unimaginably many possibilities into representations that are reproducible, verifiable and uniquely identifiable.**

## 1.4 Abstraction made concrete
**Idea:** you understand abstraction best from examples, not definitions.

The lecture says: we can't really define abstraction in abstract terms; we explain it by concrete examples.
- **Content is concrete; form is abstract.** We rarely see a perfect circle, but we call many things "round".
- The **name/signature of a library function**, or a **class in a standard library**, is an abstraction of the computation it promises.
- **Real numbers are abstract; floating-point numbers (what computers use) are concrete.**
- **Interview/placement questions about OOP are abstract; an OO program actually designed and built is concrete.**

## 1.5 The abstraction ladder: the GCD example
**GCD** = Greatest Common Divisor (e.g. gcd(12, 18) = 6).

The same idea at six levels, **from most abstract to most concrete**:
1. **Mathematical definition**: in a ring with unique factorisation, gcd(a,b) = gcd(b,a) = gcd(|a−b|, b) = gcd(a, |a−b|).
2. A **table** with columns: first number, second number, their GCD.
3. **Euclid's algorithm using subtraction.**
4. **Euclid's algorithm using division–remainder.**
5. One or more **C programs** (for integers, polynomials, quadratic integers...).
6. A **binary executable** of one of those programs.

**What to understand:** moving down the ladder, you add "how" detail and lose generality. The top says *what* GCD is; the bottom is a specific machine doing it.

## 1.6 Abstraction according to the textbook (Scott)
Programming languages (except assembly) are "higher level" in **two senses**:
1. **They abstract the programmable machine**: the real machine = OS + microprocessor (or a virtual machine like the JVM); C/C++/Java/Python hide it.
2. **They let us name pieces of code** by purpose (the **what**) and hide the **how**.

Therefore:
- **Subroutines (C functions, Java methods) = CONTROL abstractions** (they abstract *a process*).
- **Classes = DATA abstractions** (they abstract *a kind of data and what you can do with it*).

> **Memorise: Control abstraction = subroutine. Data abstraction = class.**

## 1.7 Mechanical computation
**Idea:** computation that can be done step by step with no thinking or judgement.

- The von Neumann machine does **mechanical computation**: something we could **copy exactly (even if extremely slowly)** using big physical things, where every step needs **no discretion or "horse sense"**.
- Examples in the lecture:
  - Demonstrating a sorting algorithm on a **row of books used as an array**.
  - A **treasure-hunt game on campus** that uses graph algorithms.
  - **Lamps over doors** guiding a trained **mouse through a maze** algorithm.
  - **Industrial robots** doing repetitive tasks, controlled by **PLCs (Programmable Logic Controllers)** programmed with **Ladder Diagrams**.
- **Quantum computation is NOT mechanical** in this classical sense.
- **Semi-autonomous robots** = mechanical computation **+ human discretion**.

## 1.8 What is NOT programmable? ⭐
Three categories:
1. **Anything not finitely expressible.** Example: most real numbers (irrational numbers that aren't roots of integer-coefficient polynomials) can't be written down or computed by any finite program → **most reals are not programmable**, and **equality of arbitrary reals can't be decided**.
2. **Situations where you can't list all possibilities in finite form**, so **autonomous control isn't programmable**. Example: controlling the **angle of attack during aeroplane take-off**.
3. **Captchas** (tests designed so that programs can't pass them).

## 1.9 The tea-making sequence
- **Input:** ingredients (tea, sugar, milk, water) go into the "hardware" (the kettle).
- **Processing:** they are boiled together.
- **Output:** tea is served.
- Then the lecture asks: *Is this "programmable" like baking in a microwave?* and *How come this is readily programmable?* **Hint:** you can model a **tea/coffee vending machine as a finite automaton** (finite states: idle → coin inserted → brewing → dispensing; finite inputs; fixed rules).
- Finally: *Can this handle the **uncertainty, reversibility and diversity** of the real biosphere?* → **No.** A finite model can't capture open-ended real-world situations.
- *(The answers to those questions are my reading; the lesson is: **finite, well-defined situations are programmable; open-ended ones aren't.**)*

---

# PART 2 — Programming Language Design

## 2.1 Languages are for humans, not machines
- Programming uses the same skills as any language: **reading, speaking, writing, listening**.
- **Programming languages are for humans, not machines.** (The machine only needs 0s and 1s.)
- Scott: **ease of programming** is a stronger motivation than **portability (machine independence)** in language design. (Even wanting hardware independence is about ease of use.)

**"Ease of use" includes ease of:** designing, developing, testing · modelling, simulation, formal verification · deployment · validation in real settings · maintenance, troubleshooting, repairs, updates, upgrades · documentation and training · delegation, sharing, arguing and explaining programs.

## 2.2 What is programming? The Three Essentials ⭐
Questions to ask: *What is programmable? In which ways? In which ways not?*

| Essential | Meaning | Example |
|---|---|---|
| **Elementary operations** | the basic actions: arithmetic, logic; or "move cursor, point, click" | `a + b`, `a < b` |
| **Sequencing** (*no timing*) | which operation comes after which | do A, then B |
| **Repetition** | doing something again: recursion, iteration, cycling | `while`, `for`, recursion |

Complication the lecture mentions: even **callbacks are real-time**, so is "no timing" fully true?

## 2.3 Statics and dynamics
- When you type commands in a shell, each command is static text but the **computation is dynamic** (the same command can give different results after you `cd` somewhere else).
- You can write a **shell script** to automate, but the **dynamism stays**.
- You pass **arguments** to the script to handle dynamism.
- The shell **environment** is still dynamic.
- Open question: *What is the environment of a C program?* (Think: current directory, environment variables, command-line arguments, files, stdin.)

> **One-liner: Program = static text. Computation = dynamic run.**

## 2.4 The Dijkstra debate ⭐ (a historical argument)
**Background:** In the 1960s–70s people argued about *how programming should be done and taught*.

**The people:**
- **Edsger Dijkstra** — famous for **"Go To Statement Considered Harmful" (1968)**; won the **Turing Award**; his Turing lecture was **"The Humble Programmer"**. He wanted programming to be **disciplined and mathematical**.
- **DuWorks & Smoliar (1972)** — wrote **"The Arrogant Programmer: Dijkstra and Wegner Considered Harmful"**, a reply defending practitioners.

**What Dijkstra said (Turing lecture):**
1. **I/O interrupts** happen at unpredictable, unrepeatable moments. This was a dramatic change from the old, fully predictable sequential machine, and created **grave logical problems**.
2. **Multilevel stores** (several memory levels) need heavy documentation and **still can't resolve the tricky complications**.
3. Above all, **more hardware power + much more reliability** made solutions possible that programmers hadn't dared dream of.

**Manageability (Dijkstra):**
- Programs with the **same mathematical content** can differ hugely in **intellectual manageability** (how easy they are for humans to understand and control).
- Rules to keep programs manageable are of **two kinds**:
  - **Kind 1:** rules **imposed by the choice of paradigm and compiler**, e.g. **banning `goto`** and **banning procedures with more than one output parameter**.
  - **Kind 2:** rules that need **discipline from the programmer**. **ALGOL-60 was designed to enforce this discipline** from the start.
- Finally, this led to his book **"A Discipline of Programming" (1976)**. **David Gries (1981)** finished the project with **"The Science of Programming"**: "A" Discipline → "The" Science.

## 2.5 Teaching and what CS even is (1972 context)
- In 1972, computer science as a "discipline" was **badly defined**: are computer scientists craftspeople, artists or philosophers? What if their products cause disasters?
- **Prof. Kandel (New Mexico Tech):** suggested **design projects** (like building the minicomputer UHC-1 from specifications down to circuits and machine language) in **senior-level courses**, to build interest in computer industries, improve **teamwork**, and expose students to **real-life constraints** (cost is "sometimes" a prime consideration). Reasoning: first teach designers *not to worry about efficiency*, then bring in efficiency and real-life constraints; back it up with a **practical, carefully designed case study**.
- **Prof. Howard Tompkins:** a CS major is prepared **not just to program** but to **improve programmers' tools; build better machines, software, languages, methods for intractable problems; manage computer-dependent activities; teach; or advance the theory of digital information processing**. People who only program have "changed direction, missed their aim, or are still learning".

## 2.6 The practitioners' reply (DuWorks & Smoliar)
- **Science arises from generalising observations. Observation must come before generalisation.** (They quote Suzanne Langer: general theories must be built from a special field known in full detail, or they become "vague generalities".)
- Their complaint: Dijkstra put the **"cart before the horse"** (theory before observation).
- **ALGOL-60 vs PL/I:** ALGOL-60's insistence on **mathematical purity** was **"formal overkill"** that killed good ideas. They thought **PL/I** was far better, with the "generative ideas" of third-generation computers.
- What their generation lacked: **"system elegance"** (independent of, but not excluding, mathematical elegance).
- Warning: **don't let the modelled object be dominated by the model itself.**

## 2.7 What does this mean for our course? (the course's own stand)
- The debate is **50 years old** but still alive: there's a **gap between theoretical CS and practical software engineering**.
- It strongly shapes how languages are **designed, developed and used**.
- Most programmers learn a language through **habits copied from practitioners**, but **courses teach mostly syntax** (like a third-language class), **not habits or usage**.

**Paradigms vs syntax:**
- The course is **not** against paradigm discipline, **it supports it**.
- **Programming paradigms = discipline and habits that help build software efficiently without hurting conceptual integrity.**
- We agree with Dijkstra on discipline, but following **Brooks (1982, *The Mythical Man-Month*)**, our discipline is **conceptual integrity at the human level, NOT mathematical purity**.

**Empirical, not analytical:**
- CS/engineering, paradigms, languages and the needed discipline are **empirical (like physics), not analytical (like pure maths).**
- **Newton analogy:** apples don't fall *because* Newton defined gravity; Newton defined gravity *so that* it explains apples falling. And the law doesn't *forbid* rockets leaving Earth, it predicts it exactly.
- So: **paradigms and syntax are enablers and scaffolding, not fetters (chains).**

## 2.8 How the paradigms arose
| Paradigm | How it arose |
|---|---|
| **Procedural** | Programmers grouped repeatedly useful code as **macros with short names (mnemonics)**; these grew **parameters and flexible input-output behaviour** |
| **Procedural → modular → structured** | Still **imperative**. Moving past mnemonics and **FORTRAN (formula translation)**, **structured languages** appeared and allowed **top-down design** |
| **Procedural → OO** | Many structured programs sharing common operations revealed the **underlying unity of types and operations** → **OOP** |
| **Functional** | Another view: operations as **transformations** (functions) |
| **Logic** | Another model: **relations (predicates) instead of functions** |

## 2.9 ⭐ Four models → four paradigms (very likely exam table)
| Underlying model | Paradigm |
|---|---|
| **(ALU) von Neumann machine, flow-charts, algorithms, digital logic, relay logic, ladder diagrams** | **Imperative** |
| **Simulation of evolving real-life systems of interacting entities** | **Object-oriented** |
| **Recursive function theory, lambda calculus** | **Functional** |
| **State transformations, theorem proving as computation, relational databases** | **Logic** |

**Plain meaning:**
- *Imperative* = "do this, then this" (a recipe).
- *OO* = "a world of objects that talk to each other" (a simulation).
- *Functional* = "compute by combining mathematical functions".
- *Logic* = "state facts and rules; the system works out answers".

---

# PART 3 — The Imperative Paradigm

**The three goals of this part (from the lecture):**
1. **Characterise structured programming.**
2. **Explain modular programming** and its variations.
3. **Expose the main ideology of C** compared with other procedural languages.

## 3.1 Program structure
- Programs are built from **global functions and objects** (objects can be **static** and optionally **global**).
- **Programs are static, computations are dynamic.** (Static things: data types, sizes, layouts, modules, global functions.)
- **Control flow is syntax-directed**: functions, blocks, loops, conditionals, switch-case; also `break`, `continue`, `goto`.

## 3.2 Modular programming ⭐
**Idea:** break a big program into separate modules, each one a distinct task.

From **Gouthier and Pont (1970)**:
- A well-defined split of the project ensures **modularity**: **each task = a separate, distinct module**.
- Each module's **inputs and outputs are well-defined**, so there's no confusion about how modules interface.
- At checkout, each module's **integrity is tested independently**; few scheduling problems.
- The system is **maintained in modular fashion**: errors can be **traced to specific modules**, which **limits where you have to search**.

### The compile/link demo, explained
The lecture showed a terminal session with two C files, `structure.c` and `main.c`, both defining a function `f`:
- `gcc structure.c main.c` → **error: "multiple definition of `f`"** (this is a **linker** error: `ld returned 1 exit status`). Both files define `f`, so the linker finds two of them.
- `gcc -c structure.c` / `gcc -c main.c` → **compile only** (`-c` = no linking) → works fine, because each file alone is OK.
- `gcc structure.c newmain.c` works (no clash).

**Meaning:** each `.c` file is compiled on its own; the clash only shows up when **linking**.

### Lessons ⭐ (memorise all 4)
1. **Each compilation unit is independent** in type names, variable names and function names; these names (and the structure of types) must be **given afresh to each unit**.
2. So it's possible to **create inconsistencies in data structures** across units.
3. We can exploit this independence to build data structures with **information hiding for security**.
4. But it can cause **logic bugs**; to avoid them, **header (`*.h`) files** are used as **common includes** in the various `.c` files.

## 3.3 Control flow and `goto`
`goto` = jump to a labelled line of code.

### The example program (`controlflow1.c`)
```c
#include<stdio.h>
int main(void) {
  goto L1;
  int i;
  L2: printf("Input (0 to end) : ");
      scanf("%d",&i);
  L1: printf("%d\n",i);
  if(i) goto L2;
  return 0;
}
```
**Question the lecture asks:** *Syntax-directed flow is easy to state. Does this mean the above is syntactically incorrect?* → **No, it's legal.**

**Trace (step by step):**
1. `goto L1;` jumps straight to `L1:`, **skipping** the `scanf`.
2. `printf("%d\n", i)` prints `i`, which was **never given a value** → prints garbage (the demo printed **32766**).
3. `if(i) goto L2;` → garbage is non-zero, so go to `L2`.
4. `L2:` prompts "Input (0 to end):", reads, say, **100**. Then falls to `L1:` which prints **100**. `i` is non-zero → back to `L2`.
5. Enter **511** → prints 511 → again.
6. Enter **0** → prints 0 → `if(i)` is false → program ends.

**What it teaches:** `goto` makes the flow topsy-turvy, **but names (labels, variables) stay valid**: control flow does **not** change **scope**.

### What "syntax-directed control flow" means ⭐
- It does **NOT** mean *lexical* order. You **cannot** assume execution just runs straight from top-left to bottom-right.
- The **structure** (syntax and how parts nest) **decides** the flow, including branches and jumps back and forth.
- The **basic building blocks** (functions, if-else, while, for, switch-case) are always **single entry, one or more exits**.
- As **Parnas (1972)** explains, this means **a formal flow chart models the program adequately**.

## 3.4 Scope (formal definition)
- **Legitimacy of names** = can this piece of the program refer to a name meaningfully and consistently?
- **Strict compiler definition:** **scope = the textual extent (part of the source program) in which a particular name can be used and refers to one object that is unique to that context.**
- (The course says scope will be discussed in detail later.)

## 3.5 ⭐ Theoretical basis of structured programming: Böhm & Jacopini (1966)
> **Sequencing, selection and iteration are sufficient to express any computable function.**

- **Sequencing** = do A then B. **Selection** = `if/else` (choose). **Iteration** = loops.
- This idea didn't start with the structured-programming movement: these three structures describe the **instruction cycle of a CPU** and the operation of a **Turing machine**.
- So a CPU is **always** executing a "structured program" in this sense, **even if the instructions in memory aren't structured**.
- Practical meaning: **you never actually need `goto`.**

## 3.6 Bottom-up composition vs top-down design ⭐
### Bottom-up (how the machine runs)
- The von Neumann machine works in **unit-duration steps**, each triggered by a **unit-sized instruction**.
- Each basic operation is one of: **data transfer, control transfer, or an ALU operation**. All are **imperative operations**.
- Basic ops are **grouped into composite units**; composites are grouped into higher composites, recursively.
- The computation runs the bottom-level ops **in a fixed order, possibly repeating**, stated unambiguously at each level.

### Top-down (how we design)
- Development goes the **opposite way**: **highest-level units first**, each higher level written before its lower-level details.
- The bottom level is still elementary operations. At higher levels **units** are composite, **but the operations aren't**.

**Example 1: `printf(format, ...)`.** Read as: *transfer the string form of each argument into the places marked by the format flags, then transfer the resulting string to the stdout buffer.* So **`printf` is just data transfer** (plus expression evaluation if needed).

**Example 2: the `for` loop, rewritten ⭐**
```
for(E1; E2; E3) { S }    is equivalent to    E1;
                                             L1: if (E2) { S  E3;  goto L1 }
```
Meaning: do `E1` once; test `E2`; if true, run the body `S`, then `E3`, then jump back and test `E2` again; else stop.

This shows that **control flow depends on structure** → **"Syntax-Directed Control Flow".**

**Contrast:** someone who doesn't know the `for` syntax would read it **lexically** as "keep running E1, E2, E3, S in that order until something happens", which is **wrong**. The **real order** (E1, E2, S, E3, E2, S, E3, ...) comes from the **syntax structure**.

## 3.7 Single entry, multiple exits
True of **all** paradigms (procedural, OO, functional, logic):
- Each block (`if`, `while`, `for`, `switch`) has **one entry point** ("hooked at the top").
- Each can have **many (but finitely many) exits**: not just `goto`, but also **`break`**. **`continue`** is like a `goto` to a label at the single entry point.
- **Functions and methods** have **one entry (the call) and many exits (the `return` statements)**.

## 3.8 Practice challenges (from the lecture) with hints
1. Make **`main` call itself** recursively (C/C++/Java). *(Hint: `main` can be called like any function; use `argc` to control the depth.)*
2. Use it to **print the command-line arguments in reverse**. *(Hint: recurse first, print on the way back: the stack naturally reverses the order.)*
3. Write **mutually recursive functions** where you **can't tell by reading whether recursion ends**. *(Related to the halting problem.)*
4. **Write your own `printf`** and use it instead of the library's.
5. Make a type usable **only through your functions**; even its contents can't be seen by the user programmer. *(This is an "opaque type": e.g. declare `struct S;` in the header and define it only in the `.c` file.)*
6. Using **only `math.h`/numpy calls and arithmetic expressions with no assignment**, generate and print an input integer.

## 3.9 Types as just a set of values ⭐
**Idea:** a type is nothing more than a set of values.

The lecture's figure is a "class hierarchy" of numbers:
```
Complex Numbers
      ▲
 Real Numbers  ◄── Irrational Numbers
      ▲
Rational Numbers
      ▲
Natural Numbers  ◄── Composite Numbers
      ▲
 Prime Numbers
```
Each arrow means "is a kind of" (subset).

**Two questions:**
1. *Can we say "natural numbers have all the properties of real numbers, and some additional special properties"?* → **Yes.** That's exactly the **subclass (is-a)** relationship: a subclass keeps everything its superclass has and adds more.
2. *In Java, can we pass a **complex** number where a **natural** number is expected?* → **No.** You may pass a **natural** where a **real/complex** is expected (a subtype can stand in for a supertype), but **not the other way round**. (This is the **substitution principle**.)

---

# PART 4 — From Procedural to Object-Oriented

## 4.1 Separating the WHAT from the HOW
- We want to name things so that the name tells **what** it is, not **how** it works.
- **Example: `SortedList`.** It doesn't matter whether it's an array or another structure, or whether insertion sort keeps it sorted. All we need: methods to **put elements in, take them out, and give them in sorted order** (including "get the n-th element").
- By contrast, naming a **procedure** like **QuickSort** (partition around a pivot recursively until partitions have fewer than 3 elements) is **hard**, and a procedure's process (the "how"), once programmed, can't be programmed further anyway.

## 4.2 Why bundle data and operations (encapsulation)? ⭐
**Puzzle:** if a procedure's identity is its (fixed) algorithm, and a data structure's identity doesn't depend on the algorithms that maintain it, **why bundle them together?**

**Parnas (1972): three benefits of modular programming**
1. **Managerial:** development time shortens, because separate groups work on separate modules with little need to talk to each other.
2. **Product flexibility:** you can make **big changes to one module without changing others**.
3. **Comprehensibility:** you can study the system **one module at a time**, and the whole is better designed because it is better understood.

Also: a **module = delegation of responsibility**, not just division of labour. **Modularisation is a top-level design decision**, made **before** detailed work on modules begins.

## 4.3 ⭐ Parnas' two ways to modularise (procedural modular vs OO)
Parnas' example system is a small **text processor**, split in two ways:

| | **Decomposition 1** | **Decomposition 2** |
|---|---|---|
| **Criterion** | **Conventional**: modules match the **steps of processing** | **Information hiding**: each module **hides one design decision** from the rest |
| **Interface** | well-defined | **reveals as little as possible** about inner workings |
| **Mapping** | modules = processing steps | modules **no longer match processing steps** |

The lecture calls the second one a **"(rudimentary) OO decomposition."**

*(My example of the difference: in decomposition 1 you'd have modules "read input", "sort lines", "print output". In decomposition 2 you'd have a "line storage" module that hides how lines are stored; if you later change from arrays to linked lists, nothing else changes.)*

## 4.4 Teaser exercises
- BubbleSort ("lighter elements bubble up"), MergeSort and InsertionSort are **named after their process**. **QuickSort isn't.** Could you name it by its process?
- **Design (top level only) a dictionary** with: **O(log n)** insert, remove, retrieve by key, and retrieve the **k-th ranked element**; **O(n)** sorted listing; and **stability** (equal-rank elements come out in first-in-first-out order; searching a key returns the earliest-inserted element).
  *(Hint: a balanced binary search tree augmented with subtree sizes, plus a FIFO list for duplicate keys.)*

---

# PART 5 — Object-Orientation

## 5.1 OO is more than splitting work
- So far we separated **what** (specs of types and operations) from **how** (storage layout, procedures), still inside the procedural paradigm.
- **Sethi** says **efficiency/implementation considerations** led to the (**criticised**) design where **private members appear in the class declaration**.
- Following Parnas: OO = **information hiding**: not just separation for modularity, but also **specialisation and independence**.
- **Booch's definition ⭐:** *"Encapsulation is the process of compartmentalizing the elements of an abstraction that constitute its structure and behavior; encapsulation serves to separate the **contractual interface** of an abstraction and its **implementation**."*
  - **Interface** = what others may use. **Implementation** = how it's done inside.
  - **Encapsulation is the concrete way to achieve the abstract idea of independence.**

## 5.2 ⭐ Execution vs Simulation
| | **Imperative / Procedural** | **Emergent (OO)** |
|---|---|---|
| **View** | A **complete step-by-step breakdown** of executing a full solution. May be split into modules but is finally **compiled into a sequential program** | An information process is a **system evolving through changes of its state** |
| **Components** | procedures / modules | **objects** (the essence of the process) |
| **Properties** | variables | a **measurable property** = a computational property of some objects |
| **Change** | statements execute | **state changes = actions by objects** |

**Nygaard (1986)** and **Ole-Johan Dahl** created **Simula**, the **first OO language**, which began as a **simulation language**:
- **Simula I: designed 1962–64, available 1965.** It was soon used outside simulation as a general language: computation organised as **a system of interacting program-execution components**.
- New ideas (**subclasses and virtuals**) → **Simula 67**, a general-purpose language that was also used for simulation.

> **Slogan: Simulation means OO programming.**
> Procedural = "how to execute". OO = "what the interacting things are and how their states change."

*(My example: a bank simulation. Procedural: one long routine that loops over customers. OO: `Customer`, `Teller`, `Queue` objects that interact.)*

## 5.3 ⭐⭐ "OO on top of procedural is fraught": the Partially Hidden Class
This is a **big** topic. Go slowly.

### The claim
In **C++**, a class declaration (**including its private members**) must be **fully available to every module that uses the class**, and the storage layout must be fully spelled out. Keeping all modules consistent is **left to each module**. C++ has **no direct, enforceable syntactic support** for this, so careless but harmless-looking code can become a **time bomb**.

### The example
Two modules each declare a class named `FullClass`:

**Class Module (the real one):**
```cpp
class FullClass {
  private:
    int objectId, *data, size;       // PRIVATE data members
  public:
    FullClass(void);
    FullClass(int);
    FullClass(int, int*);
    FullClass(const FullClass&);
    void tell(void);
    void setNthElement(int,int);
    ~FullClass(void);
};
```
**User Module (written independently):** declares the **same class but WITHOUT the private data members** (only the public part).

All method code lives in the class module. The user module is developed **independently**, ignoring the class module's implementation. ("That's the whole purpose of information hiding, isn't it?")

**Question: will this work?** To answer, you need three facts:

**Fact 1: method calls are just function calls.**
A method call is **no different from a global function call**, except that **`this` (the object) is passed as a hidden first parameter**. Parameters and return values use the same **activation records on the stack**. For call-by-value, the `this` object **must be passed as a whole storage block** (its data members get onto the stack).

**Fact 2: the size and layout of an object come from the class declaration visible at that point.**
For anything that gets passed or allocated (a primitive, an array, a struct, a class instance) the compiler uses the **size and layout** from the type declaration **in scope in that module**. In imperative languages this is **static, compile-time** information, used when translating procedure activations.

**Fact 3: every module is statically typed independently.**
C (and C++, being a superset) work like this: each module's type information is converted into **plain numbers (sizes/offsets)** at machine level, losing the human names. **The linker never sees type names, variable names, parameter names or data-member names**, only **global variables, static variables and global function names**. **Method names are "mangled" with the class name** (e.g. `FullClass::tell` becomes a long single name like `_ZN9FullClass4tellEv`).

### What actually happens
- The user module thinks a `FullClass` object is small (no private data) → it **allocates too little space**.
- It calls methods in the class module, which think the object is bigger and **write into `objectId`, `data`, `size`**, i.e. **past the allocated memory**.
- Neither the **compiler** (sees each module separately) nor the **linker** (sees only function names) notices the mismatch.
- **Result:** the program **compiles, links, and partly runs**, but then gives **semantic errors → runtime crashes/exceptions.**

### The lesson ⭐
- **Objects are not the principal components of a C/C++ program.** The program is made of **modules and global functions**; classes/types are layered on top.
- There **is encapsulation**, but **modules and functions are indivisible and independent, while objects and types are not.**
- **Nygaard's rigorous definition** of OO says: *an information process is a system evolving through state changes, composed of independently interacting, evolving objects.* This is **not truly achieved** in such languages.
- **Most important difference:** **C++** (and similar, e.g. **modern Fortran with added OO**) **vs** **Java, Simula, Smalltalk**, and functional-OO languages like **Python**.

**Aside: Objective-C** and its relatives **differ from C++** here: they **treat a class's methods differently from ordinary global functions**.

## 5.4 Section conclusion ⭐
Going from procedural to OO, the **three most important aspects** are:
1. **Scope of type expressions**
2. **Object independence**
3. **Treatment of class member functions**

**Java:**
- **No global functions** (everything is inside classes).
- **Type linkages are all global** (except specific local types inside classes).
- **Type information needed for bindings is present throughout execution.**
- So **Java is "purely" OO.**

---

# PART 6 — Procedure Activation (what happens when a function is called)

## 6.1 The "times" of a program ⭐ (memorise the five)
**Typical pipeline:** **Compilation** (source → assembly/bytecode/machine code) → **Linking** (modules + static libraries) → **Loading** (into memory/VM) → **Execution**.

**Lifetime of a program:** Development (design, write, test, pickle) → Loading (may include linking and starting other programs) → Execution (may involve humans and other programs/hosts).

| Time | What happens |
|---|---|
| **Compile time** | Reading and translating **the programmer's intention as written** |
| **Linking time** | Combining the translated code with the **standard environment and support** |
| **Loading time** | **Preparing execution**, including the **dynamic part of linking** |
| **Procedure activation time** | Each time a repeatable unit of code is executed **from a specific entry point** (i.e. each call) |
| **Run time** | Each **atomic imperative machine-level instruction** |

## 6.2 Words: subroutine and activation
- **First abstractions were macros.** The power of **functions with parameters and return values** was realised much later; the power of **recursion and composing functions** is still **under-appreciated** in big software projects.
- **Functions** compute a value. **Pure procedures** just wrap a long process in a neat name.
- **The lecture's convention: call all callables "subroutines", and a call a "Procedure Activation"** (Sethi 1996).

## 6.3 ⭐ The six features of a subroutine (very likely exam question)
1. **Name and signature (prototype).**
2. **Parameter passing and access** (preparing for the call, and the callee's **preamble**).
3. **Allocation and access to local (and temporary) objects; access to non-local objects.**
4. **The imperative action procedure** (the translated body).
5. **Preparation to return** (return values, **winding up**, e.g. **calling C++ destructors** for locally created objects).
6. **Restoration of program state at return.**

## 6.4 The `euclid.c` example (recursive GCD)
```c
int gcd (int parama, int paramb) {
    static int xstatic = 0;

    xstatic = parama - paramb;
    if (parama > 0 && paramb > 0) {
        if (xstatic < 0)
            return gcd(parama, -xstatic);
        else if (xstatic > 0)
            return gcd(paramb, xstatic);
        else return parama;
    }
    else return -1;
}
```
**How it works:** `xstatic` = the difference of the two numbers. If negative, recurse with `(a, -diff)` (i.e. `(a, b−a)`); if positive, recurse with `(b, diff)`; if zero, the numbers are equal → that's the GCD. If either input is ≤ 0, return **−1** (invalid).

**Trace of `gcd(12, 18)`:**
1. `xstatic = 12 − 18 = −6` (negative) → `gcd(12, 6)`
2. `xstatic = 12 − 6 = 6` (positive) → `gcd(6, 6)`
3. `xstatic = 6 − 6 = 0` → return `parama` = **6** ✅

**Key points to note:**
- It is **recursive** and the recursive calls are in **tail position** (each is `return gcd(...)`).
- **`xstatic` is a static local variable:** there is **only one copy** shared by **all** the recursive calls. It lives in the **static data area**, **not** in the stack frame. (In the assembly it shows up as `xstatic.0(%rip)` and `.comm xstatic.0,4,4`.)
- `parama` and `paramb` are **parameters** and live in the **stack frame**.

## 6.5 Reading the assembly (how to understand it, without fear)
The compiler was run as `gcc -g -S euclid.c` (`-S` = stop at assembly, `-g` = add debug info). Lines starting with a dot (`.loc`, `.cfi_...`, `.debug_...`) are **notes for the debugger**, not real instructions.

**Registers recap:** `%rbp` = frame pointer (marks the base of the current frame), `%rsp` = stack pointer (top of the stack), `%eax` = accumulator (return value), `%edi`, `%esi` = first and second argument.

### Preamble (set-up when the function starts)
```asm
pushq %rbp            ; save the caller's frame pointer on the stack  (this is the saved link to the caller)
movq  %rsp, %rbp      ; the new frame pointer = current top of stack  (callee's frame base is fixed)
subq  $16, %rsp       ; move the stack top down to make room for locals and temporaries
movl  %edi, -4(%rbp)  ; copy parama into the frame at offset -4
movl  %esi, -8(%rbp)  ; copy paramb into the frame at offset -8
```
Important: **names (`parama`, `paramb`) become offsets from `%rbp`** (−4, −8). The machine doesn't know names.

### Body
- `cmpl $0,-4(%rbp)` then `jle .L2`: compares `parama` with 0 and jumps to `.L2` (return −1) if `parama <= 0`. Same for `paramb`.
- `movl xstatic.0(%rip),%eax` loads the static variable; `testl`/`jns`: if it's not negative, skip to the `else if` part (`.L3`).
- To call `gcd(parama, -xstatic)`: load `xstatic`, **negate it** (`negl %eax`), put it in `%esi`; put `parama` in `%edi`; then **`call gcd`**; then jump to the end (`jmp .L4`).
- `.L3`: check `xstatic > 0`; if not, go to `.L5`. Else set up `(paramb, xstatic)` and `call gcd`.
- `.L5`: `movl -4(%rbp),%eax` → put `parama` in `%eax` (the return value).
- `.L2`: `movl $-1,%eax` → return −1.

### Epilogue (clean-up when the function ends)
```asm
.L4:
   leave     ; undo the frame: restore %rsp and %rbp (winding up)
   ret       ; pop the return address and jump back to the caller
```
**"Recursive returns":** after a recursive call comes back, the **result is already in `%eax`**, so the code just jumps to `leave`. **Only the base cases** (return `parama`, return −1) **explicitly put a value in `%eax`.**

### The debug sections
Long listings named `.debug_info`, `.debug_abbrev`, `.debug_aranges`, `.debug_line`, `.debug_str` are **debugging metadata** that `-g` adds so a debugger can map machine code to your source. **They are not program logic. You do NOT need to memorise them.** They just record names like `parama`, `paramb`, `xstatic`, the file name and the compiler version.

> **Careful (my addition, not in the lecture):** the course *model* says "the caller pushes parameters onto the stack". Real x86-64 gcc output passes the first arguments in **registers** (`%edi`, `%esi`) and the callee copies them into its frame. **In the exam, follow the course/Sethi model** (caller pushes parameters), and mention registers only if asked about the actual assembly.

## 6.6 ⭐ The stack frame (activation record): DRAW THIS
### Sethi's model (fields of an activation record)
```
 ▲ stack grows up
 │   ┌────────────────────┐
 │   │ Control Link       │  ← points to the CALLER's frame (so we can go back)
 │   ├────────────────────┤
 │   │ Access Link        │  ← for reaching NON-LOCAL variables (enclosing scope)
 │   ├────────────────────┤
 │   │ Saved State        │  ← saved registers / return info
 │   ├────────────────────┤
 │   │ Parameters         │  ← the actual arguments
 │   ├────────────────────┤
 │   │ Function Result    │  ← space for the return value
 │   ├────────────────────┤
 │   │ Local Variables    │  ← the callee's own variables
 │   └────────────────────┘
```
**What each field means:**
- **Control link:** a pointer to the caller's frame, so when we finish we know whose frame to return to.
- **Access link:** a pointer used to find **non-local** data (variables from an enclosing function).
- **Saved state:** the machine state to restore on return (registers, return address).
- **Parameters:** values passed in.
- **Function result:** where the return value is placed.
- **Local variables:** the function's own variables.

### What happens on a call, step by step ⭐
1. Start from the **top of the caller's frame**.
2. **Space is allocated on the stack for the return value.**
3. **Actual parameters are pushed** on the stack.
4. The **caller's frame base (frame pointer) is pushed.**
5. **`call` pushes the return address** (the address of the next instruction).
6. The **callee uses the space above for its local variables.**

### Sethi's C/C++ layout (laid out from the frame pointer)
| Region | Notes |
|---|---|
| **Incoming parameters** (parameter 1, parameter 2...) | pushed by the caller |
| **Saved state information** | |
| **Local variables** | |
| **Temporary storage** | |
| **Outgoing parameters** (these are the *incoming* parameters of the **next frame**) | |

Also shown: **"this frame"** and **"next frame"**, and the idea of **caller-saves / callee-saves** (who is responsible for saving registers).

## 6.7 A call and a subroutine, in order
1. **Incoming parameters** are pushed **by the caller** onto the stack **before** the `call` instruction.
2. The **`call` instruction saves the state** (the return address; and the processor status word etc. if the code must be re-entrant).
3. The **callee's frame pointer is fixed** (`pushq %rbp; movq %rsp,%rbp`).
4. **Local variables and temporaries** get space by adjusting `%rsp` (`subq $16,%rsp`).
5. The **caller's base pointer is saved in the "saved state information" block: this is the access (link) to the caller's frame.**
6. **Outgoing parameters** are prepared for the **recursive call**.
7. **Winding up:** `leave` + `ret`. The return value stays in the **accumulator**.

## 6.8 What the instructor was trying to find out (lessons)
He experimented with many C and C++ programs, **debugging line by line and inspecting stack frames**, to answer:
- **Which names are kept in the symbol table for the linker?** → only **globals, statics, global function names** (method names are **mangled with the class name**).
- **Which names become offsets, and how?** → **locals and parameters become offsets from the frame pointer.**
- **Are C++ method calls handled like global functions or differently?** → **the same, with `this` as an implicit first parameter.**
- **How is polymorphism enabled in C++?** How is the **class name preserved**, how are **method signatures uniquely identified** so that membership, hierarchy, **overloading, overriding, virtual methods (abstraction) and reflection** work?

---

# PART 7 — OOP in Java

## 7.1 When to use Java
- **Mainly for interfacing** (not only UI: development **without the nitty-gritty**).
- When the focus is **core business logic**, not implementation details.
- When **user and system requirements** drive the choice of **libraries and class hierarchies**.
- A provocative question raised: *"Then, wouldn't LLMs replace Java programmers?"* (Open-ended; argue both sides if asked.)

## 7.2 When NOT to use Java
- **The Banana Monkey Jungle Problem** *(the lecture only names it; the usual meaning, from Joe Armstrong: you wanted a banana, but you got a gorilla holding the banana and the entire jungle, i.e. **reusing one object drags along all its dependencies**)*.
- **The problem with the "contain and delegate" solution** (from the article "Goodbye, Object-Oriented Programming").
- **Is "reusability" so holy in the age of LLMs?**
- **Polymorphism, Contracts and Flexibility: the "tension-triad".**

## 7.3 Exercises (and what they point at)
1. A **two-class hierarchy**: make an instance of one class **forget what it was and imagine itself to be of the other class**.
2. Make a **single instance** behave differently in a method call from **all other instances** of its class.
3. **Replace an instance of a superclass by an instance of a subclass** after the specialisation has been developed.
4. Make a **generic method adapt to a new specialisation**.

*(My interpretation: these are things **Java can't do directly** but **CLOS-style (MOP) languages can**: changing an object's class at runtime (`change-class`), per-instance behaviour (`eql` specializers), and class/method redefinition affecting existing instances. This leads into Part 8.)*

---

# PART 8 — "Generics": MOP, CLOS and Reflection

*(The section is titled "Generics" because CLOS uses **generic functions**. The content is really about metaobject protocols, CLOS and reflection.)*

## 8.1 The Metaobject Protocol (MOP) ⭐
*(From the book "The Art of the Metaobject Protocol".)*

**The tension in language design:** two demands pull in opposite directions:
- **Elegance / expressive power** (Scheme, Prolog, ML: clean, theoretical)
- **Practicality / efficiency** (C, C++: fast, controllable)

Industry finds "theoretical" languages impractical and slow, so they stay in academia.

**The book's claim:** these two demands are **compatible, not conflicting**. You can have languages **as clean as the purest theoretical designs, with no loss of performance or control over implementation.**

**How?** Base the language design on **metaobject protocols**.

> **Definition: Metaobject protocols are interfaces to the language that give users the ability to incrementally modify the language's behaviour and implementation, as well as the ability to write programs within the language.**

They **blur the line between language designer and language user.**
- **Traditionally:** designers make languages with **fixed, well-defined meaning**; users treat the language as an **unchangeable black box** and only get flexibility by building things on top. This sharp split was seen as a proper division of labour; it was also assumed that a language **must be rigid** so that implementations can be portable and compilers efficient.
- **With a MOP:** the user can reach inside and adjust how the language itself behaves.

*(Analogy, my example: a car with a sealed bonnet vs a car where the engine is accessible and tunable.)*

## 8.2 CLOS: what it is and its features
**CLOS = Common Lisp Object System** (the object system of Common Lisp).

| Feature | Meaning |
|---|---|
| **Class Redefinition** | A class definition can be **changed**, and the change **propagates to subclasses and to existing instances** |
| **Method Redefinition** | A new definition **completely replaces** the old one |
| **Forward-referencing of superclasses** | A class can be defined in terms of another class **before that other class is defined** |
| **Implicit (inferred) generic function definitions** | A generic function's definition can be **inferred from its method definitions** |
| **User control of method combination** | You can customise how the **effective method** is built; arbitrary control structures and handling of method qualifiers |
| **`eql` specializers** | Methods can be specialised **not only to classes, but to individual objects** |
| **Slots with `:class` allocation** | Some slots are **per-instance**, others **shared across all instances** (like Java `static`) |
| **Fully integrated types and classes** | Common Lisp **types and CLOS classes** are unified, so you can specialise methods on **primitive classes (e.g. `symbol`)** and **structure classes (`defstruct`)** |

**Key vocabulary:**
- **Slot** = a field/data member of an object.
- **Generic function** = a function **name** that has **several methods** attached; which method runs depends on the **classes of the arguments**.
- **Method** = one implementation of the generic function for particular argument classes.

## 8.3 CLOS program structure
- A CLOS program is made of **`defclass`, `defgeneric`, `defmethod`** forms mixed with ordinary Common Lisp code.
- **Executing** these forms **defines the program's classes, generic functions and methods.**
- **Behind the scenes:** executing them creates **internal objects (metaobjects)** that record what you declared. The system uses a class's metaobject to **create instances and access slots**, and a generic function's metaobject to **invoke it**.

## 8.4 Example: Painting
```
                 t
                 │
          standard-object
           /            \
     color-mixin      rectangle
           \            /
          color-rectangle
                 │
               door
```
- `color-rectangle` inherits from **both** `color-mixin` and `rectangle` → **multiple inheritance**.
- There's a **generic function `paint`** with **three methods (#1, #2, #3)** attached to different classes.
- Legend in the figure: solid line = **direct superclass/subclass**; dashed = **generic function ↔ its methods**; dotted = **specializer / direct method**; dash-dot = **class-of**.
- **Idea:** calling `(paint door)` makes CLOS pick the **applicable methods** (those whose class is `door` or one of its superclasses) and order them **from most specific to least specific**.

## 8.5 Reflection ⭐
**Definition (Scott, textbook §12.2):** *A reflection mechanism allows a program to reason about itself.*

**CLOS reflection functions:**
- **`(find-class <symbol> &optional <errorp>)`** → returns the **class metaobject** with that name. If there's no such class: an **error is signalled** if `errorp` is missing or non-nil, and **`nil` is returned** if `errorp` is `nil`.
- **`(class-of <object>)`** → returns the **class metaobject of the object's class.**

**What the textbook says:**
- **No widely used language is fully reflective** (able to inspect every aspect of its structure and current state), but **significant forms** exist in several languages, **Prolog** among them (its `clause` predicate lets you build a **metacircular interpreter**: Prolog written in Prolog; likewise Lisp using `eval` and `apply`).
- **Rich reflection** also appears in **Java, C#, and the major scripting languages** (they let a program inspect its **full type structure**).
- **Python** lets a program inspect its **source as text**, but that's **less powerful than the homoiconic inspection** of **Prolog or Scheme**, where a program can **reason about its own code structure directly**. (*Homoiconic* = code and data have the same form.)

### ⭐ Two kinds of reflection
| Type | Meaning | Example |
|---|---|---|
| **Introspection** | **Get information about the program itself** and use it inside the program | Java's `obj.getClass().getName()` |
| **Intercession** | **Go further: change the program's own behaviour** | CLOS changing a class or an object's class at runtime |

- **Lisp and Smalltalk have both** introspection and intercession.
- **Java is basically introspective only.**

## 8.6 ⭐ Method combination (the order in which CLOS runs methods)
**Standard method combination:**
1. **Before-methods** run first, **from most specific to least specific.**
2. **The most specific applicable primary method** runs next.
3. **After-methods** run last, **from least specific to most specific.**
- The **return value** of the generic function = **whatever the primary method returns.**
- **`apply-methods`** does this sequencing, calling **`apply-method`** for each individual method.
- Running a method is handled **like a Lisp interpreter handles a normal function call**: **evaluate the method body in the lexical environment of its `defmethod`, extended with new variable bindings made by matching the parameter list with the actual arguments.**

**Memory trick:** *Before: specific → general. Primary: most specific only. After: general → specific.* It's symmetric, like walking into nested rooms and then back out.

*(My example: for `(paint door)` with before-methods on `door` and on `rectangle`: order is before(`door`), before(`rectangle`), primary, after(`rectangle`), after(`door`).)*

## 8.7 Performance vs expressive power
- **Naive MOP implementations are woefully inefficient.** At minimum, **generic function calls** and **slot access** must be sped up.
- **The basis of the optimisation:** **compute important internal results once, save them and reuse them. This technique is MEMOIZATION.**
- *(My example: `fib(30)` computed naively repeats work enormously; if you store each `fib(k)` the first time you compute it, every later request is a lookup.)*

---

# PART 9 — Revision cheat sheet

## 9.1 Definitions to write word-for-word
- **Abstraction (Scott):** (1) of the programmable machine, (2) naming code to say *what* while hiding *how*. **Control abstraction = subroutine; data abstraction = class.**
- **Formal language vs programming language:** formal = strings only; programming = strings + semantics.
- **Mechanical computation:** can be reproduced exactly (even if extremely slowly) by coarse devices with no discretion.
- **Not programmable:** not finitely expressible (most reals; equality of reals undecidable); situations that can't be finitely specified (autonomous control); captchas.
- **Three essentials of programming:** elementary operations, sequencing (no timing), repetition.
- **Program vs computation:** program = static; computation = dynamic.
- **Syntax-directed control flow:** the order of execution is decided by the **structure** of the code, **not** by its left-to-right/top-to-bottom textual order. Building blocks are **single entry, multiple exit**.
- **Böhm–Jacopini:** **sequence, selection, iteration** are sufficient to express any computable function.
- **Scope:** the **textual extent** in which a name can be used and refers to one unique object.
- **Encapsulation (Booch):** compartmentalising the structure and behaviour of an abstraction; separates the **contractual interface** from the **implementation**.
- **Information hiding (Parnas):** each module **hides a design decision**; modules **don't** correspond to processing steps. Benefits: **managerial, product flexibility, comprehensibility.**
- **Object-orientation (Nygaard):** an information process is **a system evolving through state changes, composed of independently interacting, evolving objects.** Origin: **Simula** (Dahl and Nygaard; Simula I 1962–65, Simula 67 adds subclasses and virtuals).
- **Procedure activation:** a call to a subroutine (Sethi). **Six features:** name/signature; parameter passing; locals and non-locals; body; preparation to return; restore state.
- **Five "times":** compile, link, load, procedure activation, run.
- **Activation record fields:** control link, access link, saved state, parameters, function result, local variables (+ temporaries, outgoing parameters in the C frame).
- **MOP:** interfaces that let users **incrementally modify a language's behaviour and implementation.**
- **Reflection:** a program reasoning about itself. **Introspection** = read info; **Intercession** = change behaviour. Java = introspection only; Lisp/Smalltalk = both.
- **Memoization:** compute once, save, reuse.

## 9.2 Tables to be able to reproduce
1. **Four models → four paradigms** (2.9)
2. **Procedural (execution) vs OO (simulation)** (5.2)
3. **Parnas decomposition 1 vs 2** (4.3)
4. **C++ vs Java OO** (below)
5. **Bottom-up vs top-down** (3.6)
6. **Introspection vs intercession** (8.5)
7. **Dijkstra vs DuWorks–Smoliar** (below)

**C++ vs Java:**
| | **C++** | **Java** |
|---|---|---|
| Global functions | yes | **none** |
| Class declaration | must be repeated (fully) in each module that uses it | type linkage is **global** |
| Linker sees | only global names (methods mangled with class name), **no types** | n/a: type info is **present at run time** |
| Methods | ≈ global functions with implicit `this` | proper part of the class |
| Verdict | **objects are not principal**: "OO on top of procedural" | **"purely" OO** |

**Dijkstra vs DuWorks & Smoliar:**
| | **Dijkstra** | **DuWorks & Smoliar** |
|---|---|---|
| Stand | **discipline, mathematical purity**, ban `goto`, ALGOL-60 | **practitioners**: **observation before generalisation**; PL/I; **"system elegance"** |
| Criticism | – | ALGOL-60's purity was **"formal overkill"**; Dijkstra put the **cart before the horse** |
| **Course's stand** | discipline = **conceptual integrity at human level (Brooks)**, **not** mathematical purity; the field is **empirical, like physics**; paradigms are **enablers, not fetters** | |

## 9.3 Practice questions with model answers

**Q1 ⭐ Why is C++ called "OO on top of procedural", and what can go wrong?**
In C++ a class is compiled like a C struct plus global functions. Every module needs the **full class declaration (including private members)** so it knows the object's **size and layout**; each module is typed **independently**; and the **linker only sees global names** (methods mangled with the class name), **not types**. If two modules declare the same class **inconsistently** (e.g. one omits the private members), the compiler and linker **both accept it**, but the size/layout mismatch causes **runtime crashes**. So in C++ **modules and functions, not objects, are the independent principal units.** **Java** avoids this: **no global functions, global type linkage, type info at run time.**

**Q2 ⭐ State Böhm–Jacopini and say why it matters.**
**Sequence, selection and iteration suffice to express any computable function.** So `goto` isn't needed and structured programming loses no power. The CPU's instruction cycle and a Turing machine are built from these three anyway.

**Q3 ⭐ What is syntax-directed control flow? Example.**
Execution order is decided by **syntactic structure**, not textual order. Example: `for(E1;E2;E3){S}` is `E1; L1: if(E2){S E3; goto L1}`. Textually the order is E1, E2, E3, S, but the actual order is E1, E2, S, E3, E2, S, E3 ... Blocks and functions are **single-entry, multiple-exit**.

**Q4 ⭐ Explain Parnas' two modularisations and relate them to OO.**
Decomposition 1: modules = **processing steps** (conventional). Decomposition 2: modules = **information hiding**, each hides **one design decision**, interface reveals little. The second is a **rudimentary OO decomposition**. Benefits: **managerial** (parallel work), **flexibility** (change one module only), **comprehensibility**.

**Q5 ⭐ List and explain the six features of a subroutine.**
See 6.3. Add a one-line `gcd` illustration for each: name/signature `int gcd(int,int)`; parameters copied into frame offsets; locals/temps via `subq`; body; return value in `%eax` + `leave`; `ret` restores state.

**Q6 ⭐ Draw the activation record and describe a call step by step.**
Draw the six-field diagram (6.6) and list the six call events.

**Q7 Trace `gcd(12,18)`; where is `xstatic` stored?**
`gcd(12,6)` → `gcd(6,6)` → **6**. `xstatic` is a **static local**, stored in the **static data area** (one shared copy); parameters and the saved base pointer are in the **stack frame**.

**Q8 Compare the imperative and OO views of computation.**
Use the table in 5.2 and mention Simula/Nygaard: **"simulation means OO."**

**Q9 What is a MOP, and what are introspection and intercession?**
MOP = interfaces letting users **incrementally modify the language's behaviour and implementation**; it blurs designer and user, and resolves the tension between **elegance and efficiency**. **Introspection** = a program reads information about itself; **intercession** = a program changes its own behaviour. **Java: introspection only; Lisp/Smalltalk: both.**

**Q10 What is not programmable?**
See 1.8: **not finitely expressible** (most reals; real equality undecidable), **situations that can't be finitely enumerated** (autonomous control such as take-off angle of attack), **captchas**.

**Q11 Standard method combination in CLOS.**
**Before** (most specific → least) → **most specific primary** → **after** (least specific → most). Result = primary method's value.

**Q12 What was the Dijkstra debate, and what's the course's stand?**
See 2.4 to 2.7 and the table above.

**Q13 Why do we encapsulate? (Parnas)**
Three benefits: **managerial, product flexibility, comprehensibility**; a module is **delegated responsibility**, and modularisation is a **top-level design decision**.

**Q14 Define abstraction. Give control and data abstraction examples.**
Focusing on **what**, hiding **how**. **Control abstraction: subroutine. Data abstraction: class.** (Plus the GCD ladder as an example.)

**Q15 What are the five "times"?**
Compile, link, load, procedure activation, run: with the one-line meaning each (6.1).

**Q16 Types as sets of values. Can a complex number be passed where a natural number is expected in Java?**
**No.** A **subtype** (natural) can stand in for a **supertype** (real/complex), **never the reverse**.

## 9.4 Last-minute tips
- The **assembly/debug listing** looks scary, but only the **preamble, the call, `leave`/`ret` and the `%eax` return** matter. **Don't memorise the debug sections.**
- Learn the **tables**; they convert directly into marks.
- Use the **lecture's keywords** in your answers: *syntax-directed, single entry/multiple exits, information hiding, conceptual integrity, empirical not analytical, simulation, intercession, memoization.*
- **Time plan for tonight:** read Part 0 (20 min) → Parts 1 to 3 (60 min) → Parts 4 to 5 (50 min) → Part 6 (60 min) → Parts 7 to 8 (40 min) → Part 9 questions without looking at answers (40 min) → sleep.

---
*End of notes.*
