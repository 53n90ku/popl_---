# 📝 Topic 02: C++ Module Decoupling, Representation Leak & Abstract Classes
### Predicted Exam Questions & Complete Professor-Approved Solutions

---

## 📌 Question 1: C++ Representation Leakage & Compilation Coupling (Direct 2025 Midsem Q1 Variant)

### Problem Statement
A software development team develops a high-performance graphics library in C++. Module `Renderer` depends on Module `Texture`:

```cpp
// Texture.h
#pragma once
#include <string>

class Texture {
private:
    int width;
    int height;
    unsigned int texture_id;

public:
    Texture(int w, int h);
    void bind();
    void render();
    int getWidth() const;
    int getHeight() const;
    ~Texture();
};
```

```cpp
// Renderer.cpp
#include "Texture.h"

void drawScene(Texture& tex) {
    tex.bind();
    tex.render();
}
```

A developer optimizes `Texture` by adding a private caching member:
```cpp
    bool is_cached; // Added inside private section of Texture.h
```
None of the public method signatures in `Texture.h` were modified. 

1. Why does the C++ build system (Makefile/CMake) force `Renderer.cpp` (and every other module including `Texture.h`) to recompile?
2. Explain the **two exact technical reasons** the C++ compiler must know the private members of `Texture` when compiling `Renderer.cpp`, even though `Renderer.cpp` cannot legally access any private members.
3. Why does this design violate David Parnas’s core principle of **Information Hiding**?

---

### 💡 Model Solution

#### 1. Why Recompilation is Forced
In C++, `#include "Texture.h"` textually copies the contents of `Texture.h` into `Renderer.cpp` during preprocessing. The build system tracks file modification timestamps (`.h` modified time > `.o` build time). Because `Texture.h` changed, the build tool triggers a full recompilation of `Renderer.cpp`.

#### 2. The Two Mechanical Reasons the C++ Compiler Needs Private Members
Even though private members are inaccessible to client code, the C++ compiler must know them during compilation of client files for two critical reasons:

1. **Object Size Determination (`sizeof`) for Stack and Embedded Allocation:**
   - If `Renderer.cpp` declares a local stack object:
     ```cpp
     Texture t(1920, 1080);
     ```
     or embeds a `Texture` as a member of another class:
     ```cpp
     class Scene { Texture bg; };
     ```
   - The compiler must know the **exact byte size** of `Texture` at compile time to:
     - Decrement the stack pointer (`subq $size, %rsp`).
     - Calculate memory offsets in activation records.
   - Adding `bool is_cached` changes `sizeof(Texture)` (e.g., from 12 bytes to 16 bytes due to alignment padding). Without recompiling, stack frames would be corrupted!

2. **Member Byte Offsets Computation:**
   - The compiler generates machine code with hard-coded byte offsets from the `this` pointer for direct member accesses (e.g., `this + 4`, `this + 8`).
   - If private layout changes, subsequent member offsets change. The compiler cannot take a "leap of faith" with memory layout.

#### 3. Violation of Parnas Information Hiding
Parnas stated: *"A module should hide a design decision likely to change from the rest of the program."*
In standard C++ concrete class headers, **implementation detail is colocated with interface definition**. The private section is visible textually in the header file. Any change to internal implementation details breaks the compilation boundary of unrelated modules.

---

## 📌 Question 2: Stroustrup’s Pure Abstract Base Class Pattern (The Architectural Solution)

### Problem Statement
Show how to restructure the `Texture` system from Question 1 using **Bjarne Stroustrup’s Pure Abstract Class Pattern** so that changes to `Texture`'s internal data members **never force client files to recompile**.

Provide:
1. `ITexture.h` (The pure abstract interface).
2. `TextureImpl.h` and `TextureImpl.cpp` (The hidden concrete implementation).
3. `Renderer.cpp` (The client code).
4. Explain how this achieves complete module independence.

---

### 💡 Model Solution

#### 1. The Pure Abstract Interface (`ITexture.h`)
```cpp
// ITexture.h
#pragma once

class ITexture {
public:
    // Pure virtual interface methods
    virtual void bind() = 0;
    virtual void render() = 0;
    virtual int getWidth() const = 0;
    virtual int getHeight() const = 0;

    // MANDATORY: Virtual destructor to ensure proper cleanup through interface pointer
    virtual ~ITexture() {}
};

// Factory function: Returns interface pointer, hiding concrete class creation
ITexture* createTexture(int w, int h);
```

#### 2. Concrete Implementation (`TextureImpl.h` & `TextureImpl.cpp`)
```cpp
// TextureImpl.h - PRIVATE TO THE TEXTURE MODULE (Not distributed to clients!)
#pragma once
#include "ITexture.h"

class TextureImpl : public ITexture {
private:
    int width;
    int height;
    unsigned int texture_id;
    bool is_cached; // Adding/removing fields here only affects TextureImpl.cpp!

public:
    TextureImpl(int w, int h);
    virtual void bind() override;
    virtual void render() override;
    virtual int getWidth() const override;
    virtual int getHeight() const override;
    virtual ~TextureImpl() override;
};
```

