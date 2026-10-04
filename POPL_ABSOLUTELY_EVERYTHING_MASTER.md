# POPL / Principles of Programming Languages --- Complete Exam Notes

### BITS Pilani Goa Campus --- CS F301

### Exam: 5 October 2026, 9:30 AM

> **Purpose:** This is a consolidated record of the POPL exam-prep
> conversation: syllabus boundaries, professor-slide content,
> explanations we developed, exam-safe wording, common mistakes,
> quick-answer formulations, examples, and the tutorial/slide questions
> worth revisiting.
>
> **Source priority:** Professor's merged slides / course material and
> the explanations we derived from them. Where the slides only *mention*
> a topic without explaining it, this document does **not** invent a
> professor-specific explanation.

------------------------------------------------------------------------

# 0. EXAM SCOPE --- READ THIS FIRST

## Professor's syllabus boundary

The syllabus notice we discussed says:

-   The syllabus consists of the **first four topics from the handout**,
    with the relevant cited material.
-   **Exclude the Python nugget in Topic 4.**
-   Anything done in **LT1**, practiced in tutorials/assignments, and
    shared on Moodle during the semester is in scope.
-   Nothing beyond that course boundary is automatically in scope.
-   Slides updated by **EoD 2 October 2026** are in scope.
-   The update was expected to add very little; the possible new
    material we identified was around **dynamic binding**.
-   Main languages emphasized: **C/C++ and Java**.
-   TB1: **Scott, Programming Language Pragmatics, 4th ed.**
-   Ref1: **Ravi Sethi, Programming Languages: Concepts & Constructs** /
    PLCC material.
-   The professor's slides themselves are the most important source for
    wording and emphasis.

## Exam logistics / policy we discussed

The exam is **open book and open laptop**, but the professor's notice
requires devices to be **disconnected from all networks**.

The notice says networking attempts can include things such as:

-   making or continuing network connections,
-   tethering,
-   wireless/Bluetooth networking,
-   extensive searches,
-   typing documents/programs in ways that constitute networking
    attempts.

Therefore:

-   Offline PDFs/notes/books are the safe study/reference material.
-   Do **not** assume that an offline AI assistant is authorized merely
    because it technically does not use the Internet. The notice did not
    explicitly authorize local LLM-generated answers.
-   For the exam, follow the professor's explicit rules rather than
    relying on a technical interpretation.

------------------------------------------------------------------------

# 1. HOW WE APPROACHED THIS COURSE

The important thing about this professor's material is that it is **not
just a conventional "learn definitions" PL course**.

A recurring theme is:

> **What are we trying to express, what does the language abstract away,
> and what does the underlying implementation force us to expose?**

The course repeatedly moves between:

1.  computation and abstraction,
2.  formal language vs programming language,
3.  imperative programming,
4.  structured control flow,
5.  modularity and information hiding,
6.  procedural programming,
7.  object orientation,
8.  procedure activation / runtime implementation,
9.  C++ vs Java,
10. metaprogramming / MOP / reflection.

A good exam answer should usually follow the **professor's conceptual
chain**, not merely give a generic textbook definition.

------------------------------------------------------------------------

# 2. PRELIMINARIES --- COMPUTATION AND PROGRAMMING LANGUAGES

## 2.1 Computation from simple primitives

A central introductory idea:

-   Computation can be built from a relatively small number of primitive
    operations.
-   Those elementary operations can be composed into larger operations.
-   Larger operations can themselves be composed recursively.
-   Thus, from a small foundation, we obtain arbitrarily complex
    programs.

### Mental model

``` text
few elementary operations
        ↓
combine them
        ↓
small composite operations
        ↓
combine composites
        ↓
large programs
```

The important point is that programming-language abstraction lets us
stop thinking about every machine-level detail.

------------------------------------------------------------------------

# 3. LANGUAGES AND ABSTRACTION LEVELS

## 3.1 What is a higher-level language?

The slides frame language "height" in terms of:

> **How close the language is to the human level / how expressive it
> is.**

A higher-level language should allow the programmer to express
**intention** while minimizing the burden of explicitly specifying
**HOW** that intention is implemented.

### Core exam phrasing

> A higher-level language should let the programmer state what is
> intended while abstracting away unnecessary implementation details.

------------------------------------------------------------------------

# 4. FORMAL LANGUAGE VS PROGRAMMING LANGUAGE

## 4.1 Formal language

A formal language is fundamentally a mathematically defined set of valid
strings / structures over some alphabet.

For programming languages:

-   syntax determines which programs are valid;
-   the set of syntactically valid programs forms a formal language.

But:

> **A programming language is more than a formal language.**

Why?

Because a useful programming language also needs **semantics**:

-   what valid programs mean,
-   what computation they cause,
-   how their constructs behave.

### Exam-safe distinction

``` text
Formal language → which strings/programs are valid
Programming language → syntax + meaning/semantics of those programs
```

Do not answer that a programming language is "just a formal language."

------------------------------------------------------------------------

# 5. ABSTRACTION

## 5.1 What abstraction means here

Abstraction means exposing the useful conceptual interface while hiding
unnecessary implementation detail.

The recurring pattern is:

``` text
WHAT I WANT
    ↓
abstraction/interface
    ↓
HOW IT IS IMPLEMENTED
```

Examples discussed in the slides:

-   library function + signature,
-   class,
-   real numbers vs floating-point representation,
-   subroutines/functions/methods.

### Scott-style framing

Higher-level languages:

-   abstract away details of the machine,
-   let us name code by its **purpose/role**,
-   hide the exact mechanism used to achieve that purpose.

------------------------------------------------------------------------

# 6. CONTROL ABSTRACTIONS AND DATA ABSTRACTIONS

## 6.1 Control abstraction

Functions / procedures / methods are examples of **control
abstractions**.

Instead of repeatedly writing the entire sequence of operations:

``` text
do operation A
do operation B
do operation C
...
```

we give the sequence a name and invoke it.

## 6.2 Data abstraction

Classes and abstract data structures are examples of **data
abstractions**.

The user works with:

``` text
WHAT data represents
WHAT operations are available
```

without necessarily needing to know:

``` text
HOW the data is stored
HOW operations are implemented
```

------------------------------------------------------------------------

# 7. PROGRAMMABILITY

The slides emphasize three essentials for programming arbitrary
computation.

## 7.1 Elementary operations

We need primitive operations from which more complex computations can be
built.

## 7.2 Sequencing

We need to specify a sequence of operations.

Important:

> Sequencing is about **order**, not timing.

It means:

``` text
A then B then C
```

not:

``` text
A at 2 seconds
B at 5 seconds
```

## 7.3 Repetition

We need repetition / cycling.

This can be expressed through:

-   iteration,
-   recursion.

### Exam answer

> Programmability requires elementary operations, sequencing, and
> repetition. Repetition can be obtained through iteration or recursion.

------------------------------------------------------------------------

# 8. STATIC VS DYNAMIC

A useful distinction from the introductory slides:

-   **Static**: the program / description / source representation.
-   **Dynamic**: the actual computation/execution.

The same command can potentially behave differently depending on the
**environment** in which it executes.

### Mental model

``` text
program = static description
execution = dynamic behavior
```

### Exam trap

Do not simply say:

> static = compile time, dynamic = run time.

That is often too crude for the professor's conceptual framing.

The central distinction is **description vs computation/behavior**.

------------------------------------------------------------------------

# 9. PROGRAMMING LANGUAGE DESIGN

The course repeatedly asks what makes a language useful.

A good language should:

-   express programmer intent,
-   provide useful abstractions,
-   avoid unnecessary implementation burden,
-   support composition,
-   provide suitable control/data abstractions,
-   balance expressive power and implementation practicality.

------------------------------------------------------------------------

# 10. IMPERATIVE PARADIGM

## 10.1 Imperative programming

The professor's framing:

> Imperative programming describes computation through actions that
> change state.

An imperative program is fundamentally concerned with:

``` text
current state
   ↓
action
   ↓
new state
   ↓
action
   ↓
new state
```

The key idea is **state transformation**.

### Important correction we made

Imperative programming is **not identical to structured programming**.

-   Imperative programming is the broader paradigm.
-   Structured programming is a way of organizing imperative control
    flow.

------------------------------------------------------------------------

# 11. STRUCTURED PROGRAMMING / SYNTAX-DIRECTED CONTROL FLOW

## 11.1 Böhm--Jacopini

The classic result:

> Sequencing, selection, and iteration are sufficient to express any
> computable function.

The three fundamental control structures are:

1.  **Sequencing**
2.  **Selection**
3.  **Iteration**

Examples:

``` text
sequence:
A;
B;
C;

selection:
if (condition)
    A;
else
    B;

iteration:
while (condition)
    A;
```

------------------------------------------------------------------------

# 12. SYNTAX-DIRECTED CONTROL FLOW

The professor's slides emphasize that program control flow can be
determined by the **structure/syntax and interrelationships of
constructs**.

This does NOT mean merely "read the program from left to right."

Blocks and loops give structured entry/exit behavior.

Typical structured constructs:

-   blocks,
-   loops,
-   functions/methods.

A function/method has:

-   a single entry point,
-   potentially multiple exits/returns.

### Exam-safe idea

> In syntax-directed/structured control flow, the syntactic structure of
> the program determines how control branches, loops, and returns rather
> than relying on arbitrary jumps.

------------------------------------------------------------------------

# 13. BOTTOM-UP COMPOSITION

Bottom-up construction:

1.  start with elementary operations,
2.  group them into composite operations,
3.  compose composites into larger composites,
4.  continue recursively.

At the lowest level, execution follows predetermined
sequences/repetitions.

### Mental model

``` text
primitive
  ↓
small function
  ↓
larger function
  ↓
subsystem
  ↓
program
```

------------------------------------------------------------------------

# 14. TOP-DOWN DESIGN

Top-down design starts from the highest-level desired operation and
progressively elaborates it.

Example:

``` text
solve entire problem
    ↓
major subproblems
    ↓
smaller operations
    ↓
elementary operations
```

The slides use examples such as:

-   `printf` ultimately representing a data-transfer operation,
-   a `for` loop being representable through lower-level control
    constructs such as labels/goto.

The key point:

> Top-down design specifies the high-level intention first and
> progressively determines the lower-level mechanisms.

------------------------------------------------------------------------

# 15. SCOPE

Definition we used:

> **Scope is the contextual/textual extent in the source program in
> which a name can be used and refers to an object associated with that
> context.**

Important distinction:

-   a **name** is the identifier,
-   its **scope** is where that name has a particular meaning/binding.

------------------------------------------------------------------------

# 16. DATA TYPES

The slides treat a type as a **set of values**.

A type determines which values belong to the type and what
operations/interpretations are associated with them.

The slides deliberately ask conceptual questions such as:

