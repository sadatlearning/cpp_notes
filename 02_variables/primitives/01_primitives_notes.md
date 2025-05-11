# Basic primitives

## The term **"basic primitives"** can mean different things depending on the context. 

- A breakdown of what it refers to in various domains:

### 🧮 **In Mathematics:**

* **Primitive operations**: Addition, subtraction, multiplication, division.
* **Primitive functions**: In calculus, a function whose derivative is a given function (also called an *antiderivative*).

### 💻 **In Computer Science / Programming:**

* **Primitive data types** (in most languages):

  * `int` – Integer
  * `float` / `double` – Floating-point numbers
  * `char` – Character
  * `bool` – Boolean
* **Primitive operations**:

  * Arithmetic (`+`, `-`, `*`, `/`)
  * Comparison (`<`, `==`, `>`)
  * Logical (`&&`, `||`, `!`)

### 🎨 **In Graphics / Geometry:**

* **Primitives**: Basic geometric shapes used to build more complex graphics.

  * Point
  * Line
  * Triangle
  * Quad
  * Polygon
  * In 3D: Cube, Sphere, Cylinder, etc.

### 📡 **In Networking / OS:**

* **Primitives**: Basic operations available in APIs (e.g., `send()`, `receive()`, `connect()`).

### 🔐 **In Cryptography:**

* **Cryptographic primitives**:

  * Hash functions (e.g., SHA-256)
  * Encryption algorithms (e.g., AES)
  * Digital signatures
  * Key exchange protocols

---

## In **Computer Science / Programming**, a system is **Turing complete** if it can simulate a **Turing machine**—meaning it can implement any algorithm that is computationally possible, provided enough time and memory.

### 🔧 **What Makes a Programming Language Turing Complete?**

It must support:

1. **Conditional branching** – `if`, `switch`, etc.
2. **Ability to change memory** – using variables or some form of storage.
3. **Loops or recursion** – `while`, `for`, or functions that call themselves.

If a language has these, it's theoretically capable of expressing **any computation**.

### ✅ **Turing Complete Programming Languages:**

* **Python**
* **C/C++**
* **Java**
* **JavaScript**
* **Rust**
* **Go**
* Even esoteric languages like **Brainfuck**, **Whitespace**, and **LOLCODE**

### 🚫 **Examples of Non-Turing Complete Languages/Systems:**

* **HTML** – Just describes content; no logic or memory.
* **CSS** – Styles elements, can’t perform computation or loops (though newer versions are getting close).
* **Regular Expressions** – Most implementations aren’t Turing complete, though some extended versions are.
* **SQL (basic)** – Lacks looping and general computation.

### 🤔 **Why Care About Turing Completeness?**

It’s mostly a theoretical concept, but it helps answer questions like:

* "Can this language solve every kind of programming problem?"
* "Is this scripting system expressive enough?"

**Important**: Turing completeness says *nothing about how easy or efficient* it is to solve a problem—just that it's *possible in principle*.

---
## A minimal example of turning a simple language into a Turing-complete one?

### A **minimal example** of turning a very simple language into a **Turing-complete** one. 
#### We’ll start with something *not* Turing complete and gradually add features until it is.

### 🧱 Step 1: Start with a Tiny Language (Not Turing Complete)

Imagine a toy language that only lets you print numbers:

```text
print 1
print 2
print 3
```

This can’t:

* Store values
* Make decisions
* Repeat

➡️ **Not Turing complete.**

### 🔄 Step 2: Add Variables and Assignments

```text
let x = 0
print x
x = x + 1
print x
```

Now it can store and update values—but still no decisions or loops.

➡️ Still **not Turing complete.**

### 🧠 Step 3: Add Conditional Statements

```text
let x = 1
if x > 0 then
    print x
end
```

Now we can make decisions.

Still **no loops or recursion**, so we can't compute arbitrarily.

➡️ Still **not quite Turing complete.**

### ♻️ Step 4: Add Loops — Boom! Turing Complete

```text
let x = 5
while x > 0 do
    print x
    x = x - 1
end
```

