# 🧭 POPL Midsem Master Navigator & Lookup Guide

> **How to use this during the exam / prep:**
> - Need the folder layout? Check [Folder Architecture](#-organized-folder-architecture) below.
> - Stuck on an exam question? Jump straight to [Part 0: "I'm Stuck On..." Emergency Decision Table](#part-0--im-stuck-on-emergency-decision-table).
> - Need rapid definitions or tricky code snippets? Check [Part 1: Predicted Exam Questions & Solutions (By Topic)](#part-1--predicted-exam-questions--solutions-by-topic).
> - Need to know which file contains what? Go to [Part 2: Complete Directory & File Inventory](#part-2--complete-directory--file-inventory).
> - Need concepts explained in modular bites? Go to [Part 3: Modular Topic-by-Topic Syllabus Guide](#part-3--modular-topic-by-topic-syllabus-guide).
> - Need past exam answers? Go to [Part 4: Past Exam Question-by-Question Solutions](#part-4--past-exam-question-by-question-solutions).
> - Fast Ctrl+F search terms? Go to [Part 5: Ctrl+F Fast Search Keywords](#part-5--ctrlf-fast-search-keywords).

---

## 📂 Organized Folder Architecture

```text
POPL Midsem/
├── POPL_NAVIGATOR.md                         <-- YOU ARE HERE (Central routing & index)
├── POPL_ABSOLUTELY_EVERYTHING_MASTER.md      <-- Exhaustive 4,233-line syllabus bible (101 sections)
│
├── 📁 07_Predicted_Questions_and_Solutions/  <-- ⭐ SECRET WEAPON: High-Yield Exam Q&As by Topic
│   ├── 00_Rapid_Definitions_and_Distinctions.md    <-- 40+ 1-minute definitions & pairwise distinctions
│   ├── 01_C_Modules_Linkage_and_Storage.md         <-- C separate compilation, extern, leap of faith
│   ├── 02_Cpp_Module_Decoupling_and_Abstract_Classes.md <-- Representation leak & Stroustrup pattern
│   ├── 03_Procedural_vs_OO_Design_and_Simulation.md     <-- Nygaard simulation & Booch/Parnas
│   ├── 04_Procedure_Activation_and_Stack_Frames.md      <-- Recursive GCD stack frame, x86-64 assembly
│   ├── 05_Binding_Times_and_Parameter_Passing.md        <-- 7-stage binding matrix & aliasing bugs
│   ├── 06_Language_Philosophy_Bohm_Jacopini_and_CLOS.md <-- Dijkstra debate, Bohm-Jacopini, CLOS/MOP
│   └── 07_Tricky_Code_Snippets_and_Bug_Traps.md    <-- 15 classic "What Prints? / Spot the Bug" traps
│
├── 📁 01_Notes_and_Slides/
│   ├── Slides_17Sept2026_popl.pdf            <-- Official lecture slide deck
│   ├── Slides_17Sept2026-TILL ENDSEM...      <-- Extended lecture slides
│   ├── POPL_exam_notes_midsem                <-- 16-page high-density summary PDF with recall boxes
│   ├── POPL_Midsem_Compiled.pdf              <-- 38MB mega compiled notes
│   └── popl_tb.pdf                           <-- Standard textbook
│
├── 📁 02_Past_Exams_and_Solutions/
│   ├── PoPL2024MidSem1.pdf                   <-- 2024 Midsem Question Paper
│   ├── PoPL 2024 MidSem Solution.pdf         <-- 2024 Midsem Official Solutions
│   ├── PoPL2025MidSem1.pdf                   <-- 2025 Midsem Question Paper
│   ├── midsem-solutions.pdf                  <-- 2025 Midsem Official Solutions
│   ├── compre-answer-key.pdf                 <-- Comprehensive Exam Answer Key
│   ├── PoPL Compre Solutions.pdf             <-- Comprehensive Exam Worked Solutions
│   └── PoPL2025EndSemAssignment.pdf
│
├── 📁 03_Quizzes/
│   ├── POPL Eval Quiz.pdf
│   ├── Quiz 1 - Procedural to OO _ Corrected Quiz_ Attempt review.pdf
│   ├── Quiz 1-1.pdf / Quiz 1-2.pdf / Quiz 1-3.pdf
│   └── Quiz 2.pdf / Quiz 2 - Post-Mid-Sem Quiz_ Attempt review.pdf
│
├── 📁 04_Assignments_and_Tutorials/
│   ├── Assignment 1.pdf / Assignment 2.pdf / Assignment 3.pdf
│   ├── midsem-assignment.pdf / Practice Assignment.pdf / Tasks for OO Module.pdf
│   ├── Tutorial 1.pdf / Tutorial 2.pdf / Tutorial 3.pdf
│   ├── OO Language Comparison Tut.pdf
│   ├── poplqbank.pdf
│   └── pre-midsem-practice-questions.pdf
│
├── 📁 05_Readings_and_Articles/
│   ├── Bjarne Stroustrup On Abstract Classes.pdf  <-- Core reading for C++ decoupling
│   ├── GOODBYE OOP.pdf                            <-- Banana/Gorilla OOP critique
│   ├── chicken crossing puzzle.pdf                <-- Simulation modeling puzzle
│   └── debates (1).zip
│
└── 📁 06_Code_and_Implementations/
    ├── AbstractClassModules.zip              <-- Decoupled C++ abstract class demo
    ├── IndependentModules.zip                <-- C independent modules & headers demo
    ├── HiddenClass.zip                       <-- Partially hidden C++ class demo
    ├── dynamicBindingJava.zip                <-- Java dynamic dispatch demo
    ├── An Example Program for Q2...zip       <-- Midsem 2025 Q2 source code
    ├── Example Program for Q2 - 2025 PDF.pdf
    ├── Example Program GCD.pdf               <-- Recursive GCD x86-64 assembly walkthrough
    ├── params.tar.gz                         <-- Parameter passing examples
    ├── fghc.lisp / mergesort-final.lisp / painting.lisp <-- Common Lisp & CLOS files
    └── Dictionary (2).zip / Dictionary (3).zip / examplesOf23aug2024.zip
```

---

# Part 0 — "I'm Stuck On..." Emergency Decision Table

| If your question mentions... | Go to this exact Q&A Model Answer | Key Takeaway / What to Write |
| :--- | :--- | :--- |
| **Short 1–2 Mark Definitions & Distinctions** | [00_Rapid_Definitions_and_Distinctions.md](./07_Predicted_Questions_and_Solutions/00_Rapid_Definitions_and_Distinctions.md) | 40+ ready-to-write definitions: Referential transparency, type punning, nominal vs structural typing, deep vs shallow binding, closure vs thunk, object slicing. |
| **"What Does This Print?" / Code Bugs** | [07_Tricky_Code_Snippets_and_Bug_Traps.md](./07_Predicted_Questions_and_Solutions/07_Tricky_Code_Snippets_and_Bug_Traps.md) | 15 code traps: Missing virtual destructor, object slicing, returning local stack address, pointer to array vs array of pointers, XOR swap aliasing trap, struct padding. |
| **C Module Independence / Header Sharing / Multiple Definition** | [Topic 01 Q1 & Q2](./07_Predicted_Questions_and_Solutions/01_C_Modules_Linkage_and_Storage.md) <br> [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf) | Headers contain **declarations** only (`extern`, typedef, function prototypes). Definitions (`int x;`, function bodies) in headers cause **linker multiple definition errors** if included by >1 file. The compiler takes a "leap of faith"; the linker resolves addresses. |
| **C++ Abstract Class / Making Modules Independent** | [Topic 02 Q1 & Q2](./07_Predicted_Questions_and_Solutions/02_Cpp_Module_Decoupling_and_Abstract_Classes.md) <br> [midsem-solutions.pdf](./02_Past_Exams_and_Solutions/midsem-solutions.pdf) (Q1) | In C++, concrete classes in headers force clients to recompile when private members change because the compiler needs object size & layout! **Solution:** Pure abstract base class with pure virtual functions (`virtual void f() = 0;`), factory function returning pointer/reference, concrete class hidden in `.cpp`. Client depends only on interface! |
| **Procedural to OO Conversion / Designing OO System** | [Topic 03 Q1 & Q2](./07_Predicted_Questions_and_Solutions/03_Procedural_vs_OO_Design_and_Simulation.md) <br> [midsem-solutions.pdf](./02_Past_Exams_and_Solutions/midsem-solutions.pdf) (Q2) | **Procedural:** Passive data + active functions that manipulate data (`f(data)`). Step-by-step command execution. <br>**OO (Nygaard):** Simulation of real-world entities. Objects have identity, internal state, and behavior. Main program instantiates objects and lets them interact via message passing. |
| **Activation Record / Stack Frame Layout / Assembly** | [Topic 04 Q1 & Q3](./07_Predicted_Questions_and_Solutions/04_Procedure_Activation_and_Stack_Frames.md) <br> [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf) (Q3) | Frame contains: Incoming parameters, Return address, Saved caller `%rbp`, Local variables, Temporaries, Outgoing parameters. Prologue: `pushq %rbp; movq %rsp, %rbp; subq $N, %rsp`. Epilogue: `leave; ret`. Stable offsets from `%rbp`; `%rsp` fluctuates. |
| **Binding Time (Static vs Dynamic, Compile vs Link vs Run)** | [Topic 05 Q1](./07_Predicted_Questions_and_Solutions/05_Binding_Times_and_Parameter_Passing.md) <br> [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf) (Q2) | 7 distinct times: Language design (`int` keyword), Language implementation (`sizeof(int)` representation), Compile time (variable type, local variable offset from `%rbp`), Link time (external function address), Load time (absolute memory address), Procedure call (formal parameter to actual argument), Run time (variable value). |
| **Pass-by-Value vs Reference vs Pointer / Aliasing** | [Topic 05 Q2 & Q3](./07_Predicted_Questions_and_Solutions/05_Binding_Times_and_Parameter_Passing.md) | **Pass-by-value:** Value copied. Changes local only.<br>**Pass pointer by value (C):** Address copied. Mutating `*p` changes original, but changing `p` doesn't affect caller's pointer.<br>**Pass-by-reference (C++):** Language-level alias. No new object created; callee directly accesses caller's variable. <br>**Aliasing:** When two formal parameters reference same location (`foo(x, x)`). |
| **Opaque Pointer Pattern / FILE * in C** | [Topic 01 Q3](./07_Predicted_Questions_and_Solutions/01_C_Modules_Linkage_and_Storage.md) | Forward declaration `typedef struct Vault Vault;` in header. Struct body defined only in `.c`. User cannot see struct size or fields; all operations via function pointers/handles. `FILE *` in `<stdio.h>` is the classic standard example. |
| **Dijkstra vs Linguists / Bohm-Jacopini Theorem** | [Topic 06 Q1 & Q2](./07_Predicted_Questions_and_Solutions/06_Language_Philosophy_Bohm_Jacopini_and_CLOS.md) | **Dijkstra/Purists:** Formal mathematical discipline, intellectual manageability, eliminate `goto`. <br>**Bohm-Jacopini:** Sequence, selection, iteration are sufficient. Can eliminate `break` only by adding flags. |
| **CLOS / MOP / Reflection (Introspection vs Intercession)** | [Topic 06 Q4](./07_Predicted_Questions_and_Solutions/06_Language_Philosophy_Bohm_Jacopini_and_CLOS.md) | **Introspection:** Read program structure (`instanceof`, Java reflection). <br>**Intercession:** Modify language semantics/behavior at runtime (CLOS/Smalltalk). <br>**Method combination:** `:before` (most specific to least), primary method, `:after` (least specific to most). |

---

# Part 1 — Predicted Exam Questions & Solutions (By Topic)

This is your direct question bank with full model answers matching the professor's exact format:

### 0. [00_Rapid_Definitions_and_Distinctions.md](./07_Predicted_Questions_and_Solutions/00_Rapid_Definitions_and_Distinctions.md)
* 40+ crisp 1-minute definitions and pairwise comparison tables covering Abstraction, Typing Systems, Memory/Linkage, OO Mechanics, and Metaprogramming.

### 1. [01_C_Modules_Linkage_and_Storage.md](./07_Predicted_Questions_and_Solutions/01_C_Modules_Linkage_and_Storage.md)
* **Q1:** Separate compilation with `extern int *data;` vs `int data[10];` (Links without error, but crashes with SegFault at runtime — detailed byte-level explanation).
* **Q2:** Linker "Leap of Faith" & Struct Inconsistencies across two `.c` files (Byte offset calculation).
* **Q3:** Opaque Pointer Pattern (`FILE *` in C) with full header `vault.h`, `vault.c`, and client `main.c`.
* **Q4:** Tentative Definitions (weak symbols) vs Multiple Definitions (strong symbols) vs `static` internal linkage.

### 2. [02_Cpp_Module_Decoupling_and_Abstract_Classes.md](./07_Predicted_Questions_and_Solutions/02_Cpp_Module_Decoupling_and_Abstract_Classes.md)
* **Q1:** C++ Representation Leakage & Compilation Coupling (Why adding `private int x;` forces all clients to recompile).
* **Q2:** Stroustrup’s Pure Abstract Base Class Pattern (`ITexture.h`, `TextureImpl.cpp`, factory `createTexture()`).
* **Q3:** Vtable and Vptr Internal Mechanics (Memory diagram, dynamic dispatch assembly `call *16(%rax)`).
* **Q4:** C++ vs Java Compilation & Linking Coupling (Why Java bytecode does not force caller recompilation).

### 3. [03_Procedural_vs_OO_Design_and_Simulation.md](./07_Predicted_Questions_and_Solutions/03_Procedural_vs_OO_Design_and_Simulation.md)
* **Q1:** Converting Procedural Execution to OO Simulation (Refactoring an imperative ATM script into Nygaard OO simulation).
* **Q2:** Kristen Nygaard’s Philosophy & Paradigms (Simulation of real-world phenomena vs command execution).
* **Q3:** Grady Booch’s 5 Core Attributes of OOP (Abstraction, Encapsulation, Modularity, Hierarchy, Typing).
* **Q4:** Parnas's Decomposition Criteria: Flowcharting vs Information Hiding.

### 4. [04_Procedure_Activation_and_Stack_Frames.md](./07_Predicted_Questions_and_Solutions/04_Procedure_Activation_and_Stack_Frames.md)
* **Q1:** Recursive Stack Frame Layout (GCD recursion trace and full memory diagram at peak stack depth).
* **Q2:** Anatomy of an Activation Record & Registers (Return address, saved `%rbp`, caller vs callee-saved registers).
* **Q3:** x86-64 Assembly Prologue and Epilogue (`pushq %rbp; movq %rsp, %rbp; subq $16, %rsp; leave; ret`).
* **Q4:** Static Links (Access Links) vs Dynamic Links (Control Links) with nesting diagram and non-local lookup.

### 5. [05_Binding_Times_and_Parameter_Passing.md](./07_Predicted_Questions_and_Solutions/05_Binding_Times_and_Parameter_Passing.md)
* **Q1:** Complete 7-Stage Binding Times Taxonomy (14 programmatic entities classified with explanations).
* **Q2:** Parameter Passing Semantics Comparison Matrix (Value, Pointer, Reference, Value-Result, Name/Thunks).
* **Q3:** The Aliasing Trap (Tracing `Modify(x, x)` under Pass-by-Reference vs Pass-by-Value-Result).
* **Q4:** Deep Binding vs Shallow Binding (Tracing environments when procedures are passed as arguments).

### 6. [06_Language_Philosophy_Bohm_Jacopini_and_CLOS.md](./07_Predicted_Questions_and_Solutions/06_Language_Philosophy_Bohm_Jacopini_and_CLOS.md)
* **Q1:** Dijkstra Purists vs Linguists & Intellectual Manageability.
* **Q2:** Bohm-Jacopini Theorem & Structured Control Flow (Eliminating `break`/`continue` with auxiliary flags).
* **Q3:** Critiques of OOP & The "Gorilla/Banana" Problem (Joe Armstrong critique from `GOODBYE OOP.pdf`).
* **Q4:** Common Lisp Object System (CLOS), MOP, and Method Combination (`:before`, primary, `:after` trace + Lisp code).

### 7. [07_Tricky_Code_Snippets_and_Bug_Traps.md](./07_Predicted_Questions_and_Solutions/07_Tricky_Code_Snippets_and_Bug_Traps.md)
* 15 rapid-fire "What prints? / Spot the Bug" code snippets with explanations (missing virtual destructors, object slicing, dangling local stack addresses, XOR aliasing bugs, struct alignment, and CLOS method execution traces).

---

# Part 2 — Complete Directory & File Inventory

### 1. Root Directory
* [POPL_NAVIGATOR.md](./POPL_NAVIGATOR.md) — Central switchboard and quick lookup guide (this file).
* [POPL_ABSOLUTELY_EVERYTHING_MASTER.md](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md) — The comprehensive 4,233-line textbook covering all 101 sections with full derivations and code.

### 2. `01_Notes_and_Slides/`
* [POPL_exam_notes_midsem](./01_Notes_and_Slides/POPL_exam_notes_midsem) — 16-page compact PDF summarizing slides & notebook with recall boxes.
* [Slides_17Sept2026_popl.pdf](./01_Notes_and_Slides/Slides_17Sept2026_popl.pdf) & [Slides_17Sept2026-TILL ENDSEM...](./01_Notes_and_Slides/Slides_17Sept2026-TILL%20ENDSEM%20%28HALF%20FOR%20MIDSEM%20-%20MOSTLY%20TILL%20PROCEDURE%29.pdf) — Lecture slides with diagrams.
* [POPL_Midsem_Compiled.pdf](./01_Notes_and_Slides/POPL_Midsem_Compiled.pdf) — 38MB full scanned compilation.
* [popl_tb.pdf](./01_Notes_and_Slides/popl_tb.pdf) — Reference textbook.

### 3. `02_Past_Exams_and_Solutions/`
* [PoPL2024MidSem1.pdf](./02_Past_Exams_and_Solutions/PoPL2024MidSem1.pdf) & [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf)
* [PoPL2025MidSem1.pdf](./02_Past_Exams_and_Solutions/PoPL2025MidSem1.pdf) & [midsem-solutions.pdf](./02_Past_Exams_and_Solutions/midsem-solutions.pdf)
* [compre-answer-key.pdf](./02_Past_Exams_and_Solutions/compre-answer-key.pdf) & [PoPL Compre Solutions.pdf](./02_Past_Exams_and_Solutions/PoPL%20Compre%20Solutions.pdf)

### 4. `03_Quizzes/`
* [POPL Eval Quiz.pdf](./03_Quizzes/POPL%20Eval%20Quiz.pdf), [Quiz 1 Review](./03_Quizzes/Quiz%201%20-%20Procedural%20to%20OO%20_%20Corrected%20Quiz_%20Attempt%20review.pdf), [Quiz 2 Review](./03_Quizzes/Quiz%202%20-%20Post-Mid-Sem%20Quiz_%20Attempt%20review.pdf)

### 5. `04_Assignments_and_Tutorials/`
* Assignments 1–3, Tutorials 1–3, [OO Language Comparison Tut.pdf](./04_Assignments_and_Tutorials/OO%20Language%20Comparison%20Tut.pdf), and question banks.

### 6. `05_Readings_and_Articles/`
* [Bjarne Stroustrup On Abstract Classes.pdf](./05_Readings_and_Articles/Bjarne%20Stroustrup%20On%20Abstract%20Classes.pdf)
* [GOODBYE OOP.pdf](./05_Readings_and_Articles/GOODBYE%20OOP.pdf)

### 7. `06_Code_and_Implementations/`
* [AbstractClassModules.zip](./06_Code_and_Implementations/AbstractClassModules.zip), [IndependentModules.zip](./06_Code_and_Implementations/IndependentModules.zip), [dynamicBindingJava.zip](./06_Code_and_Implementations/dynamicBindingJava.zip), [Example Program GCD.pdf](./06_Code_and_Implementations/Example%20Program%20GCD.pdf), Lisp files (`fghc.lisp`, `mergesort-final.lisp`).

---

# Part 3 — Modular Topic-by-Topic Syllabus Guide

## Topic 1: Computational Foundations, Abstraction & Paradigms
* **Key Pointers:** [Master Notes §1: Foundations](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§4: GCD Ladder](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§8: Dijkstra Debate](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§12: Bohm-Jacopini](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md).

## Topic 2: C Modules, Linkage, Types & Encapsulation
* **Key Pointers:** [Master Notes §16: Scope & Storage](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§20: Declaration vs Definition](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§23: Separate Compilation](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§28: Opaque Pointers](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md).

## Topic 3: Procedural to OO Transition & C++ Module Independence
* **Key Pointers:** [Master Notes §36: Procedural vs OO](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§38: Parnas/Booch/Nygaard](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§42: C++ Representation Leak](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§45: Stroustrup Abstract Classes](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md).

## Topic 4: Procedure Activation, Stack Frames & Runtime Environments
* **Key Pointers:** [Master Notes §61: Activation Records](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§65: Stack Frames](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§70: Recursive GCD Call](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§75: Static vs Dynamic Scope](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md).

## Topic 5: Binding Times & Parameter Passing Modes
* **Key Pointers:** [Master Notes §81: Binding Taxonomy](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§85: Parameter Modes](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§88: Aliasing Dangers](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md).

## Topic 6: Reflection, Metaobject Protocols (MOP) & CLOS
* **Key Pointers:** [Master Notes §91: Reflection](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§95: MOP & CLOS](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md), [§98: Method Combination](./POPL_ABSOLUTELY_EVERYTHING_MASTER.md).

---

# Part 4 — Past Exam Question-by-Question Solutions

### 2025 Midsem Paper Breakdown
* **Question 1: C++ Module Coupling & Abstract Classes:** [midsem-solutions.pdf](./02_Past_Exams_and_Solutions/midsem-solutions.pdf) (Page 1) & [Topic 02 Q1/Q2](./07_Predicted_Questions_and_Solutions/02_Cpp_Module_Decoupling_and_Abstract_Classes.md).
* **Question 2: Procedural to OO Design:** [midsem-solutions.pdf](./02_Past_Exams_and_Solutions/midsem-solutions.pdf) (Page 2) & [Topic 03 Q1](./07_Predicted_Questions_and_Solutions/03_Procedural_vs_OO_Design_and_Simulation.md).
* **Question 3: Parameter Passing & Semantics:** [midsem-solutions.pdf](./02_Past_Exams_and_Solutions/midsem-solutions.pdf) (Page 3) & [Topic 05 Q2/Q3](./07_Predicted_Questions_and_Solutions/05_Binding_Times_and_Parameter_Passing.md).

### 2024 Midsem Paper Breakdown
* **Question 1: C Separate Compilation & Global Names:** [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf) (Page 1) & [Topic 01 Q1/Q2](./07_Predicted_Questions_and_Solutions/01_C_Modules_Linkage_and_Storage.md).
* **Question 2: Complete Binding Times Table:** [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf) (Page 2) & [Topic 05 Q1](./07_Predicted_Questions_and_Solutions/05_Binding_Times_and_Parameter_Passing.md).
* **Question 3: Stack Frame Layout & Recursion:** [PoPL 2024 MidSem Solution.pdf](./02_Past_Exams_and_Solutions/PoPL%202024%20MidSem%20Solution.pdf) (Page 3) & [Topic 04 Q1](./07_Predicted_Questions_and_Solutions/04_Procedure_Activation_and_Stack_Frames.md).

---

# Part 5 — Ctrl+F Fast Search Keywords

| What you are looking for | Exact Ctrl+F string to search |
| :--- | :--- |
| Stroustrup abstract class pattern | `making modules independent` OR `pure virtual` |
| Linker errors & multiple definitions | `leap of faith` OR `multiple definition` |
| Opaque pointer & `FILE *` handle | `opaque pointer` OR `FILE as an Abstract Data Type` |
| Stack frame assembly (`%rbp`, `%rsp`) | `function prologue and epilogue` OR `pushq %rbp` |
| Recursive GCD assembly trace | `recursive gcd call` OR `gcd ladder` |
| Binding times taxonomy | `taxonomy of binding times` OR `time of binding` |
| Pass by value vs reference vs pointer | `pass by reference vs passing a pointer` |
| Dijkstra vs Linguists / Purists | `dijkstra, purists, linguists` |
| Bohm-Jacopini structured theorem | `bohm-jacopini` |
| Joe Armstrong / OOP critique | `banana monkey jungle` OR `goodbye oop` |
| CLOS method combination rules | `standard clos method combination` OR `eql specializers` |
| Introspection vs Intercession | `introspection and intercession` |
| Static vs Dynamic chain (scoping) | `lexical (static) vs dynamic scoping` OR `access link` |
| Parnas information hiding | `parnas, booch, and nygaard` |