-   Do naturals "have all properties of reals + extras"?
-   If a Java method expects a natural-number-like value, can a complex
    number be supplied?

The purpose is to make you reason about:

-   sets of values,
-   type compatibility,
-   whether one type can stand in for another.

### Do not blindly memorize "subtype = more values"

The slides are provoking a deeper question about **properties and
substitutability**, not just set inclusion.

------------------------------------------------------------------------

# 17. MODULAR PROGRAMMING

## 17.1 What modularity tries to achieve

A module should allow part of a system to be developed/understood
independently.

The slides list Parnas's benefits:

1.  **Managerial** --- development can be divided among groups with less
    communication.
2.  **Product flexibility** --- drastic changes to one module should not
    require changes to others.
3.  **Comprehensibility** --- study the system one module at a time.

Very important:

> A module is about **responsibility delegation**, not merely division
> of labour.

------------------------------------------------------------------------

# 18. MODULARIZATION IS A SYSTEM-LEVEL DESIGN DECISION

The slides stress that modularization happens at the **top/system
level**.

It is a design decision made before the detailed implementation of the
individual modules.

So:

``` text
system-level decomposition
        ↓
decide responsibilities / boundaries
        ↓
detail individual modules
```

Not merely:

> "split a giant function into smaller functions."

------------------------------------------------------------------------

# 19. INFORMATION HIDING --- PARNAS

## 19.1 Conventional modularization vs information hiding

Parnas's example compares two decompositions.

### Conventional modularization

The system is divided into modules corresponding roughly to processing
steps.

### Information-hiding modularization

Modules are characterized by **design decisions they know and hide**.

The interface is deliberately chosen to reveal as little as possible
about the inner workings.

This is a key conceptual transition toward OO decomposition.

### Exam-safe sentence

> Parnas's information-hiding criterion says that a module should
> encapsulate knowledge of a design decision and expose an interface
> that reveals as little as possible about that decision's
> implementation.

------------------------------------------------------------------------

# 20. INFORMATION HIDING ≠ JUST MODULARITY

This distinction mattered repeatedly in our quizzes.

Do NOT say:

> "Information hiding just means splitting work between modules."

Instead:

``` text
modularity
    → separation / responsibility
information hiding
    → hide design decisions + minimize exposed implementation
```

Information hiding adds **specialization and independence**.

------------------------------------------------------------------------

# 21. PROCEDURAL → OBJECT ORIENTATION

This is one of the most important conceptual sections.

## 21.1 WHAT vs HOW

We want names that describe **what is programmable**, rather than names
tied to the implementation process.

### SortedList example

Suppose we need a sorted list.

A useful abstraction is:

``` text
SortedList
```

It tells us what the object/data structure is.

The implementation could use:

-   an array,
-   some other storage structure,
-   insertion sort,
-   another sorting strategy.

Those details are **HOW**.

The abstraction only needs operations such as:

``` text
put element
remove element
get nth element
```

while preserving the sorted property.

------------------------------------------------------------------------

# 22. WHY `QuickSort` IS DIFFERENT

`QuickSort` is named after **how the sorting process works**:

-   choose a pivot,
-   partition recursively,
-   continue until partitions are small.

The professor's point:

> A procedure's process, once programmed, is not further "programmable"
> in the same sense as a data abstraction whose implementation can vary.

Thus:

``` text
SortedList → WHAT
QuickSort  → HOW
```

### Exam answer

> `SortedList` is a useful abstraction because its identity is the
> property/behavior users need, independent of storage and sorting
> implementation. `QuickSort` names a particular algorithm/process and
> therefore exposes HOW rather than the higher-level WHAT.

------------------------------------------------------------------------

# 23. WHY ENCAPSULATION?

The slides ask:

> If a procedure's identity is its algorithm, while a data structure's
> identity does not depend on the algorithms that maintain it, why
> encapsulate data and operations together?

The answer develops through **information hiding**.

If the representation and operations can be treated as one abstraction
with a stable interface, then clients need not depend on implementation
details.

------------------------------------------------------------------------

# 24. BOOCH --- ENCAPSULATION

Booch's definition from the slides:

> Encapsulation is the process of compartmentalizing the elements of an
> abstraction that constitute its structure and behavior; encapsulation
> separates the contractual interface of an abstraction from its
> implementation.

### Very important correction from our quiz

Do **NOT** say:

> "Encapsulation means hiding methods."

That is inaccurate.

A method can be public and still be part of the abstraction's
contractual interface.

The thing being hidden is the **implementation/detail of structure and
behavior**.

### Exam-safe

> Encapsulation separates the public/contractual interface of an
> abstraction from its implementation.

------------------------------------------------------------------------

# 25. PARNAS / BOOCH / NYGAARD --- MEMORIZE THIS

This was explicitly confusing initially, so keep this table.

  -----------------------------------------------------------------------
  Person                  Main idea               Memory hook
  ----------------------- ----------------------- -----------------------
  **Parnas**              Information hiding /    **Hide design
                          modularization          decisions**

  **Booch**               Encapsulation           **Interface vs
                                                  implementation**

  **Nygaard**             Object orientation /    **Evolving interacting
                          Simula                  objects**
  -----------------------------------------------------------------------

### One-line chain

``` text
Parnas → information hiding
Booch → encapsulation
Nygaard → OO
```

------------------------------------------------------------------------

# 26. "MORE THAN JUST A DIVISION OF LABOUR"

The slides say OO involves more than modular separation.

Following Parnas:

> Object orientation involves **information hiding**, meaning not merely
> separation for modularity but also **specialisation and
> independence**.

This is why the OO discussion is not simply:

> "Put related functions together."

The deeper issue is:

-   what the object knows,
-   what it hides,
-   what it is responsible for,
-   how independent it is from other modules.

------------------------------------------------------------------------

# 27. OBJECT INDEPENDENCE

Object independence is the stronger goal behind the information-hiding
discussion.

A useful exam formulation:

> An object should encapsulate its own structure and behavior so that
> other modules do not need to depend on its internal representation.

### Our earlier correction

You previously described it roughly as:

> "the object itself is responsible for itself --- data, actions,
> anything."

That is a decent intuition, but the exam-safe version is:

> **Object independence means that an object's structure and behavior
> are encapsulated so other modules can use its abstraction without
> depending on its implementation.**

------------------------------------------------------------------------

# 28. PROCEDURAL VS OO --- THE CORE DIFFERENCE

## Procedural

The program gives a:

> **complete step-by-step breakdown of the execution of the full
> solution.**

It may be modularized, but ultimately the computation is assembled as a
sequential program.

### Mental model

``` text
problem
 ↓
steps
 ↓
step 1
 ↓
step 2
 ↓
step 3
 ↓
...
```

## Object-oriented

The information process is regarded as:

> a system evolving through transformations of its state.

The system is organized into objects.

Objects have:

-   computational properties,
-   state,
-   behavior/actions.

State transformations are regarded as **actions by objects**.

### Mental model

``` text
object A ↔ object B ↔ object C
   ↓          ↓          ↓
 state      state      state
 changes    changes    changes
```

------------------------------------------------------------------------

# 29. "SIMULATION MEANS OO PROGRAMMING"

The slides paraphrase Nygaard:

> An information process is regarded as a system evolving through
> transformations of state, composed of independently interacting
> evolving objects.

This comes from Simula's origins as a simulation language.

### Exam-safe interpretation

OO naturally models a system as:

-   entities/objects,
-   each with state,
-   each capable of actions,
-   interacting to evolve the system.

Do not reduce OO to:

> "classes + inheritance + polymorphism."

Those are mechanisms/features. The professor's conceptual definition is
deeper.

------------------------------------------------------------------------

# 30. C++: "OO ON TOP OF PROCEDURAL IS FRAUGHT"

This is a **high-value professor-specific point**.

## 30.1 The problem

In C++:

-   a class/type declaration must be fully available to modules that use
    it,
-   this includes private members,
-   the storage structure/layout must be known,
-   keeping the structure consistent across modules is not
    directly/enforceably guaranteed by C++ syntax.

So implementation details can leak across module boundaries.

------------------------------------------------------------------------

# 31. DO NOT SAY "C++ HAS NO ENCAPSULATION"

This was one of our biggest corrections.

C++ **does have encapsulation**.

The professor's criticism is more specific:

> C++'s underlying procedural/module model can require type and
> storage-layout information to be available across modules, weakening
> information hiding and object independence.

### Bad exam answer

> "C++ doesn't have encapsulation."

### Better

> "C++ provides encapsulation, but because its procedural module/type
> system requires class/type and storage-layout information to be
> available across module boundaries, implementation details can leak
> and object independence is weakened."

------------------------------------------------------------------------

# 32. PARTIALLY HIDDEN CLASS

The slides construct a hypothetical situation:

``` text
Class Module          User Module
------------          -----------
methods               independently developed code
implementation        uses same named class
```

The user module is developed independently without regard to the
implementation.

The question:

> Does this really work?

To answer, understand procedure activation, object layout, and static
typing.

------------------------------------------------------------------------

# 33. C++ METHOD CALLS AND `this`

The professor's slides make a specific implementation point:

> A class method call is fundamentally similar to a call to a global
> function, except that `this` is an implicit/default first parameter.

So conceptually:

``` cpp
obj.f(x);
```

is similar to:

``` text
f(&/with obj as implicit this, x)
```

The exact low-level representation can vary, but the professor's
conceptual point is about procedure activation and the implicit object
argument.

------------------------------------------------------------------------

# 34. PROCEDURE ACTIVATION RECORDS IN C++

Each call uses an activation record / stack frame.

For a method:

-   parameters are passed,
-   the implicit `this` object is involved,
-   local data is allocated,
-   return information is maintained.

The slide says that in call-by-value situations the object's storage
structure/data members are involved in the representation.

This is why **object layout matters**.

------------------------------------------------------------------------

# 35. WHY OBJECT LAYOUT MATTERS

If a function/module needs to allocate or pass an object, it needs to
know:

-   size,
-   layout,
-   positions of members.

In imperative languages, this information is generally available
statically at compile time.

Thus:

``` text
class declaration
      ↓
size/layout known
      ↓
compiler generates machine-level offsets/storage
```

This is one reason C++ class representation can leak across modules.

------------------------------------------------------------------------

# 36. STATIC TYPING ACROSS C/C++ MODULES

For C/C++-style modular procedural languages:

-   each module has its own static type information,
-   the compiler converts symbolic information into quantitative
    machine-level information,
-   after compilation, much of the symbolic type information is gone.

The linker generally does not know about:

-   ordinary type names,
-   local variable names,
-   parameter names,
-   data-member names.

It does know about relevant symbols such as:

-   global variables,
-   static variables,
-   global function names.