```cpp
// TextureImpl.cpp
#include "TextureImpl.h"
#include <iostream>

TextureImpl::TextureImpl(int w, int h) 
    : width(w), height(h), texture_id(1), is_cached(false) {}

void TextureImpl::bind() { /* GPU bind code */ }
void TextureImpl::render() { /* GPU render code */ }
int TextureImpl::getWidth() const { return width; }
int TextureImpl::getHeight() const { return height; }
TextureImpl::~TextureImpl() { /* release GPU texture */ }

// Factory implementation
ITexture* createTexture(int w, int h) {
    return new TextureImpl(w, h);
}
```

#### 3. Client Code (`Renderer.cpp`)
```cpp
// Renderer.cpp
#include "ITexture.h" // ONLY includes the pure interface

void drawScene() {
    ITexture* tex = createTexture(1920, 1080);
    tex->bind();
    tex->render();
    delete tex;
}
```

#### 4. Why This Eliminates Compilation Coupling
* `ITexture.h` contains **zero data members** and only pure virtual functions (`= 0`).
* Client code (`Renderer.cpp`) interacts only through `ITexture*` pointers. The size of any pointer in 64-bit architecture is **always 8 bytes**, regardless of what fields are inside `TextureImpl`.
* When developers add, delete, or change private members in `TextureImpl.h` / `TextureImpl.cpp`, **`ITexture.h` does not change**.
* Therefore, `Renderer.cpp` **never recompiles**. Only `TextureImpl.cpp` is recompiled, and the linker links them together. True physical module independence is achieved!

---

## 📌 Question 3: Vtable and Vptr Internal Mechanics

### Problem Statement
Given the abstract class `ITexture` and concrete class `TextureImpl` from Question 2:
1. Draw the exact memory layout of an instantiated `TextureImpl` object in heap memory.
2. Draw the Virtual Method Table (**vtable**) generated by the compiler.
3. Show the assembly instruction sequence that executes when the client calls:
   ```cpp
   tex->render();
   ```
4. What is the execution overhead of this dynamic dispatch compared to a regular non-virtual function call?

---

### 💡 Model Solution

#### 1 & 2. Memory Layout Diagram

```text
Heap Object (TextureImpl instance)           Virtual Method Table (vtable for TextureImpl)
+-----------------------------------+        +-----------------------------------+
|  vptr (8 bytes)                   | -----> | &TextureImpl::~TextureImpl()      | (Slot 0)
+-----------------------------------+        +-----------------------------------+
|  int width (4 bytes)              |        | &TextureImpl::bind()              | (Slot 1)
+-----------------------------------+        +-----------------------------------+
|  int height (4 bytes)             |        | &TextureImpl::render()            | (Slot 2)
+-----------------------------------+        +-----------------------------------+
|  unsigned int texture_id (4 bytes)|        | &TextureImpl::getWidth()          | (Slot 3)
+-----------------------------------+        +-----------------------------------+
|  bool is_cached (1 byte)          |        | &TextureImpl::getHeight()         | (Slot 4)
+-----------------------------------+        +-----------------------------------+
|  [3 bytes padding for alignment]  |
+-----------------------------------+
```

#### 3. Assembly Trace for `tex->render()`
Suppose pointer `tex` is stored in register `%rdi`:
```assembly
movq (%rdi), %rax          # 1. Dereference object to fetch vptr into %rax
movq 16(%rax), %rax        # 2. Fetch slot 2 (render method address, 2 * 8 = 16 bytes offset)
call *%rax                 # 3. Indirect call through function pointer!
```

#### 4. Overhead Analysis
1. **Time Overhead:** One memory indirection to fetch the vptr table address, one memory fetch for the function pointer, and an indirect branch instruction (`call *%rax`), which can cause CPU branch prediction misses. Additionally, virtual functions **cannot be inlined** by the compiler when called through a base pointer.
2. **Space Overhead:** Every object instance carries one hidden pointer (`vptr`, 8 bytes), and each class with virtual functions generates one `vtable` in the read-only data segment (`.rodata`).

---

## 📌 Question 4: C++ vs Java Compilation & Linking Coupling

### Problem Statement
Why does Java **NOT** suffer from the C++ recompilation problem when private members are added to a class, whereas C++ does? Contrast the memory models and linking mechanisms of both languages.

---

### 💡 Model Solution

| Feature | C++ | Java |
| :--- | :--- | :--- |
| **Object Allocation** | Stack, Heap, or Global. Stack allocation requires compiler to know exact byte size at compile time. | **Always on Heap.** Variables in stack frames are strictly 4/8 byte object references (handles). Frame size is completely independent of class size. |
| **Field Access Resolution** | Hardcoded by compiler as static byte offsets (`this + 8`) into machine instructions. | Resolved symbolically at class loading time via JVM constant pool references (e.g., `#FieldRef`). JVM computes offsets dynamically. |
| **Binary Interface** | Fragile Binary Interface (ABI): Changing private fields breaks byte alignment of existing compiled binaries. | Robust Bytecode Interface: Adding private fields does not alter constant pool resolution for existing callers. |
| **Separate Compilation** | Compiler reads textual headers (`#include`) and needs full layout. | Compiler checks type contracts via `.class` metadata; runtime ClassLoader links dynamically. |
