# 📘 C Language Learning Guide

## 📗 1. *C Programming* – Ajay Mittal

### ✅ Overview

A comprehensive beginner-to-intermediate level book that explains the fundamentals of C programming with examples.

### 📚 Key Concepts

* **Structure of C Programs**
* **Data Types and Operators**
* **Control Statements**
* **Functions and Recursion**
* **Arrays and Strings**
* **Structures and Unions**
* **File Handling**
* **Dynamic Memory Allocation**

### 🧠 Best For:

* B.Tech/Engineering students
* Beginners in C programming

### 💡 Highlights

* Step-by-step coding examples
* Exercises at the end of each chapter
* Good coverage of `struct`, `union`, `enum`, `typedef`, and file operations

---

## 📘 2. *Understanding and Using C Pointers* – Richard Reese

### ✅ Overview

A specialized book that provides **in-depth understanding of C pointers**, crucial for systems programming and embedded development.

### 📚 Key Topics

* **Pointer Basics**:

  * Memory models
  * Pointer arithmetic
  * Pointer to pointer
* **Dynamic Memory Management**:

  * `malloc`, `calloc`, `realloc`, `free`
  * Avoiding memory leaks
* **Function Pointers**
* **Pointers and Arrays**:

  * Pointer-index interchangeability
* **Pointer to Structures**
* **Pointers in Data Structures**
* **Using pointers safely**

### 🧠 Best For:

* Intermediate to advanced C programmers
* Systems programmers
* Anyone preparing for **Advanced C Workshops** (e.g., CPA)

### 💡 Workshop Add-on:

> Recommended companion for **Advanced C Pointer Workshops** – includes exercises that test memory diagrams, tracing pointers, and custom allocators.

---

## 🧰 3. Data Structures Using Arrays

### ✅ Scope:

Focuses on **array-based data structure implementation** in C – fundamental to mastering low-level memory and data organization.

---

### 📦 3.1 Dynamically Resizable Array

#### 📚 Topics

* Implementing your own version of `vector`/`ArrayList`
* Reallocation strategy (`double` on overflow)
* Performance analysis of insert/delete
* Memory reallocation with `realloc`

#### 💡 Key Functions

```c
void init(Array* a);
void insert(Array* a, int value);
void resize(Array* a);
void delete(Array* a, int index);
```

---

### 📦 3.2 Array-Based Stack

#### 📚 Topics

* Stack operations: `push`, `pop`, `peek`
* Overflow and underflow handling
* Time complexity analysis: O(1)

#### 💡 Implementation Tip

Use a struct with `top` index and fixed/dynamic array.

```c
typedef struct {
    int top;
    int capacity;
    int* data;
} Stack;
```

---

### 📦 3.3 Array-Based Queue

#### 📚 Topics

* Circular Queue logic
* Enqueue, Dequeue operations
* Full vs Empty condition
* Fixed-size vs dynamic circular queues

```c
typedef struct {
    int front, rear, size;
    int capacity;
    int* data;
} Queue;
```

---

### 📦 3.4 Array-Based Deque

#### 📚 Topics

* Double-ended queue implementation
* Insert/Remove from both ends
* Circular buffer techniques
* Use cases in scheduling algorithms

---

## 🛠️ Recommended Hands-On Exercises

1. **Implement a dynamic array (vector-like)**
2. **Build a stack and queue using static and dynamic arrays**
3. **Simulate a task scheduler using a circular deque**
4. **Create a memory leak detector for pointer-based stack**

---

## 🎯 Final Tips for Mastery

* Trace all pointer-related code manually using diagrams
* Use tools like `valgrind` for memory debugging
* Practice memory layout questions (especially for pointers to arrays/structs)
* Practice all ADTs both using static and dynamic memory