C++ method names are also **mangled**, with class information
incorporated into generated names.

------------------------------------------------------------------------

# 37. THE "TIME BOMB" / INCONGRUENT DECLARATIONS

Suppose two modules use different declarations for what they believe is
the same class.

Because:

-   each module compiles using its own static type information,
-   much type/layout information disappears before linking,
-   the linker does not have enough information to detect every
    incongruity,

the two modules can potentially:

1.  compile,
2.  link,
3.  start running,

while still being semantically inconsistent.

Possible result:

-   semantic errors,
-   runtime exceptions,
-   crashes.

### Exam-safe causal chain

``` text
C++ module independence
        ↓
each module compiles using local type/layout info
        ↓
symbolic type info largely disappears
        ↓
linker cannot detect every representation mismatch
        ↓
incongruent modules can compile/link
        ↓
semantic/runtime failure
```

------------------------------------------------------------------------

# 38. THE C++ LESSON

The slide's conclusion:

> Objects are not the principal components of a C/C++ program.

The reason is the underlying program organization:

-   modules,
-   global functions,
-   class/type definitions.

The object is not necessarily the fundamental independently interacting
execution component in the strong OO sense used by the professor.

------------------------------------------------------------------------

# 39. JAVA VS C++

This was another frequently tested comparison.

## Java

The slides emphasize:

-   no global functions,
-   type linkages are global,
-   type information needed for bindings remains available throughout
    execution,
-   except for specifically local types within classes.

The slides characterize Java as **purely OO** in this context.

## C++

C++:

-   retains the C/procedural program structure,
-   has global functions/modules,
-   requires more representation information across module boundaries,
-   therefore weakens the stronger OO notion of independent objects.

### Exam-safe comparison

> Java's execution/type model keeps the type information needed for
> bindings globally available and has no global functions, whereas C++
> retains a modular procedural foundation in which type/storage-layout
> information can be exposed across module boundaries.

------------------------------------------------------------------------

# 40. JAVA --- WHAT NOT TO SAY

You already know basic Java OOP from your OOP course, so do not waste
exam time explaining:

-   inheritance basics,
-   classes and objects 101,
-   constructors,
-   overriding definitions,

unless a question explicitly asks.

For this professor, focus on:

-   no global functions,
-   global type linkages,
-   binding information at execution,
-   contrast with C++,
-   relation to object independence.

------------------------------------------------------------------------

# 41. PROCEDURE ACTIVATION --- HIGH IMPORTANCE

This section is implementation-heavy and was explicitly tested in our
mini-quiz.

## 41.1 Program lifetime

The slides distinguish:

1.  Development
2.  Loading
3.  Execution

Then finer distinctions:

1.  Compile time
2.  Linking time
3.  Loading time
4.  Procedure activation time
5.  Run time

------------------------------------------------------------------------

# 42. COMPILE / LINK / LOAD / ACTIVATION / RUN

## Compile time

Translate the programmer's source-level intention into a lower-level
representation.

## Linking time

Combine the translated program with the standard environment/support and
other separately translated pieces.

## Loading time

Prepare the program for execution, including dynamic parts of linking
where relevant.

## Procedure activation time

Each time a callable piece of code is executed from a particular entry
point.

## Run time

Execution of atomic imperative machine-level pieces.

------------------------------------------------------------------------

# 43. PROCEDURE VS ACTIVATION VS ACTIVATION RECORD

This distinction is crucial.

## Procedure

The **static code**.

Example:

``` c
int gcd(int a, int b) { ... }
```

There is one procedure definition.

## Procedure activation

One **particular call/execution** of that procedure.

If `gcd()` is called seven times:

``` text
1 procedure
7 activations
```

## Activation record

The runtime storage/information associated with one activation.

Usually contains information such as:

-   parameters,
-   local variables,
-   temporaries,
-   saved state,
-   return information,
-   relevant links.

Thus:

``` text
Procedure = static code
Activation = one call/execution
Activation record = runtime data for that call
```

------------------------------------------------------------------------

# 44. RECURSION AND ACTIVATION RECORDS

If a procedure calls itself:

``` text
gcd(...)
  ↓
gcd(...)
    ↓
gcd(...)
```

each call is a **new activation**.

Therefore recursion requires multiple activation records, normally
stacked.

### Mini-quiz answer

Question:

> Why does a recursive call create a new activation?

Answer:

> Because each call is a distinct execution of the procedure and
> therefore needs its own runtime state/activation record for its
> parameters, locals, temporaries, and return state.

------------------------------------------------------------------------

# 45. SIX FEATURES OF A SUBROUTINE

Memorize these six.

## 1. Name and signature/prototype

Defines how the subroutine is identified and called.

## 2. Parameter passing and access

Preparation for the call and subroutine preamble.

## 3. Allocation/access to local and non-local objects

Includes:

-   local variables,
-   temporaries,
-   access to non-local objects.

## 4. Imperative action procedure

Translation/execution of the actual source-code body.

## 5. Preparation to return

Includes:

-   return values,
-   cleanup,
-   winding-up operations,
-   e.g. C++ destructor calls for local objects created during
    activation.

## 6. Restoration of program state

Restore the caller's state and return appropriately.

### Memorize as

``` text
Name
Parameters
Locals/nonlocals
Body
Return
Restore
```

------------------------------------------------------------------------

# 46. RECURSIVE `gcd` EXAMPLE

The slide uses:

``` c
int gcd(int parama, int paramb) {
    static int xstatic = 0;

    xstatic = parama - paramb;

    if (parama > 0 && paramb > 0) {
        if (xstatic < 0)
            return gcd(parama, -xstatic);
        else if (xstatic > 0)
            return gcd(paramb, xstatic);
        else
            return parama;
    } else
        return -1;
}
```

The example is used to connect:

-   parameters,
-   local/static data,
-   recursive calls,
-   return values,
-   activation records,
-   assembly.

------------------------------------------------------------------------

# 47. STATIC VARIABLE VS ACTIVATION DATA

In the `gcd` example:

``` c
static int xstatic = 0;
```

is **static storage**.

It is not a fresh independent variable created inside every activation
in the same way ordinary local activation data is.

This is important when reasoning about stack frames.

------------------------------------------------------------------------

# 48. STACK FRAME / ACTIVATION RECORD

A simplified call sequence:

``` text
caller
  |
  | push/pass incoming parameters
  v
call
  |
  | save return state
  v
callee stack frame
  |
  | allocate locals/temporaries
  | execute body
  |
  | maybe make another call
  v
recursive callee
```

When returning:

``` text
callee finishes
    ↓
restore saved state
    ↓
return value
    ↓
caller resumes
```

------------------------------------------------------------------------

# 49. ACCESS LINK / SAVED STATE

The stack-frame slides show:

-   incoming parameters,
-   call saving state,
-   return address,
-   saved caller/frame information,
-   local variables and temporary storage,
-   outgoing parameters for recursive calls,
-   `leave`,
-   `ret`.

The exact assembly syntax matters less than understanding the **runtime
structure**.

------------------------------------------------------------------------

# 50. FRAME POINTER

A frame pointer gives a stable reference to the current activation
record.

Conceptually:

``` text
frame pointer
    ↓
[parameters]
[saved state]
[locals]
[temporaries]
```

The caller's frame information is saved as part of maintaining the call
chain.

The slides refer to the saved caller frame information as an **access
link** in this context.

------------------------------------------------------------------------

# 51. RETURN VALUES

When a recursive call returns:

-   the result is placed where the calling convention expects it,
-   the caller resumes,
-   the returned value can be propagated upward.

Do not get lost in individual assembly instructions unless the exam
explicitly asks.

The conceptual chain is:

``` text
call
→ create activation
→ execute
→ possibly call again
→ receive return value
→ restore state
→ return
```

------------------------------------------------------------------------

# 52. `this` IS PART OF PROCEDURE ACTIVATION

For a C++/Java method:

``` text
method(obj, explicit arguments...)
```

conceptually includes the current object as an implicit argument.

Thus `this` participates in the activation.

This connects the OO section directly to the procedure activation
section.

------------------------------------------------------------------------

# 53. THE "NAME-CALLING" SLIDE

The professor makes a historical/conceptual point:

-   early programming abstractions included macros,
-   functions with parameters and return values provided much stronger
    expressive power,
-   recursion and composition are extremely powerful,
-   instead of making fine distinctions among callable entities, the
    slides call all "callables" **subroutines**,
-   a call is called a **Procedure Activation**.

This terminology is from the Sethi-based treatment.

------------------------------------------------------------------------

# 54. WHEN TO USE JAVA

The slides ask provocative questions.

Java is presented as useful especially:

-   for **interfacing**,
-   when development should avoid low-level implementation details,
-   when the focus is **core business logic** rather than implementation
    mechanics,
-   when requirements lead to choices of libraries/class hierarchies.

"Interfacing" is not restricted to GUI/UI; it is about developing
without dealing with all the nitty-gritty.

The slide also asks:

> Wouldn't LLMs replace Java programmers?

This is a discussion/provocation rather than a conventional technical
definition.

------------------------------------------------------------------------

# 55. WHEN NOT TO JAVA

The slides list:

-   **Banana Monkey Jungle Problem**
-   problem with the **contain-and-delegate** solution
-   whether **reusability** is such a holy/useful thing in the LLM era
-   **Polymorphism, Contracts and Flexibility = tension-triad**

### IMPORTANT

The slides we reviewed **list these topics but do not give enough
detailed explanation to safely reconstruct the professor's exact
intended argument**.

Therefore:

-   know that these are named syllabus points,
-   review the actual slide/class explanation if available,
-   do not invent a detailed professor-specific answer from generic Java
    knowledge.

------------------------------------------------------------------------

# 56. GENERICS SECTION --- IMPORTANT CORRECTION

The "Generics" section of this slide deck is **not primarily ordinary
Java generics**.

It moves into:

-   MetaObject Protocol (MOP),
-   CLOS,
-   reflection,
-   method combinations,
-   metaprogramming,
-   expressive power vs performance.

Do not spend revision time treating this as a standard Java `<T>`
generics chapter.

------------------------------------------------------------------------

# 57. METAOBJECT PROTOCOL (MOP)

## Definition

A MetaObject Protocol gives users interfaces to the language that allow
them to:

-   modify language behavior,
-   modify aspects of implementation,
-   write programs using those mechanisms.

This blurs the distinction between:

``` text
language designer
        ↕
language user
```

The user gets control over mechanisms that are normally part of language
implementation.

------------------------------------------------------------------------

# 58. WHY MOP?

The motivation in the slides is to reconcile:

-   elegance / expressive power of very high-level languages,
-   with industrial efficiency/control associated with languages such as
    C/C++.

Examples named:

-   Scheme,
-   Prolog,
-   ML,
-   C/C++.

The general tension:

