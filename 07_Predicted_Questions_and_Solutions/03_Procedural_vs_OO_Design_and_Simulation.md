# 📝 Topic 03: Procedural vs OO Design, Simulation & Architecture
### Predicted Exam Questions & Complete Professor-Approved Solutions

---

## 📌 Question 1: Converting Procedural Execution to OO Simulation (Direct 2025 Midsem Q2 Variant)

### Problem Statement
Below is a typical procedural implementation of an Automated Teller Machine (ATM) transaction system in C:

```c
/* Procedural ATM */
#include <stdio.h>

int account_balance = 1000;
int atm_cash_available = 500;

void verify_pin(int input_pin, int actual_pin, int *status) {
    if (input_pin == actual_pin) *status = 1;
    else *status = 0;
}

void withdraw_cash(int amount, int *user_bal, int *atm_cash, int *dispensed) {
    if (amount <= *user_bal && amount <= *atm_cash) {
        *user_bal -= amount;
        *atm_cash -= amount;
        *dispensed = amount;
    } else {
        *dispensed = 0;
    }
}

int main() {
    int auth_status = 0;
    int cash_dispensed = 0;
    
    verify_pin(1234, 1234, &auth_status);
    if (auth_status) {
        withdraw_cash(300, &account_balance, &atm_cash_available, &cash_dispensed);
        printf("Dispensed: %d, Remaining Bal: %d, ATM Cash: %d\n", 
               cash_dispensed, account_balance, atm_cash_available);
    }
    return 0;
}
```

1. Explain why this program represents the **Procedural Paradigm** rather than the **Object-Oriented Paradigm** according to Kristen Nygaard.
2. Refactor this system into an authentic **Object-Oriented Simulation** in C++.
3. In your refactored design, show how the `main()` function reflects the OO principle of **"Instantiate and Forget"**.

---

### 💡 Model Solution

#### 1. Why this is Procedural (The Procedural Flaw)
* **Passive Data vs Active Code:** Data (`account_balance`, `atm_cash_available`) is passive, global, or passed around as pointers. Functions are active verbs that act upon exposed data structures (`f(data)`).
* **Centralized Controller:** The `main()` function acts as an all-knowing, imperative controller driving every minor procedural step (checking auth flags, orchestrating variable updates).
* **Lack of Encapsulation & State Invariant:** Any procedure can arbitrarily mutate `account_balance` without maintaining business rules or invariants.

#### 2. Refactored Object-Oriented Simulation (C++)
In Kristen Nygaard’s definition: *"Object-oriented programming is a method of programming where a program execution is regarded as a physical model simulating the behavior of either a real or an imagined part of the world."*

```cpp
#include <iostream>
#include <string>

// Autonomous Entity 1: BankAccount maintains its own balance and authentication
class BankAccount {
private:
    int balance;
    int pin;

public:
    BankAccount(int initial_bal, int secret_pin) 
        : balance(initial_bal), pin(secret_pin) {}

    bool authenticate(int entered_pin) const {
        return entered_pin == pin;
    }

    bool debit(int amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            return true;
        }
        return false;
    }

    int getBalance() const { return balance; }
};

// Autonomous Entity 2: CashDispenser manages hardware reserve
class CashDispenser {
private:
    int cash_on_hand;

public:
    CashDispenser(int initial_cash) : cash_on_hand(initial_cash) {}

    bool dispense(int amount) {
        if (amount <= cash_on_hand) {
            cash_on_hand -= amount;
            std::cout << "[Dispenser] Physically dispensed $" << amount << "\n";
            return true;
        }
        std::cout << "[Dispenser] Error: Insufficient cash in machine.\n";
        return false;
    }

    int getCashOnHand() const { return cash_on_hand; }
};

// Autonomous Entity 3: ATM models the coordinator object collaborating via messages
class ATM {
private:
    CashDispenser dispenser;

public:
    ATM(int initial_cash) : dispenser(initial_cash) {}

    void processWithdrawal(BankAccount& account, int entered_pin, int amount) {
        std::cout << "--- Processing Withdrawal Request of $" << amount << " ---\n";
        
        if (!account.authenticate(entered_pin)) {
            std::cout << "[ATM] Authentication failed: Invalid PIN.\n";
            return;
        }

        if (dispenser.getCashOnHand() < amount) {
            std::cout << "[ATM] Machine unable to satisfy request: Low cash.\n";
            return;
        }

        if (account.debit(amount)) {
            dispenser.dispense(amount);
            std::cout << "[ATM] Transaction complete. Remaining Account Balance: $" 
                      << account.getBalance() << "\n";
        } else {
            std::cout << "[ATM] Transaction rejected: Insufficient account funds.\n";
        }
    }
};
```

#### 3. The "Instantiate and Forget" `main()`
```cpp
int main() {
    // 1. Instantiate the autonomous entities that model the physical domain
    BankAccount customer_account(1000, 1234);
    ATM city_atm(500);

    // 2. Instantiate and Trigger: Objects interact through message-passing!
    // The main function does not touch balances or manipulate flags.
    city_atm.processWithdrawal(customer_account, 1234, 300);
    city_atm.processWithdrawal(customer_account, 1234, 400); // Exceeds ATM cash

    return 0;
}
```
* **Why this is true OO:** `main()` does not know how `BankAccount` tracks its balance, nor does it control how cash is deducted. The entities manage their own internal states and collaborate via message passing.