Now we can:

* Store state (variables)
* Make decisions (conditionals)
* Repeat (loops)

➡️ ✅ This language is now **Turing complete**!

### 🧠 Final Thought:

- A **Turing machine** is just a theoretical model with a tape (memory), a head (reads/writes), and a set of rules. 
- Any language that can simulate this (with memory + decisions + repetition) is Turing complete.
---
## To see a real example like how a language like Brainfuck reaches Turing completeness with just 8 symbols?
- Let’s explore **Brainfuck**, one of the simplest Turing-complete languages ever created.
- It proves that you don’t need complex syntax to achieve Turing completeness.

### 🧠 **Brainfuck Basics**

Brainfuck operates on:

* A tape (an array of memory cells, all initially 0)
* A data pointer (points to one memory cell at a time)
* 8 commands — that’s it!

### 🔧 **Brainfuck Commands:**

| Symbol | Meaning                                     |
| ------ | ------------------------------------------- |
| `>`    | Move pointer right                          |
| `<`    | Move pointer left                           |
| `+`    | Increment current cell                      |
| `-`    | Decrement current cell                      |
| `.`    | Output the character at current cell        |
| `,`    | Input a character and store in current cell |
| `[`    | Start loop (if cell == 0, jump to `]`)      |
| `]`    | End loop (if cell != 0, jump back to `[`)   |

### 🔁 **Loops, Memory, and Branching**

Brainfuck is Turing complete because:

* You can simulate memory with the tape.
* You can create conditionals using `[` and `]`.
* You can build any logic using combinations of the 8 commands.

### 📝 **Example: Print "A"**

```brainfuck
++++++++++       # Set cell to 10
[                
    >+++++++     # Multiply by 7 (10 × 7 = 70)
    <-
]                
>++              # Add 2 (70 + 2 = 72 → ASCII 'H')
.                # Print 'H'
```

You can build complex programs like loops, math, and even a **Brainfuck interpreter written in Brainfuck** — proving Turing completeness.

---

## A commented example of how to implement a small algorithm like multiplication in Brainfuck?

- Let’s walk through a **commented Brainfuck program** that performs **multiplication** of two numbers, `3 × 2`, and outputs the result (`6`) as an ASCII character (`'\x06'`).


### 🎯 **Goal:** Multiply 3 × 2 = 6 and print it

### 💡 **How It Works Conceptually**

* We'll store `3` in one cell, `2` in another.
* Then we’ll use a loop to add `3` to a result cell, two times.

### 🧠 **Memory Layout (Tape)**

| Cell Index | Purpose                 |
| ---------- | ----------------------- |
| 0          | Multiplier (2)          |
| 1          | Multiplicand (3)        |
| 2          | Result (0, accumulates) |
| 3+         | Temporary/working       |

### 🔢 **Brainfuck Code with Comments**

```brainfuck
++             Set cell 0 to 2       (multiplier)
> +++          Set cell 1 to 3       (multiplicand)
>              Move to cell 2        (result)
[              Loop begins (cell 0 - multiplier)
  <            Move to cell 1
  >            Move to cell 2
  +++          Add 3 (multiplicand) to result
  <            Move to cell 1
  <            Move to cell 0
  -            Decrement multiplier
]              Loop ends when multiplier = 0
>>.            Move to result cell and print (will print ASCII value 6)
```

### 🧪 **Step-by-Step Simulation**

Initial:

```
Cell 0: 2
Cell 1: 3
Cell 2: 0
```

Loop iteration 1:

* Add 3 to Cell 2 → now 3
* Decrease Cell 0 → now 1

Loop iteration 2:

* Add 3 to Cell 2 → now 6
* Decrease Cell 0 → now 0 → exit loop

Output ASCII of 6 (non-printable, but it's correct!).

### ✅ **Conclusion**

This shows how you can:

* Use variables (cells)
* Do loops (`[ ]`)
* Apply logic to manipulate memory

With just 8 symbols, **Brainfuck is Turing complete** — capable of expressing **any algorithm** with enough tape and time.