``` text
expressiveness
      ↕
implementation control / efficiency
```

MOP tries to give advanced users more control while preserving a
high-level language framework.

------------------------------------------------------------------------

# 59. CLOS

CLOS = Common Lisp Object System.

Features listed in the slides include:

-   class redefinition,
-   method redefinition,
-   forward-referencing superclasses,
-   implicit generic function definitions,
-   user control of method combination,
-   `eql` specializers,
-   slots with class allocation,
-   integrated types/classes.

Do not assume you need to implement CLOS. The important thing is
understanding what these features say about a highly
reflective/extensible object system.

------------------------------------------------------------------------

# 60. CLOS PROGRAM STRUCTURE

The slides mention constructs such as:

``` lisp
defclass
defgeneric
defmethod
```

Execution creates internal representations of these definitions.

Conceptually:

``` text
source definition
      ↓
internal/metaobject representation
      ↓
language machinery
```

------------------------------------------------------------------------

# 61. REFLECTION

Two central ideas:

## Introspection

The program obtains information about **itself** and uses that
information within the program.

Example from the slides:

``` lisp
(find-class <symbol>)
(class-of <object>)
```

These return class metaobjects.

## Intercession

The program goes further and **changes its own behavior**.

### Key distinction

``` text
Introspection = know about yourself
Intercession  = modify yourself
```

------------------------------------------------------------------------

# 62. LANGUAGE REFLECTION COMPARISON

The slide says:

-   Lisp and Smalltalk support both introspection and intercession.
-   Java is basically **introspective only** in the framing of this
    slide.

This is the professor's slide framing; preserve it in exam answers.

------------------------------------------------------------------------

# 63. `find-class` AND `class-of`

### `find-class`

Returns the class metaobject corresponding to a named class.

If the class does not exist:

-   error may be signaled depending on the optional error flag,
-   otherwise `nil` can be returned.

### `class-of`

Returns the class metaobject corresponding to the class of an object.

The key concept is **metaobject**:

> an object representing information about the program's own object/type
> structure.

------------------------------------------------------------------------

# 64. METHOD COMBINATION

Under standard method combination, the slide gives this order:

1.  applicable **before** methods:
    -   most specific → least specific
2.  most specific applicable **primary** method
3.  applicable **after** methods:
    -   least specific → most specific

The values returned by the generic function are the values returned by
the primary method.

### Memorize

``` text
BEFORE:  most → least
PRIMARY: most specific
AFTER:   least → most
RETURN:  primary's result
```

This is a likely easy-detail question.

------------------------------------------------------------------------

# 65. PERFORMANCE VS EXPRESSIVE POWER

A naive MOP implementation can be very inefficient.

The slides say real implementations need to optimize things such as:

-   generic function invocation,
-   slot access.

The optimization idea:

> Compute critical internal results once, save them, and reuse them.

This is called:

## Memoization

``` text
expensive computation
       ↓
compute once
       ↓
save result
       ↓
reuse result
```

------------------------------------------------------------------------

# 66. DYNAMIC BINDING --- POSSIBLE LATE ADDITION

The syllabus update suggested that at most a very small amount of
additional material might have been added, with **dynamic binding**
being the main possibility we identified.

If dynamic binding is asked, distinguish it from static/lexical binding
conceptually.

### Static/lexical binding

The meaning/binding of a name is determined from the program's
textual/static structure.

### Dynamic binding

The binding can depend on the **runtime calling environment / dynamic
chain**.

Do not over-expand this unless the professor's added slides/classes
contain more.

------------------------------------------------------------------------

# 67. IMPORTANT TERMINOLOGY MAP

Use this map to keep concepts separate.

  -----------------------------------------------------------------------
  Term                                Meaning
  ----------------------------------- -----------------------------------
  Formal language                     Set of valid strings/structures

  Programming language                Syntax + semantics/meaning

  Abstraction                         Expose useful concept, hide
                                      irrelevant implementation

  Control abstraction                 Functions/procedures/methods

  Data abstraction                    Classes/data structures

  Scope                               Textual/contextual extent where a
                                      name refers to something

  Imperative                          State-changing computation

  Structured programming              Organizing control using structured
                                      constructs

  Modularity                          Decompose system into
                                      responsibilities

  Information hiding                  Hide design decisions; minimize
                                      interface exposure

  Encapsulation                       Separate contractual interface from
                                      implementation

  Object independence                 Object does not expose/depend on
                                      internal representation

  Procedural                          Step-by-step execution
                                      decomposition

  OO                                  System of evolving/interacting
                                      objects and state transformations

  Procedure                           Static callable code

  Activation                          One execution/call of a procedure

  Activation record                   Runtime storage/state for one
                                      activation

  MOP                                 Meta-level protocol allowing
                                      language behavior/implementation
                                      customization

  Introspection                       Program examines itself

  Intercession                        Program changes its own behavior

  Memoization                         Save results of expensive
                                      computations for reuse
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 68. THE MOST IMPORTANT CONCEPTUAL CHAINS

## Chain A --- Abstraction

``` text
machine details
      ↓
abstract them
      ↓
name purpose
      ↓
programmer expresses WHAT
      ↓
language handles HOW
```

## Chain B --- Modularity → information hiding → OO

``` text
modularity
   ↓
separate responsibilities
   ↓
information hiding
   ↓
hide design decisions
   ↓
specialization + independence
   ↓
OO-style decomposition
```

## Chain C --- C++ problem

``` text
C++ procedural module model
        ↓
type/storage layout available to modules
        ↓
representation information leaks
        ↓
object independence weakened
        ↓
modules can become inconsistent
        ↓
compiler/linker may miss mismatch
        ↓
runtime semantic errors/crashes
```

## Chain D --- Procedure call

``` text
procedure definition
      ↓
call
      ↓
activation
      ↓
activation record / stack frame
      ↓
parameters + locals + state
      ↓
execute body
      ↓
return preparation
      ↓
restore state
      ↓
caller continues
```

## Chain E --- Reflection

``` text
program information about itself
        ↓
introspection

program changes its own behavior
        ↓
intercession
```

------------------------------------------------------------------------

# 69. COMMON EXAM MISTAKES WE ALREADY CORRECTED

## Mistake 1

> "Encapsulation means hiding methods."

### Correction

Encapsulation separates the **contractual interface** from the
**implementation**.

Public methods may be exactly the interface being exposed.

------------------------------------------------------------------------

## Mistake 2

> "C++ is bad because it doesn't have encapsulation."

### Correction

C++ has encapsulation.

The professor's criticism is that the procedural module/type model can
require:

-   class/type declarations,
-   private-member information,
-   storage layout,

to be available across modules.

That weakens information hiding/object independence.

------------------------------------------------------------------------

## Mistake 3

> "Object independence means the object is responsible for itself."

### Correction

That's intuition, but the exam version is:

> The object encapsulates its structure and behavior so clients do not
> depend on its internal representation.

------------------------------------------------------------------------

## Mistake 4

> "Java is better because it has no data leakage."

### Correction

Too vague.

Say:

> Java has no global functions and maintains the type-linkage
> information needed for bindings throughout execution, whereas C++
> retains a procedural/module model in which representation/type-layout
> information can be exposed across modules.

------------------------------------------------------------------------

## Mistake 5

> "Structured programming = imperative programming."

### Correction

Structured programming is a structured way to organize imperative
control flow.

Imperative programming is the broader paradigm based on state-changing
actions.

------------------------------------------------------------------------

## Mistake 6

> "Formal language = programming language."

### Correction

A programming language has syntax **and semantics**. The set of valid
programs is a formal language, but the PL itself is more than the set.

------------------------------------------------------------------------

## Mistake 7

> "Static = compile time, dynamic = runtime."

### Correction

The professor's conceptual static/dynamic distinction is broader:

``` text
static → program/description
dynamic → computation/execution
```

------------------------------------------------------------------------

## Mistake 8

> "Procedure = activation."

### Correction

``` text
procedure = static code
activation = one call/execution
activation record = runtime state for that activation
```

------------------------------------------------------------------------

## Mistake 9

> "One recursive function means one stack frame."

### Correction

Every recursive call creates a distinct activation and therefore a
distinct activation record.

------------------------------------------------------------------------

# 70. QUICK EXAM ANSWERS

These are short answers you can expand in a 5--10 mark question.

## Q: What is abstraction?

> Abstraction exposes the useful conceptual interface while hiding
> implementation details. In a higher-level language it allows the
> programmer to express WHAT is intended without explicitly specifying
> every HOW-level machine detail.

------------------------------------------------------------------------

## Q: Formal language vs programming language?

> A formal language is a mathematically defined set of valid
> strings/structures. A programming language includes such syntactic
> validity but also gives programs semantics, i.e. meaning and
> computational behavior.

------------------------------------------------------------------------

## Q: What are the essentials of programmability?

> Elementary operations, sequencing, and repetition. Repetition may be
> obtained through iteration or recursion.

------------------------------------------------------------------------

## Q: What are the three structured-programming constructs?

> Sequencing, selection, and iteration, following the Böhm--Jacopini
> result.

------------------------------------------------------------------------

## Q: What is scope?

> The textual/contextual extent in which a name can be used and refers
> to an object associated with that context.

------------------------------------------------------------------------

## Q: What is information hiding?

> A module hides knowledge of a design decision and exposes an interface
> revealing as little as possible about the decision's implementation.

------------------------------------------------------------------------

## Q: Parnas vs Booch?

> Parnas gives the information-hiding criterion for modularization.
> Booch describes encapsulation as compartmentalizing structure and
> behavior and separating the contractual interface from implementation.

------------------------------------------------------------------------

## Q: Why is `SortedList` a better abstraction than `QuickSort`?

> `SortedList` describes WHAT is programmable: a list that maintains
> sorted behavior. Its implementation may use different storage and
> sorting mechanisms. `QuickSort` names a particular process/HOW and
> therefore exposes a specific algorithm.

------------------------------------------------------------------------

## Q: What is object independence?

> Object independence means the object's structure and behavior are
> encapsulated so that clients can use its abstraction without depending
> on its implementation details.

------------------------------------------------------------------------

## Q: Procedural vs OO?

> Procedural programming decomposes the execution of a solution into
> step-by-step operations. OO models the information process as a system
> evolving through state transformations, with independently interacting
> objects as system components.

------------------------------------------------------------------------

## Q: Why is C++ "OO on top of procedural" fraught?

> C++ requires class/type and storage-layout information to be available
> to modules using a class. Since its procedural module/type system can
> expose representation information and the linker cannot detect every
> type-layout inconsistency, object independence and information hiding
> are weakened.

------------------------------------------------------------------------

## Q: Why can two incongruent C++ modules compile/link?

