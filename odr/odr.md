### 📌 **ODR (One Definition Rule) in C Language**

The **One Definition Rule (ODR)** in C ensures that variables and functions have **exactly one definition** across your entire program. If you violate this rule, the linker will throw **multiple definition** errors.

---

### 📖 **1. Understanding ODR in C**

In C, ODR applies to:
1. **Global Variables**: There must be **one definition** across all translation units.
2. **Functions**: Each function should be defined **only once** across all source files.
3. **`extern` Declarations**: You can declare a variable/function **multiple times**, but **define** it once.

---

### 📌 **2. ODR for Global Variables**

✅ **Correct Usage (One Definition + Multiple Declarations)**

**`main.c`**
```c
#include <stdio.h>

extern int global_var;  // Declaration (no memory allocated)

int main() {
    printf("Value: %d\n", global_var);
    return 0;
}
```

**`globals.c`**
```c
int global_var = 42;  // Definition (memory allocated)
```

✅ **Compilation and Execution:**
```bash
gcc main.c globals.c -o program
./program  # Output: Value: 42
```

🚨 **Wrong Usage (Multiple Definitions)**

**`file1.c`**
```c
int global_var = 10;  // Definition
```

**`file2.c`**
```c
int global_var = 20;  // Another Definition
```

❌ **Error:**
```bash
/usr/bin/ld: multiple definition of `global_var'
```

✅ **Fix:** Use `extern` in one file and **define** the variable in only **one** source file.

---

### 📌 **3. ODR for Functions**

✅ **Correct Usage (One Definition + Multiple Declarations)**

**`utils.h`** (Header File)
```c
#ifndef UTILS_H
#define UTILS_H

void print_message(); // Declaration

#endif
```

**`utils.c`** (Function Definition)
```c
#include <stdio.h>
#include "utils.h"

void print_message() { // Definition
    printf("Hello from utils!\n");
}
```

**`main.c`**
```c
#include "utils.h"

int main() {
    print_message();  // Works fine!
    return 0;
}
```

✅ **Compilation:**
```bash
gcc main.c utils.c -o program
```

🚨 **Wrong Usage (Multiple Definitions)**

If you define the same function **twice**:

**`file1.c`**
```c
void print_message() {
    printf("From file1\n");
}
```

**`file2.c`**
```c
void print_message() {
    printf("From file2\n");
}
```

❌ **Error:**
```bash
/usr/bin/ld: multiple definition of `print_message'
```

✅ **Fix:** Define the function **once** and declare it in a **header file**.

---

### 📌 **4. Static and ODR**

1. **Static Variables:**  
   `static` variables have **internal linkage**, so you can define a `static` variable with the **same name** in multiple files.

✅ **Example:**
```c
// file1.c
static int count = 10; // Independent copy

// file2.c
static int count = 20; // Another independent copy
```

2. **Static Functions:**  
   Functions declared with `static` are also **file-local**.

✅ **Example:**
```c
// file1.c
static void helper() { /* Only visible in file1.c */ }

// file2.c
static void helper() { /* Different helper in file2.c */ }
```

---

### 📌 **5. Common ODR Mistakes**

1. **Forgetting `extern` for Shared Variables**
   - Always use `extern` for variables shared across files.  
2. **Defining Functions in Header Files**
   - Only **declare** functions in headers, **define** them in source files.  
3. **Inline Functions** (C99 and later)
   - **`inline`** functions violate ODR if defined in multiple places without `static`.

Would you like to dive into more ODR pitfalls or discuss how it works in **C++** where ODR is more nuanced? 🚀
