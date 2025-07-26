# 📘 System Programming & Operating Systems Study Guide

---

## 🧩 System Call Interface & Linux System Programming

### 📘 1. *Linux System Programming* — Robert Love

* Covers **system calls**, **file descriptors**, **process control**, **threads**, and **signals**.
* Great for learning how **user-space interacts with the kernel**.
* 🧠 **Recommended for intermediate learners** with basic C knowledge.

---

### 📘 2. *Advanced Programming in the UNIX Environment* — W. Richard Stevens

* Often abbreviated as **APUE**.
* Classic and **most comprehensive guide** to UNIX programming concepts.
* Includes topics like:

  * File I/O
  * Process relationships
  * Daemons
  * Terminal I/O
  * Inter-process communication (IPC)

📌 *Alternative*:
**Linux System Programming** by **Kiwan Billimoria** – Practical and concise.
✅ **Recommended if you prefer structured, hands-on explanations**.

---

### 📘 3. *The Linux Programming Interface (TLPI)* — Michael Kerrisk

* Considered **the Bible of Linux system programming**.
* Must-read for serious Linux developers.
* Covers:

  * System calls in extreme detail
  * Process lifecycle
  * File I/O, signals, sockets, threads, memory

> 🟡 **Start this book after mastering C language basics.**

---

## 🖥️ Operating System Concepts & Kernel Internals

### 📘 4. *Operating System Concepts* — Abraham Silberschatz

* A theoretical introduction to OS design.
* Covers:

  * Process management
  * Memory management
  * Scheduling algorithms
  * File systems
* Great for foundational OS concepts.

---

### 📘 5. *Modern Operating Systems* — Andrew S. Tanenbaum

* Academic and theoretical, written by the MINIX OS creator.
* Topics include:

  * Virtual memory
  * File systems
  * IPC
  * Distributed systems
* Good complement to hands-on Linux kernel learning.

---

### 📘 6. *Computer Organization* — Carl Hamacher / Morris Mano / M. Morris R. Mano / Zaky

* Focuses on:

  * Instruction cycles
  * Assembly language
  * Memory hierarchy
* Crucial for understanding low-level OS and kernel interactions.

> 🟢 **Choose Zaky or Stallings based on availability and clarity.**

---

## 🧠 Linux Kernel Programming

### 📘 7. *Linux Kernel Programming Guide*

* Introductory material for **understanding kernel modules**.
* Basics of kernel-space development.
* Good for early exploration.

---

### 📘 8. *Linux Kernel Development* — Robert Love

* A hands-on guide to understanding and modifying the Linux kernel.
* Covers:

  * Kernel data structures
  * Process scheduling
  * Synchronization mechanisms
* ⚙️ **Highly recommended before diving into kernel source code**.

---

## 🧵 Memory, Process, and Interrupts – In-Depth

### 📘 9. *Understanding the Linux Kernel (2nd Edition)* — Daniel P. Bovet, Marco Cesati

* Deep dive into how Linux kernel works internally.
* Recommended chapters:

  * **Chapter 2**: Memory Management
  * **Chapter 3**: Processes
  * **Chapter 4**: Interrupt Handling

> 📌 **Advanced learners only. Requires C and OS concepts as prerequisites.**

---

## 💾 Assembly & Architecture Background (Optional, for Deeper Understanding)

### 📘 10. *IBM PC Assembly Language and Programming* — Peter Abel

* For understanding **DOS environment**, low-level I/O, and system boot.
* Complements kernel and system programming by giving architecture insight.

---

### 📘 11. *Advanced 80386 Programming* — James Turley

* Dive into x86 protected mode programming.
* Learn segmentation, paging, and low-level processor operations.

> 🧠 **Essential if you're building or modifying bootloaders or working with early Linux startup code.**

---

## ✅ Suggested Study Flow

```markdown
1. Start with: 
   - C Programming Fundamentals
2. Move to:
   - Linux System Programming (Robert Love / Kiwan Billimoria)
   - APUE (W. Richard Stevens)
3. Parallel Theory:
   - Operating System Concepts (Silberschatz) or Modern OS (Tanenbaum)
   - Computer Organization (Zaky)
4. Kernel Programming:
   - Linux Kernel Programming Guide
   - Linux Kernel Development (Love)
5. Deep Dive:
   - TLPI (Michael Kerrisk)
   - Understanding the Linux Kernel (select chapters)
6. Optional Architecture:
   - Peter Abel’s Assembly
   - Advanced 80386 Programming
```