> Each module is statically typed independently. The compiler converts
> much type information into machine-level quantitative information, and
> the linker generally does not have the symbolic type/member
> information needed to detect all representation mismatches. Thus the
> program may compile and link but fail semantically at runtime.

------------------------------------------------------------------------

## Q: What is procedure activation?

> One particular execution/call of a callable procedure from a specific
> entry point.

------------------------------------------------------------------------

## Q: Procedure vs activation record?

> The procedure is static code; an activation is one execution of it; an
> activation record is the runtime storage/state associated with that
> execution.

------------------------------------------------------------------------

## Q: Why does recursion need multiple activation records?

> Every recursive call is a distinct activation and requires separate
> runtime state for its parameters, locals, temporaries, and return
> information.

------------------------------------------------------------------------

## Q: Six subroutine features?

> Name/signature; parameter passing/access; local/non-local object
> allocation/access; imperative body; preparation to return; restoration
> of program state.

------------------------------------------------------------------------

## Q: Introspection vs intercession?

> Introspection lets a program acquire and use information about itself.
> Intercession additionally lets the program alter its own behavior.

------------------------------------------------------------------------

## Q: Method combination order?

> Before methods from most specific to least specific, then the most
> specific applicable primary method, then after methods from least
> specific to most specific. The primary method supplies the returned
> value(s).

------------------------------------------------------------------------

## Q: What is memoization?

> Computing an expensive result once, saving it, and reusing it later to
> improve performance.

------------------------------------------------------------------------

# 71. SLIDE-BASED TUTORIAL / EXERCISE QUESTIONS

The slides themselves include questions/exercises. These are worth
knowing because they reveal the professor's style.

## 71.1 Recursion in `main`

Question:

> Can `main` be recursive?

What it tests:

-   functions are callable procedures,
-   recursion is not conceptually limited to a function named
    `fact`/`gcd`,
-   each call creates an activation.

------------------------------------------------------------------------

## 71.2 Reverse command-line arguments

Question:

> Reverse command-line arguments.

Likely tests:

-   arrays/arguments,
-   indexing,
-   iteration,
-   procedural decomposition.

------------------------------------------------------------------------

## 71.3 Mutually recursive functions

Question:

> Consider mutually recursive functions where termination/calls are not
> obvious by inspection.

What it tests:

-   recursion,
-   call graph,
-   dynamic activation,
-   why static source inspection may not reveal the full runtime call
    sequence.

------------------------------------------------------------------------

## 71.4 Implement your own `printf`

This tests abstraction.

A high-level call:

``` c
printf(...)
```

hides substantial lower-level work.

The exercise asks you to think about:

-   parsing,
-   formatting,
-   output,
-   lower-level data transfer.

------------------------------------------------------------------------

## 71.5 Opaque type

Question:

> Implement/use a type whose contents are inaccessible to the user.

This directly connects to:

-   abstraction,
-   information hiding,
-   encapsulation,
-   object independence.

------------------------------------------------------------------------

## 71.6 Generate/print integer with only math operations

Question:

> Generate/print an input integer using only mathematical operations /
> arithmetic expressions, with no assignment.

This tests:

-   expression power,
-   language restrictions,
-   difference between expressions and state-changing assignments.

------------------------------------------------------------------------

## 71.7 Dictionary design exercise

The slides ask for a top-level design of a dictionary guaranteeing:

-   O(log n) insertion,
-   O(log n) removal,
-   O(log n) key-based retrieval,
-   O(log n) kth-rank retrieval,
-   O(n) sorted listing,
-   stability for equal ranks,
-   earliest inserted element for a single key search.

The important lesson for this course section is not just selecting a
data structure. It is:

> **Design the abstraction at the top level based on WHAT guarantees it
> must provide, before deciding the implementation details.**

------------------------------------------------------------------------

# 72. THE DATA-STRUCTURE / ABSTRACTION DESIGN MINDSET

When given a design question, ask:

### Step 1 --- WHAT must the abstraction guarantee?

Example:

``` text
insert
remove
retrieve by key
retrieve kth element
sorted iteration
stability
```

### Step 2 --- What complexity guarantees are required?

``` text
O(log n)
O(n)
...
```

### Step 3 --- Only then ask HOW to implement it.

This is the professor's WHAT/HOW theme.

------------------------------------------------------------------------

# 73. PROCEDURE ACTIVATION MINI-QUIZ --- WHAT YOU ALREADY KNOW

We tested this.

### Question 1

What are the six features?

You knew them.

### Question 2

Why does a recursive call create a new activation?

Your answer:

> "because new call"

Correct intuition.

Refined answer:

> Each call is a distinct execution and therefore needs a separate
> activation record containing its runtime state.

### Question 3

One procedure called seven times?

Answer:

``` text
1 procedure
7 activations
7 activation records
```

------------------------------------------------------------------------

# 74. PERCENTAGE / COVERAGE STATUS

During our preparation, we initially estimated the conceptual coverage
around 65--70%, then worked through the remaining slides.

After going through the actual deck through the final content slide:

> **The content slides were essentially covered end-to-end.**

The remaining work is **revision, consolidation, and exam-style
application**, not discovering an entirely new major section.

The final reference pages are citations, not a new topic.

------------------------------------------------------------------------

# 75. PROFESSOR-SPECIFIC PRIORITY ORDER

If time is limited, prioritize the following.

## Tier 1 --- MUST KNOW

1.  Abstraction
2.  Formal language vs PL
3.  Programmability: elementary operations + sequencing + repetition
4.  Static vs dynamic
5.  Imperative paradigm
6.  Structured programming / Böhm--Jacopini
7.  Scope
8.  Modularity
9.  Information hiding / Parnas
10. WHAT vs HOW / `SortedList`
11. Encapsulation / Booch
12. Procedural vs OO
13. Object independence
14. C++ "OO on top of procedural is fraught"
15. C++ partially hidden class
16. Java vs C++
17. Procedure / activation / activation record
18. Six subroutine features
19. Stack frame basics
20. MOP
21. Reflection
22. Introspection vs intercession
23. Method combination order
24. Memoization

## Tier 2 --- KNOW THE CONCEPT, LESS DETAIL

-   program lifetime stages,
-   top-down vs bottom-up design,
-   `this`,
-   C++ name mangling,
-   CLOS features,
-   `find-class`,
-   `class-of`,
-   Java use cases.

## Tier 3 --- REVIEW ACTUAL SLIDE/CLASS EXPLANATION

-   Banana Monkey Jungle Problem,
-   contain-and-delegate,
-   reusability in the LLM era,
-   polymorphism/contracts/flexibility tension-triad,
-   dynamic binding additions.

These are named in the material, but we did not have enough explicit
slide explanation to reconstruct the professor's exact intended argument
safely.

------------------------------------------------------------------------

# 76. ULTRA-COMPACT LAST-MINUTE SHEET

If you only have 20--30 minutes, memorize this.

``` text
FORMAL LANGUAGE
= set of valid strings/structures

PROGRAMMING LANGUAGE
= syntax + semantics

ABSTRACTION
= expose WHAT, hide unnecessary HOW

PROGRAMMABILITY
= elementary operations + sequencing + repetition

STATIC / DYNAMIC
= program/description vs computation/execution

IMPERATIVE
= state-changing actions

STRUCTURED
= sequence + selection + iteration
Böhm–Jacopini

SCOPE
= contextual/textual extent of a name

MODULARITY
= responsibility decomposition

PARNAS
= information hiding
= hide design decisions
= reveal as little as possible

BOOCH
= encapsulation
= structure + behavior compartmentalized
= contractual interface separated from implementation

NYGAARD
= OO
= evolving system of interacting objects

WHAT vs HOW
SortedList = WHAT
QuickSort = HOW

PROCEDURAL
= step-by-step execution decomposition

OO
= evolving state + interacting independent objects

C++
= encapsulation exists
BUT procedural module model exposes type/storage layout
→ weaker object independence
→ incongruent modules can compile/link
→ runtime semantic errors/crashes

JAVA
= no global functions
= type linkages maintained globally / throughout execution
= stronger OO framing in slides

PROCEDURE
= static code

ACTIVATION
= one call/execution

ACTIVATION RECORD
= runtime state for one activation

RECURSION
= new activation + new frame per call

6 SUBROUTINE FEATURES
1. name/signature
2. parameters
3. locals/nonlocals
4. body
5. return preparation
6. restore state

MOP
= users can modify language behavior/implementation

REFLECTION
introspection = inspect self
intercession = change self

METHOD COMBINATION
before: most → least
primary: most specific
after: least → most

MEMOIZATION
= compute once, save, reuse
```

------------------------------------------------------------------------

# 77. ANSWER-WRITING STRATEGY FOR THIS PROFESSOR

When answering conceptual questions:

## 1. Start with the professor's definition/theme

Example:

> "According to the information-hiding criterion..."

Then explain.

## 2. Give the causal chain

For example:

``` text
C++ module model
→ type/layout exposed
→ object independence weakened
→ linker cannot catch all inconsistencies
→ runtime failure
```

This is much stronger than:

> "C++ is bad OO."

## 3. Use the professor's WHAT/HOW language

Especially for abstraction questions.

## 4. Name the relevant person when appropriate

``` text
Parnas → information hiding
Booch → encapsulation
Nygaard → OO
```

## 5. Don't over-answer with generic textbook material

The exam is designed around the professor's material. If a slide has a
specific conceptual framing, reproduce that framing first.

------------------------------------------------------------------------

# 78. FINAL MEMORY MAP

The entire course section can be mentally compressed into this story:

``` text
COMPUTATION
   ↓
few primitives + composition
   ↓
ABSTRACTION
   ↓
express WHAT, hide HOW
   ↓
PROGRAMMABILITY
   ↓
elementary ops + sequence + repetition
   ↓
IMPERATIVE
   ↓
state-changing actions
   ↓
STRUCTURED PROGRAMMING
   ↓
sequence + selection + iteration
   ↓
MODULARITY
   ↓
responsibility delegation
   ↓
PARNAS
   ↓
INFORMATION HIDING
   ↓
hide design decisions
   ↓
OO
   ↓
BOOCH → ENCAPSULATION
   ↓
interface ≠ implementation
   ↓
NYGAARD
   ↓
objects evolve/interact/state transforms
   ↓
C++
   ↓
procedural foundation leaks representation
   ↓
weaker object independence
   ↓
JAVA
   ↓
stronger OO/type-linkage model
   ↓
PROCEDURE ACTIVATION
   ↓
call → activation → stack frame
   ↓
runtime state
   ↓
MOP
   ↓
language can expose/modify its own mechanisms
   ↓
REFLECTION
   ↓
introspection + intercession
```

------------------------------------------------------------------------

# 79. THE ONE-LINERS TO REMEMBER BEFORE WALKING INTO THE EXAM