---

## 📌 Question 2: Kristen Nygaard’s Philosophy & Paradigms

### Problem Statement
1. State Kristen Nygaard’s foundational definition of **Object-Oriented Programming**.
2. Contrast the core viewpoint of the **Von Neumann / Imperative Paradigm** with that of the **Object-Oriented Simulation Paradigm**. Fill out the comparison table:
   * View of Data
   * View of Program Execution
   * Role of Main Function
   * Primary Mechanism of Progress

---

### 💡 Model Solution

#### 1. Nygaard’s Foundational Definition
Kristen Nygaard (co-creator of Simula 67) defined OOP:
> *"A program execution is regarded as a physical model, simulating the behavior of either a real or an imagined part of the world. The objects in the program represent phenomena, their classes represent concepts, and their methods represent actions that change state."*

#### 2. Paradigm Comparison Table

| Dimension | Imperative / Procedural Paradigm | Object-Oriented Simulation Paradigm |
| :--- | :--- | :--- |
| **View of Data** | Passive memory cells / data structures read and modified by active algorithms (`Data` is operated on by `Code`). | Active, self-governing entities (Objects) that encapsulate private state and expose behavioral contracts. |
| **View of Program Execution** | A sequence of instructions executed step-by-step to transition machine memory from initial state to final result. | A society of autonomous communicating agents sending messages and evolving their internal states over time. |
| **Role of Main Function** | The central master controller that drives every branch, loop, and step of the algorithm. | A bootstrapping mechanism: it instantiates the environment and objects, wires dependencies, and triggers the initial event. |
| **Primary Mechanism of Progress** | Direct function calls (`f(data, x)`). | Dynamic message dispatch (`object->receiveMessage(args)`). |

---

## 📌 Question 3: Grady Booch’s 5 Core Attributes of OOP

### Problem Statement
Grady Booch formulated the fundamental object model consisting of five core attributes:
1. **Abstraction**
2. **Encapsulation**
3. **Modularity**
4. **Hierarchy**
5. **Typing**

Define each attribute in one crisp sentence, and provide a concrete programming language construct demonstrating it.

---

### 💡 Model Solution

1. **Abstraction:**
   * *Definition:* The essential characteristics of an entity that distinguish it from all other kinds of entities, defining a boundary relative to the perspective of the viewer.
   * *Construct:* C++ pure abstract base class (`class IShape { virtual void draw() = 0; };`).

2. **Encapsulation (Information Hiding):**
   * *Definition:* The process of compartmentalizing the elements of an abstraction that constitute its structure and behavior, separating its external contractual interface from its internal implementation.
   * *Construct:* Access specifiers (`private: int balance;` accessed strictly through `public: int getBalance();`).

3. **Modularity:**
   * *Definition:* The property of a system that has been decomposed into a set of cohesive and loosely coupled units (modules) that can be compiled and maintained independently.
   * *Construct:* Translation units and header/source separation (`.h` and `.cpp` files, Java packages).

4. **Hierarchy:**
   * *Definition:* A ranking or ordering of abstractions, structured either as an "is-a" relationship (Inheritance/Generalization) or a "has-a" relationship (Aggregation/Composition).
   * *Construct:* Class inheritance (`class Dog : public Animal`) or member embedding (`class Car { Engine e; };`).

5. **Typing:**
   * *Definition:* The enforcement of the class of an object such that objects of different types may not be interchanged, or at most may be interchanged only in very restricted, well-defined ways.
   * *Construct:* Static type checking (`Dog* d = new Cat();` rejected at compile time) and polymorphic substitutability (Liskov Substitution Principle).

---

## 📌 Question 4: Parnas's Decomposition Criteria: Flowcharting vs Information Hiding

### Problem Statement
David L. Parnas published a seminal 1972 paper titled *"On the Criteria To Be Used in Decomposing Systems into Modules"*.
1. Contrast the traditional method of modular decomposition (**flowchart / step-by-step processing**) with Parnas's proposed criterion (**information hiding**).
2. Why does decomposition by flowchart steps lead to catastrophic maintenance costs when requirements change?

---

### 💡 Model Solution

#### 1. The Two Decomposition Criteria
* **Decomposition by Flowchart (Procedural Decomposition):**
  - The system is divided according to the temporal sequence of processing steps:
    `Module 1: Read Input` -> `Module 2: Parse Tokens` -> `Module 3: Sort Data` -> `Module 4: Format Output`.
  - Every module shares the common internal data representation (e.g., global arrays or shared memory structs).
* **Decomposition by Information Hiding (Parnas Criteria):**
  - Every module is designed to **encapsulate and hide a difficult design decision or a volatile requirement that is likely to change**.
  - Examples of hidden secrets: hardware storage format, sorting algorithm, file layout, network protocol.

#### 2. Why Flowchart Decomposition Fails Under Change
* When the data structure or storage format changes (e.g., moving from an in-memory array to a disk database or compressed tree):
  - In **flowchart decomposition**, because the data representation is shared across Module 1, Module 2, Module 3, and Module 4, **every single module in the system must be modified and retested**.
  - In **Parnas decomposition**, only the single module that encapsulates that data structure is modified. All other modules communicate via unchanged public interfaces and remain completely unaffected.