1.  **Abstraction:** WHAT, not unnecessary HOW.
2.  **Formal language:** valid strings; **PL:** syntax + semantics.
3.  **Programmability:** primitive operations + sequencing + repetition.
4.  **Structured programming:** sequence + selection + iteration.
5.  **Parnas:** hide design decisions.
6.  **Booch:** encapsulate structure/behavior; interface vs
    implementation.
7.  **Nygaard:** OO as interacting evolving objects/state
    transformations.
8.  **SortedList:** abstraction; `QuickSort`: implementation/process.
9.  **C++ has encapsulation --- the issue is representation/type-layout
    exposure.**
10. **Object independence:** clients should not depend on
    representation.
11. **Procedure ≠ activation ≠ activation record.**
12. **Recursion → separate activation/frame per call.**
13. **Six subroutine features:** name, params, locals, body, return,
    restore.
14. **MOP:** modify language behavior/implementation.
15. **Introspection:** know yourself. **Intercession:** change yourself.
16. **Method combination:** before M→L, primary most-specific, after
    L→M.
17. **Memoization:** compute once, save, reuse.

------------------------------------------------------------------------

# 80. END-OF-NOTES CHECKLIST

Before the exam, you should be able to answer without notes:

-   [ ] What is abstraction?
-   [ ] Why is a PL more than a formal language?
-   [ ] What are the three essentials of programmability?
-   [ ] Static vs dynamic?
-   [ ] What is imperative programming?
-   [ ] Böhm--Jacopini?
-   [ ] What is syntax-directed control flow?
-   [ ] What is scope?
-   [ ] Why modularize?
-   [ ] Parnas's information-hiding criterion?
-   [ ] WHAT vs HOW?
-   [ ] Why `SortedList` rather than `QuickSort` as an abstraction?
-   [ ] Booch's encapsulation?
-   [ ] Parnas vs Booch vs Nygaard?
-   [ ] Procedural vs OO?
-   [ ] What is object independence?
-   [ ] Why is C++ OO-on-procedural "fraught"?
-   [ ] Why can incongruent C++ modules compile/link?
-   [ ] What is the role of `this`?
-   [ ] Java vs C++ according to the slides?
-   [ ] Procedure vs activation vs activation record?
-   [ ] Why does recursion create new activations?
-   [ ] Six subroutine features?
-   [ ] Basic stack-frame organization?
-   [ ] What is MOP?
-   [ ] What is CLOS?
-   [ ] `find-class` vs `class-of`?
-   [ ] Introspection vs intercession?
-   [ ] Method combination order?
-   [ ] Memoization?
-   [ ] What exactly did the slides say about Banana Monkey Jungle /
    contain-and-delegate / tension-triad?
-   [ ] What was the possible dynamic-binding addition?

------------------------------------------------------------------------

## Final note

This file is intended to be the **single consolidated revision
document** from our POPL preparation. It preserves the
professor-specific conceptual framing that mattered during our
discussions and explicitly marks places where the slide deck only gave a
topic name without enough explanation to safely invent the professor's
intended answer.


---

# 81. COMPLETE RECORD OF OUR STUDY APPROACH / USER-SPECIFIC PREFERENCES

This section records how the POPL preparation was conducted so that a local
model can reproduce the same style and priorities.

## 81.1 Preferred teaching style

The student prefers:

- direct, informal, fast explanations;
- very little generic textbook padding;
- explanations grounded in the professor's actual slides/material;
- simple mental models and analogies where useful;
- quick tests after important concepts;
- blunt correction when an answer is incomplete or technically wrong;
- concise exam-safe wording;
- continuation of the syllabus rather than repeatedly re-testing concepts already mastered;
- explicit distinction between what the professor's material says and what is
  merely general PL knowledge.

Do not turn this into a generic programming-languages course. The target is the
professor's POPL course and the exam.

## 81.2 Important learning preference

The student repeatedly emphasized:

> Teach according to the merged professor slides / supplied course material,
> and do not silently add material outside the syllabus.

If a slide merely names a topic without explaining it, do NOT invent a
professor-specific explanation.

Examples where this matters:

- Banana Monkey Jungle Problem
- contain-and-delegate
- Polymorphism, Contracts and Flexibility / "tension-triad"

These appear as slide topics/questions, but the supplied slides do not provide
enough detail to claim exactly what the professor's intended answer is.

If expanding them, explicitly label the expansion as general knowledge or
inference.

---

# 82. WHAT THE STUDENT ALREADY KNOWS

The student has previously taken OOP in Java.

Therefore:

- basic Java OOP explanations are not the priority;
- do not waste time explaining basic classes, objects, inheritance,
  polymorphism, etc. from scratch unless specifically requested;
- the important POPL angle is why OO exists in the professor's framework,
  especially information hiding, object independence, C++ vs Java, and
  procedural-to-OO evolution.

The student is much more interested in the professor's conceptual framing than
in memorizing generic Java syntax.

---

# 83. OUR CONCEPT-BY-CONCEPT PERFORMANCE / CORRECTIONS

These are not additional syllabus topics. They record where answers were
already tested and how they were corrected.

## 83.1 Abstraction

Initial understanding became strong.

Final safe idea:

> Abstraction exposes what matters for use and hides unnecessary
> implementation detail / HOW.

Scott-style framing from the slides:

> Higher-level languages abstract the machine and allow code to be named by
> purpose/role rather than forcing the programmer to describe low-level HOW.

Examples used:

- library function/signature;
- class;
- real numbers vs floating-point representation;
- `SortedList` vs `QuickSort`.

Approximate quiz performance after correction: 4.5/5.

## 83.2 Formal language vs programming language

The student understood the distinction well.

Safe distinction:

- A formal language is a mathematical set of strings/structures satisfying
  some syntactic definition.
- Valid programs form a formal language.
- A programming language is more than the set of valid strings because it also
  has semantics / meaning and an execution model.

Approximate performance: 4–4.5/5.

## 83.3 Static vs dynamic

Final understanding was strong.

Safe mental model:

- static = description/program/type information existing before or independent
  of a particular execution;
- dynamic = behavior/computation occurring during execution.

Important idea from the slides:

> The same command can behave differently because of the environment.

Approximate performance: 4.5/5.

## 83.4 Programmability

The student initially missed part of the intended answer, then corrected it.

The essential ingredients emphasized by the slides are:

1. elementary operations;
2. sequencing;
3. repetition.

"Sequencing" is about composing operations in order, not about real-time
timing.

Repetition can be through:

- iteration;
- recursion;
- cycling/repetition mechanisms.

Do not answer only "loops" or only "functions."

## 83.5 Syntax-directed control flow

The student initially described this incompletely, then corrected it.

Final idea:

> Program structure/syntax and the relationships among constructs determine
> how control moves between pieces of the program.

It is not simply "the program executes top-to-bottom."

Examples:

- blocks;
- loops;
- selection;
- structured calls/returns.

The important connection is that higher-level structured constructs can be
represented through lower-level control transfers such as labels/goto, while
the higher-level language hides that mechanism.

## 83.6 Imperative programming

Final answer should include:

> Imperative programming describes computation through actions that change
> program state.

The student initially conflated imperative programming with structured
programming.

Correction:

- imperative = state-changing commands/actions;
- structured programming = organization of imperative control flow using
  structured sequence/selection/repetition;
- therefore not every imperative program is automatically "structured."

Approximate performance after correction: 4/5.

## 83.7 Modularity vs information hiding

The student initially treated them as opposites.

Correction:

- modularity = organize a system into manageable/delegated components;
- information hiding = choose module boundaries so that design decisions /
  implementation details are hidden behind a minimal interface.

They overlap, but information hiding is a stronger and more specific
criterion for deciding module boundaries.

## 83.8 Procedural vs OO

Initial answers mixed up modularity with the actual distinction.

Final professor-aligned framing:

### Procedural

A complete solution is broken down into step-by-step execution/procedures.
It may be modular, but the final computational organization remains centered
on procedures / sequential execution.

### Object-oriented

The information process is regarded as a system evolving through
transformations of state, composed of independently interacting/evolving
objects. State transformations are actions by objects.

The slide's memorable framing is:

> "Simulation means OO Programming."

## 83.9 SortedList: WHAT vs HOW

This was an important successful refinement.

`SortedList` is a better abstraction because it names WHAT the programmer
wants:

- a list that remains sorted;
- operations such as `put`, `remove`, `nth element`.

Its implementation HOW may change:

- array;
- linked structure;
- insertion sorting;
- another internal algorithm.

`QuickSort`, by contrast, names a particular process / HOW.

Exam-safe sentence:

> A good abstraction names the programmable responsibility/WHAT while hiding
> the implementation/HOW.

Approximate performance: 4.5/5.

## 83.10 Encapsulation

A major correction:

Do NOT say:

> "Encapsulation means hiding methods."

That is inaccurate.

Better:

> Encapsulation is the process of compartmentalizing the elements of an
> abstraction that constitute its structure and behavior, separating the
> contractual interface from the implementation.

Public methods can be part of the visible interface. What is hidden is the
implementation/detail that the client should not depend on.

This is the Booch framing from the slide.

## 83.11 Parnas / Booch / Nygaard

Memorize:

- **Parnas → information hiding / modularization criterion**
- **Booch → encapsulation**
- **Nygaard → object-oriented programming**

Mental chain:

> Parnas tells us how to choose module boundaries by hiding design
> decisions.
>
> Booch describes encapsulation as the concrete separation of interface and
> implementation.
>
> Nygaard frames OO as an evolving information process composed of
> independently interacting evolving objects.

## 83.12 C++ "OO on top of procedural is fraught"

The student initially summarized this as "data leakage."

Correction: use the more precise causal chain.

### Safe causal chain

1. C++ class declarations/type definitions must be available to modules using
   the class.
2. The class's storage structure/layout must be known.
3. Therefore implementation representation can be exposed across module
   boundaries.
4. Each module has independent static type information.
5. The compiler turns that information into machine-level quantities such as
   offsets/layouts.
6. The linker generally does not preserve all symbolic type information.
7. Incongruent declarations can therefore sometimes compile/link.
8. The resulting program can fail semantically or at runtime.

The problem is not "C++ has no encapsulation."

Better:

> C++ has encapsulation, but its procedural/module model can weaken object
> independence because representation and type/layout information may need to
> cross module boundaries.

Approximate performance on the C++ question after refinement: 4.5/5.

## 83.13 Object independence

The student's initial phrase was roughly "the object itself is responsible for
itself."

That is directionally useful but too vague.

Better:

> Object independence means an object/component should maintain its own
> relevant structure and behavior without other modules depending on its
> internal representation.

Connect this to C++:

> If clients/modules need to know the object's storage layout or internal type
> representation, they become dependent on implementation details, weakening
> object independence.

## 83.14 Java vs C++

The student's answer was initially too vague.

Exam-safe comparison:

### Java

- no global functions in the ordinary C/C++ sense;
- type linkages needed for bindings are maintained globally throughout
  execution, except specifically local types within classes;
- the slides therefore characterize Java as "purely" OO in the relevant
  sense.

### C++

- retains a strong procedural/module model;
- class/type/storage layout information can be required across module
  boundaries;
- this weakens the stronger object-independence goal.

Do not simply say "Java has no data leakage and C++ has data leakage."

---

# 84. PROCEDURE ACTIVATION: WHAT WAS ALREADY TESTED

This was tested with a mini-quiz.

## Q1: What are the six subroutine features?

The student already knew these.

1. name and signature/prototype;
2. parameter passing and access;
3. allocation/access to locals and temporaries, including access to nonlocals;
4. imperative action / procedure body;
5. preparation to return;
6. restoration of program state at return.

## Q2: Why does a new activation exist?

Student answer:

> "because new call"

Correct.

Refined:

> Each call/execution of a procedure creates a distinct activation because
> that call needs its own runtime state.

## Q3: One procedure called 7 times?

Correct model:

- 1 procedure = 1 static code entity;
- 7 calls = 7 activations;
- normally 7 activation records.

Recursion is exactly why multiple activation records for the same procedure
can coexist.

---

# 85. COVERAGE STATUS DURING PREPARATION

At the end of the main slide-by-slide teaching pass:

> **Essentially 100% of the actual 118 content slides had been covered at
> least once.**

The merged PDF itself had 152 pages, with later pages consisting partly of
references/citations.

Important distinction:

- "covered" does NOT mean "exam-ready";
- consolidation, recall, question practice, and exam-style application are
  still necessary.

Earlier rough estimates were around 65–70% before the remaining procedure
activation / Java / MOP / reflection slides were taught. The final status after
those slides was essentially full slide coverage.

---

# 86. TUTORIAL / SLIDE CHALLENGES THAT MUST NOT BE FORGOTTEN

These were explicitly visible in the supplied material.

## 86.1 Recursion in `main`

Question idea:

> Can `main` be recursive?

The point is to reason about procedure activation rather than assuming
recursion is restricted to specially named functions.

## 86.2 Reverse command-line arguments

Implement reversal of command-line arguments.

Relevant concepts:

- arrays/parameters;
- indexing;
- procedure structure;
- possibly recursion/iteration.

## 86.3 Mutually recursive functions

The slides challenge the student to reason about mutually recursive functions
where the call/termination behavior may not be obvious by simply reading one
function.

Relevant concepts:

- recursion;
- activation records;
- control flow;
- compositional reasoning.

## 86.4 Implement your own `printf`

Purpose:

- understand what a library abstraction is hiding;
- connect high-level interface to lower-level implementation;
- illustrate abstraction and control/data transfer.

Do not assume the professor expects a full industrial `printf`; the slide's
point is conceptual/programming practice.

## 86.5 Opaque type

Question idea:

> Design/use a type whose internal contents are inaccessible to the user.

This directly connects to:

- data abstraction;
- information hiding;
- interface vs implementation;
- object independence.

## 86.6 Generate/print an integer with only math operations

The challenge restricts the implementation to mathematical operations / `math.h`
(or analogous mathematical functions) and arithmetic expressions, without
ordinary assignment.

The conceptual point is that restrictions on elementary operations force us to
think about what constitutes a programmable operation and how expressions can
be composed.

## 86.7 Dictionary design

The supplied material included dictionary-related material/examples.

Treat this as a design/abstraction exercise:

- identify WHAT the dictionary abstraction must provide;
- separate interface from implementation;
- think about implementation alternatives;
- do not prematurely lock the abstraction to one representation.

---

# 87. SOURCE-FAITHFUL EXAMPLES / CODE IDEAS DISCUSSED

## 87.1 Recursive GCD example

The slide example was:

```c
int gcd(int parama, int paramb) {
    static int xstatic = 0;
    xstatic = parama-paramb;

    if (parama > 0 && paramb > 0) {
        if (xstatic < 0)
            return gcd(parama, -xstatic);
        else if (xstatic > 0)
            return gcd(paramb, xstatic);
        else
            return parama;
    } else
        return -1;
}
```

Important things to notice:

- `gcd` is one static procedure/code body;
- every recursive call creates a new activation;
- each activation gets its own parameters/local runtime state;
- `xstatic` is static storage and is NOT the same thing as an ordinary
  per-activation local;
- recursion therefore produces multiple activation records associated with the
  same procedure.

---

# 88. STACK-FRAME / ACTIVATION DETAILS WE DISCUSSED

A typical conceptual sequence from the slides:

1. caller prepares/pushes incoming parameters;
2. `call` saves necessary state, including return address;
3. callee establishes a frame/base pointer;
4. local variables and temporary storage are allocated;
5. caller/frame state is preserved;
6. an access link can be used to connect relevant enclosing/runtime state;
7. outgoing parameters for recursive/other calls are prepared;
8. called procedure executes;
9. return value is placed where the calling convention expects;
10. `leave` / `ret`-style operations restore state and return.

Important slide-level terminology:

- activation record;
- stack frame;
- frame pointer/base pointer;
- return address;
- saved state;
- access link;
- parameters;
- locals;
- temporaries;
- return value.

Do not overgeneralize a specific assembly calling convention into a universal
rule for every architecture. Use the slide's model for exam answers.

---

# 89. ASSEMBLY / LINKER QUESTIONS FROM THE SLIDES

The slides ask students to reason about:

- Which names survive into a linker symbol table?
- Which names become offsets?
- How are offsets generated?
- How are C++ method calls represented?
- How can class names/signatures be preserved enough for:
  - membership;
  - hierarchy;
  - overloading;
  - overriding;
  - virtual dispatch;
  - reflection?

These questions connect the high-level language concepts to compilation,
linking, layout, and runtime representation.

The professor's larger theme is:

> High-level abstraction can hide implementation details from the programmer,
> but the compiler/runtime must still represent enough information to execute
> the program.

---

# 90. "WHEN TO JAVA?" AND "WHEN NOT TO JAVA?"

These slides were covered but are partly provocative rather than fully
explained.

## When to Java?

The slide themes were:

- mainly for interfacing;
- not merely UI;
- development without requiring nitty-gritty implementation detail;
- when the focus is core business logic rather than low-level implementation;
- when user/system requirements drive choices of libraries/class hierarchies.

Provocative slide question:

> Then, wouldn't LLMs replace Java programmers?

Do not invent the professor's intended answer.

## When NOT to Java?

The slides explicitly list:

- Banana Monkey Jungle Problem;
- problem with "contain and delegate" solution;
- whether "reusability" is such a holy/useful thing in the LLM era;
- Polymorphism, Contracts and Flexibility = "tension-triad".

These should be recognized as syllabus/slide topics, but the provided slides do
not contain enough detail to claim a precise professor-specific derivation.

---

# 91. MOP / CLOS / REFLECTION: IMPORTANT "GENERICS" CORRECTION

One of the easiest places to go wrong:

> The section labelled "Generics" in the slides is NOT ordinary Java generics.

It is primarily about:

- MetaObject Protocol (MOP);
- CLOS;
- reflection;
- introspection;
- intercession;
- method combination.

## 91.1 MetaObject Protocol

Definition from the course framing:

> Interfaces to a language that allow users to incrementally modify language
> behavior and implementation, as well as write programs.

This blurs the distinction between:

- language designer;
- language user.

## 91.2 Why MOP?

The motivation is to reconcile:

- elegance / expressive power of high-level languages such as Scheme, Prolog,
  ML;
- industrial efficiency/control associated with C/C++.

## 91.3 CLOS features

The slides list:

- class redefinition;
- method redefinition;
- forward-referencing superclasses;
- implicit generic function definitions;
- user control of method combination;
- `eql` specializers;
- slots with class allocation;
- integrated types/classes.

## 91.4 CLOS program structure

Typical pieces:

```lisp
defclass
defgeneric
defmethod
```

Execution creates internal representations.

## 91.5 Reflection

Two key concepts:

### Introspection

A program acquires information about itself and uses that information within
the program.

### Intercession

A program alters its own behavior.

The slides characterize Lisp/Smalltalk as supporting both.

The slide's characterization of Java is essentially:

> Java is basically introspective only.

Do not replace this with a broader generic reflection lecture unless asked.

## 91.6 `find-class` and `class-of`

- `find-class` returns a class metaobject associated with a named class.
- `class-of` obtains the class/metaobject associated with an object.

The exact API details should follow the course slide rather than a generic
language implementation manual.

## 91.7 Method combination

Order from the slides:

1. `before` methods: most specific → least specific;
2. most specific applicable primary method;
3. `after` methods: least specific → most specific;
4. return values come from the primary method.

Memorize the direction.

## 91.8 Performance vs expressive power

Naive MOP implementations can be inefficient.

The course discusses optimizing critical internal computations.

### Memoization

Compute something once, save the result, and reuse it when the same result is
needed again.

---

# 92. DYNAMIC BINDING

Dynamic binding was identified as a possible small late addition in the
updated slides.

Do not confuse:

- lexical/static binding;
- dynamic binding.

Broad safe distinction:

### Static / lexical binding

The meaning/reference of a name is determined using program structure / lexical
context.

### Dynamic binding

The binding can depend on the dynamic calling/execution context.

Because this was identified as a possible late addition, check the actual
updated slide wording if answering a professor-specific question.

---

# 93. THE COMPLETE CONCEPT MAP

A useful dependency graph for the whole course:

```text
COMPUTATION
   │
   ├── elementary operations
   ├── sequencing
   └── repetition
          │
          ↓
PROGRAMMING LANGUAGES
          │
          ├── syntax / formal language
          ├── semantics / execution
          └── abstraction
                 │
                 ├── control abstraction
                 └── data abstraction
                        │
                        ↓
                    MODULARITY
                        │
                        ↓
                 INFORMATION HIDING
                     (Parnas)
                        │
                        ↓
                  ENCAPSULATION
                     (Booch)
                        │
                        ↓
                 OBJECT INDEPENDENCE
                        │
                        ↓
              OBJECT-ORIENTED VIEW
                    (Nygaard)
                        │
                 ┌──────┴──────┐
                 ↓             ↓
                C++           Java
                 │             │
     procedural/module      stronger global
       representation       type linkage
                 │             │
                 ↓             ↓
       layout exposure     object independence
                 │
                 ↓
      "OO on top of procedural
            is fraught"
```

Separate but related runtime path:

```text
PROCEDURE
   ↓
CALL
   ↓
ACTIVATION
   ↓
ACTIVATION RECORD / STACK FRAME
   ↓
params + locals + temporaries + saved state + return info
   ↓
recursive calls
   ↓
multiple simultaneous activations
```

And meta-language path:

```text
LANGUAGE
   ↓
MOP
   ↓
METAOBJECTS
   ├── introspection
   └── intercession
          ↓
        CLOS
          ↓
   method combination
```

---

# 94. EXACT "DO NOT SAY THIS" LIST

These were recurring corrections.

## Do not say:

> Encapsulation means hiding methods.

Say:

> Encapsulation separates the contractual interface from implementation and
> compartmentalizes structure and behavior.

## Do not say:

> C++ has no encapsulation.

Say:

> C++ has encapsulation, but its procedural/module model can expose
> representation/layout information across module boundaries, weakening
> object independence.

## Do not say:

> C++ has data leakage.

Prefer:

> Type/storage-layout information can be exposed across module boundaries,
> allowing modules to depend on representation.

## Do not say:

> Modularity and information hiding are opposites.

Say:

> Information hiding is a particular criterion for modularization based on
> hiding design decisions behind interfaces.

## Do not say:

> OO just means classes.

Use the slide framing:

> OO views an information process as a system evolving through state
> transformations and composed of independently interacting/evolving objects.

## Do not say:

> Structured programming = imperative programming.

Instead:

> Structured programming organizes imperative computation using structured
> control flow such as sequence, selection, and iteration.

## Do not say:

> The "Generics" section is about Java generics.

It is about MOP/CLOS/reflection.

## Do not assume:

> The professor's exact intended explanation of Banana Monkey Jungle,
> contain-and-delegate, or the tension-triad.

The slide only gives the topics.

---

# 95. PROFESSOR-ALIGNED ANSWER FORMULA

For conceptual questions, the strongest pattern is:

### 1. Definition

State the course concept in one sentence.

### 2. Mechanism / reason

Explain HOW or WHY.

### 3. Professor's framing

Use the relevant terminology:

- WHAT vs HOW;
- information hiding;
- object independence;
- state transformation;
- procedure activation;
- interface vs implementation.

### 4. Example

Give the slide example if possible.

### 5. Contrast

If the question is comparative, explicitly contrast the alternatives.

Example:

> Why is C++ "OO on top of procedural" fraught?

Answer structure:

1. C++ provides OO constructs and encapsulation.
2. But its module/procedural compilation model requires class/type/layout
   information across compilation units.
3. Therefore representation details can leak across module boundaries.
4. Modules can become dependent on object representation.
5. This weakens object independence/information hiding.
6. Hence the slide calls OO-on-procedural "fraught."

---

# 96. LOCAL QWEN / BIONIC EXPERIMENT — COMPLETE RECORD

After the POPL preparation, we discussed creating a local offline knowledge
assistant using Qwen3 14B on the user's Mac.

## Hardware

The user's machine:

- Mac;
- M2 normal chip;
- 16 GB unified memory.

A local Qwen3 14B setup was considered usable, but the user found the
document-processing workflow cumbersome.

## Goal

The intended goal was:

> Give the local model all POPL course material once, then ask NEW questions
> based on that material later, even after shutting down the Mac.

This is fundamentally a **document retrieval / RAG** use case, not model
fine-tuning.

Conceptually:

```text
course PDFs / notes
       ↓
document retrieval/index
       ↓
Qwen3 14B
       ↓
new question
       ↓
retrieve relevant material
       ↓
reason over retrieved material
       ↓
answer
```

The important distinction:

- model weights do not permanently "learn" the PDFs;
- a persistent project/index makes the documents available for retrieval;
- chat history and document knowledge are different things.

## The folder the user actually has

The user already has a folder named:

```text
POPL Midsem/
```

It contains many relevant files, including things such as:

- Assignment 1.pdf
- Assignment 2.pdf
- Assignment 3.pdf
- Bjarne Stroustrup ... Abstract Classes.pdf
- chicken crossing puzzle.pdf
- compre-answer-key.pdf
- debates (1).zip
- Dictionary (2).zip
- Dictionary (3).zip
- dynamicBindingJava.zip
- Example Program ... Q2 - 2025 PDF.pdf
- Example Program GCD.pdf
- and other POPL course material.

The user does NOT need to reorganize this into multiple subfolders just for the
sake of organization.

## Bionic setup attempted

The user opened Bionic with Qwen3 14B selected and created a Project named
roughly:

```text
hilol
```

The user then attached the `POPL Midsem` folder to that Project.

The intended use was:

```text
POPL Project
   ├── course files
   ├── assignments
   ├── tutorials
   └── notes
          ↓
       Bionic
          ↓
      Qwen3 14B
```

## First test

The user asked:

> "hi what is there in the folder"

Bionic displayed:

```text
Processing prompt 37%
Context: 6.2K
```

The interpretation was that Bionic was processing a relatively large context
rather than instantly listing a directory.

We decided this was not a useful first test because asking the model to inspect
the entire folder at once can force a large amount of processing.

A better test would have been a specific file question, e.g.:

> Read `Assignment 1.pdf` and summarize what topics/questions it contains.

## Final practical decision

The user decided that fighting the offline model/RAG setup was not worthwhile
for immediate exam preparation.

This is an important preference:

> **Do not turn exam preparation into an ML infrastructure project.**

The local model can be revisited later with a smaller curated document set, but
for the immediate POPL exam, direct study from the already-understood course
material is more useful.

---

# 97. LOCAL MODEL SYSTEM PROMPT THAT WAS PROPOSED

If the user later sets up the local model again, the proposed instruction was:

```text
You are my POPL exam assistant.

SOURCE PRIORITY:
1. Professor's slides
2. Professor's handout
3. Tutorials
4. Assignments
5. My consolidated notes

Answer questions primarily from the supplied course material.

Do not invent what the professor meant.
Preserve the professor's terminology and framing.
If the supplied material does not support an answer, say so.

When useful, distinguish:
SOURCE: what the course material explicitly says
INFERENCE: what follows from the source
GENERAL KNOWLEDGE: information outside the supplied material

Do not silently replace the professor's framing with generic textbook framing.
```

This prompt should be used if/when a persistent local document assistant is
configured.

---

# 98. FINAL MASTER REVISION PRIORITY

If time is extremely limited, prioritize in this order.

## Priority 1 — Highest value

1. Abstraction / WHAT vs HOW
2. Formal language vs programming language
3. Programmability: elementary operations + sequencing + repetition
4. Imperative programming
5. Structured/syntax-directed control flow
6. Modularity
7. Parnas / information hiding
8. Booch / encapsulation
9. Nygaard / OO
10. Procedural vs OO
11. Object independence
12. Why C++ OO-on-procedural is fraught
13. Java vs C++
14. Procedure activation
15. Activation records / stack frames
16. Recursion and multiple activations

## Priority 2 — Important

17. Program lifetime: compile/link/load/activation/run
18. `this`
19. static storage vs activation storage
20. C++ module/type/layout/linker issues
21. opaque types
22. top-down vs bottom-up design
23. data types
24. scope
25. Böhm-Jacopini
26. reflection
27. introspection vs intercession
28. CLOS / MOP
29. method combination
30. memoization
31. dynamic binding

## Priority 3 — Recognize and review

32. When to Java
33. When not to Java
34. Banana Monkey Jungle
35. contain-and-delegate
36. reusability in the LLM era
37. polymorphism/contracts/flexibility tension-triad
38. tutorial programming challenges
39. assembly/linker questions

For the Priority 3 provocative topics, return to the actual slide rather than
inventing a professor-specific answer.

---

# 99. 30-SECOND MEMORY SHEET

```text
ABSTRACTION
    WHAT, not HOW

FORMAL LANGUAGE
    set of valid strings/structures

PROGRAMMING LANGUAGE
    syntax + semantics / execution meaning

PROGRAMMABILITY
    elementary ops + sequence + repetition

IMPERATIVE
    state-changing actions

STRUCTURED CONTROL
    sequence + selection + iteration

SCOPE
    textual/contextual extent where a name is usable/referential

MODULARITY
    divide/delegate system responsibilities

PARNAS
    INFORMATION HIDING
    hide design decisions behind interfaces

BOOCH
    ENCAPSULATION
    compartmentalize structure/behavior
    separate contractual interface from implementation

NYGAARD
    OO
    evolving information process
    independently interacting evolving objects

WHAT vs HOW
    SortedList = WHAT
    QuickSort = HOW

OBJECT INDEPENDENCE
    object should own/maintain its structure + behavior
    clients shouldn't depend on representation

C++
    encapsulation exists
    but module/type/layout exposure can weaken independence
    OO on top of procedural = fraught

JAVA
    no ordinary global functions
    type linkage information maintained through execution
    slide calls it "purely" OO in relevant sense

PROCEDURE
    static code

ACTIVATION
    one particular execution/call

ACTIVATION RECORD
    runtime state for that call

RECURSION
    same procedure
    multiple activations
    multiple activation records

MOP
    modify language behavior/implementation

INTROSPECTION
    inspect/use information about self

INTERCESSION
    change own behavior

METHOD COMBINATION
    before: specific → general
    primary: most specific applicable
    after: general → specific

MEMOIZATION
    compute once, save, reuse
```

---

# 100. FINAL INSTRUCTION TO A LOCAL MODEL USING THIS FILE

If this Markdown file is supplied to a local LLM as course knowledge, the
model should follow these rules:

1. Treat professor-supplied material as authoritative.
2. Treat this file as a consolidated study representation, not as permission
   to invent missing professor explanations.
3. Preserve terminology and distinctions in this document.
4. If a question asks for the professor's exact framing, prefer explicit
   source statements over generic PL knowledge.
5. If the source does not explain a topic, say that it does not provide enough
   information.
6. Do not turn a short conceptual answer into an unnecessary textbook lecture.
7. For exam answers, use the professor's WHAT/HOW, information-hiding,
   encapsulation, object-independence, procedure-activation, and
   introspection/intercession vocabulary where relevant.
8. When correcting an answer, explicitly identify the incorrect phrase and
   replace it with the exam-safe version.
9. Do not confuse the "Generics" slide section with Java generics.
10. Do not claim C++ lacks encapsulation.
11. Do not reduce the C++ problem to vague "data leakage."
12. Do not claim that the provocative "When Not to Java?" slide topics have
    explanations that are absent from the supplied material.
13. Do not silently add the excluded Python nugget from Topic 4.
14. Respect the syllabus boundary: first four handout topics, relevant cited
    material, LT1/tutorials/assignments/Moodle material, and slides updated by
    EoD 2 October 2026; nothing beyond unless explicitly requested.

---

# 101. END

This document is intended to be the **single consolidated POPL knowledge file**
covering the syllabus, source-derived concepts, explanations, corrections,
exam-safe wording, exercises, priorities, and the local-model context discussed
through the preparation conversation.

