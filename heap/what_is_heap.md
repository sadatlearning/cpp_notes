## **What is Heap Memory?**

- The **heap** is a special section of memory in a program's address space that is used for **dynamic memory allocation**. 
- It is separate from other memory regions like the **stack, code, and data sections**.

### **Where is the Heap Located?**
- When a C program runs, the operating system provides it with a structured memory layout.
- This layout typically includes:

1. **Text (Code) Segment** → Stores compiled program instructions.  
2. **Data Segment**  
   - **Initialized Data** → Stores global/static variables with values assigned before execution.  
   - **BSS (Block Started by Symbol)** → Stores uninitialized global/static variables.  
3. **Heap** → **Grows upward (towards higher memory addresses) when memory is allocated dynamically.**  
4. **Stack** → **Grows downward (towards lower memory addresses) when functions call each other and store local variables.**  

📌 The **heap is allocated from a section of RAM managed by the OS**. When you use `malloc()` or `calloc()`, the C runtime library requests memory from the OS, 
which usually extends the heap using system calls like `sbrk()` or `mmap()`.

### **Heap in Action**
Let’s visualize the memory layout of a running C program:

```
Memory Layout:
--------------------------------
|       Stack (↓ Growth)       |  <- High memory address
|------------------------------|
|        Heap (↑ Growth)       |
|------------------------------|
|   BSS (Uninitialized Data)   |
|------------------------------|
|   Data (Initialized Data)    |
|------------------------------|
|   Code (Program Instructions)|
--------------------------------
```  

🔹 The heap is dynamically managed, meaning it **grows** when memory is allocated and **shrinks** when memory is freed.


### **How is Heap Allocated in C?**
The heap is allocated when you use **dynamic memory allocation functions**, like:

| Function | Purpose |
|----------|---------|
| `malloc()`  | Allocates memory but does not initialize it. |
| `calloc()`  | Allocates and initializes memory to zero. |
| `realloc()` | Resizes previously allocated memory. |
| `free()`    | Releases allocated memory back to the OS. |

#### **Example Code**
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr = (int *)malloc(sizeof(int)); // Allocating 4 bytes on heap

    if (ptr == NULL) {
        printf("Memory allocation failed\n");
        return 1;
    }

    *ptr = 42; // Using heap memory
    printf("Heap memory value: %d\n", *ptr);

    free(ptr); // Freeing heap memory
    return 0;
}
```

### **How does the OS allocate the heap?**
1. **When you call `malloc()`** → The C runtime library requests memory from the OS.
2. **The OS extends the heap** using system calls like:
   - `sbrk(size)` → Expands the heap by `size` bytes.
   - `mmap()` → Used for larger allocations, maps new memory pages.
3. **The memory is returned to the program**.
4. **When `free()` is called**, the memory is marked as available for future allocations.


### **Key Takeaways**
✅ The heap is a **separate section of memory** used for **dynamic allocation**.  
✅ It is allocated **from RAM by the OS**, usually through `sbrk()` or `mmap()`.  
✅ **Heap memory grows upwards**, unlike the stack which grows downwards.  
✅ You must **manually free heap memory**; otherwise, it leads to **memory leaks**.  

- Check out -> deeper look at system calls like `sbrk()`? 🚀

---

## The **`sbrk()`** system call

- Let's dive deeper into how the heap is allocated in C using the **`sbrk()` system call**.

## **🔹 What is `sbrk()`?**
`sbrk()` is a **low-level system call** used to **increase or decrease** the program's heap size. It changes the program’s "break" (end of the heap segment) to allocate more memory.

📌 **Prototype:**
```c
void *sbrk(intptr_t increment);
```
- `increment` → The number of bytes to expand (or shrink) the heap.
- Returns the **previous** program break (i.e., the start of the newly allocated memory).
- Returns `(void *) -1` on failure.

**⚠️ Important:**
- `sbrk()` directly manipulates the heap, but it is rarely used in modern C programming because `malloc()` and `free()` manage memory better.
- Some modern OS implementations **disable `sbrk()`** in favor of `mmap()` for security reasons.

## **🔹 Example: Using `sbrk()` to Allocate Memory**
```c
#include <stdio.h>
#include <unistd.h>

int main() {
    void *initial_brk = sbrk(0); // Get current heap end
    printf("Initial program break: %p\n", initial_brk);

    void *new_brk = sbrk(10); // Increase heap by 10 bytes
    printf("New program break: %p\n", sbrk(0));

    // Freeing memory using sbrk() is tricky, so we avoid shrinking.
    return 0;
}
```
### **📌 Output (Memory Addresses Vary)**
```
Initial program break: 0x5612ab400000
New program break: 0x5612ab40000a
```
🔹 The heap size increased by **10 bytes**.

## **🔹 How does `malloc()` Use `sbrk()`?**
Internally, `malloc()` uses `sbrk()` **or** `mmap()` (for large allocations). It manages a pool of free memory and only calls `sbrk()` when more space is needed.

### **How `malloc()` Works Internally**
1. **Checks if free memory is available** in the heap.
2. If not enough memory is free, it **expands the heap** using `sbrk()`.
3. Returns a pointer to the newly allocated memory.

🔹 Example Simulation:
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    printf("Heap before malloc: %p\n", sbrk(0));
    
    int *ptr = (int *)malloc(4); // Allocating 4 bytes
    printf("Heap after malloc: %p\n", sbrk(0));

    free(ptr); // Freeing memory (does not shrink heap!)
    
    return 0;
}
```
✅ **`malloc()` internally calls `sbrk()` to expand the heap.**  
✅ **`free()` does not shrink the heap; it only marks memory as available.**

## **🔹 Why `sbrk()` is Not Commonly Used?**
🔹 **Manual Memory Management is Risky**  
   - You must track memory yourself (like `malloc()` but without automatic management).  

🔹 **Fragmentation Issues**  
   - Allocating and freeing memory frequently leads to fragmentation, where the heap has small free blocks that can't be reused.

🔹 **Security & Modern Systems Use `mmap()` Instead**  
   - Newer systems use `mmap()` to allocate large blocks efficiently.  
   - `sbrk()` changes the program’s heap boundary, which is unsafe in multi-threaded environments.

## **🔹 `sbrk()` vs. `mmap()`**
| Feature | `sbrk()` | `mmap()` |
|---------|---------|---------|
| Expands Heap? | Yes | No, allocates separate memory pages |
| Used by `malloc()`? | Yes (small allocations) | Yes (large allocations) |
| Multi-thread Safe? | ❌ No | ✅ Yes |
| Supports Freeing? | ❌ No | ✅ Yes (`munmap()`) |

📌 Modern memory allocators prefer `mmap()` because:
- It allows fine-grained control.
- It supports freeing memory properly (`munmap()`).
- It works well in multi-threaded programs.

## **🔹 Final Takeaways**
✅ **`sbrk()` extends the heap** but is rarely used directly in modern C programs.  
✅ **`malloc()` internally uses `sbrk()` or `mmap()`** to allocate memory dynamically.  
✅ **Modern memory allocation prefers `mmap()`** for large allocations due to security and performance benefits.  

Check out -> to see how `mmap()` works? 🚀

---
## What is Heap?

The **heap** is a region of a computer's memory used for **dynamic memory allocation**. 
It is managed by the operating system and allows programs to allocate and deallocate memory at runtime as needed.

### **Key Characteristics of the Heap**
1. **Used for Dynamic Memory Allocation**  
   - Memory is allocated on the heap using functions like `malloc()`, `calloc()`, and `realloc()`, and freed using `free()`.
   
2. **Larger than the Stack**  
   - The heap generally has more space available than the stack and can grow as needed (subject to system limitations).

3. **Persists Beyond Function Calls**  
   - Unlike stack memory, which is automatically reclaimed when a function returns, heap memory remains allocated until explicitly freed.

4. **Managed with Pointers**  
   - Since the heap does not automatically manage memory like the stack, pointers are used to access allocated memory.

### **Heap vs. Stack**
| Feature  | Heap  | Stack  |
|----------|-------|--------|
| **Memory Management** | Manual (using `malloc()`/`free()`) | Automatic (managed by function calls) |
| **Size** | Large (but limited by system memory) | Small (fixed size, faster access) |
| **Lifetime** | Persists until `free()` is called | Freed automatically when function exits |
| **Speed** | Slower (requires pointer access and management) | Faster (uses contiguous memory) |

### **Example: Allocating Memory on the Heap**
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    // Allocating memory for an integer on the heap
    int *ptr = (int *)malloc(sizeof(int));

    if (ptr == NULL) {
        printf("Memory allocation failed!\n");
        return 1;
    }

    *ptr = 10; // Storing a value in heap memory
    printf("Heap value: %d\n", *ptr);

    free(ptr); // Freeing allocated memory

    return 0;
}
```
### **Why Use the Heap?**
- When you need memory to **persist beyond function calls**.
- When the size of memory needed is **unknown at compile time**.
- When allocating large amounts of memory that **would not fit in the stack**.

---

## Heap Section
- In C, the **heap section** (or simply "heap") is a region of memory used for **dynamic memory allocation**.
- This means that memory can be allocated and freed manually at runtime using functions from the `<stdlib.h>` library.

### **Key Features of the Heap Section**
1. **Dynamically Allocated Memory**  
   - Unlike the **stack**, where memory is automatically allocated and deallocated, memory in the heap must be managed manually.
   - Functions like `malloc()`, `calloc()`, `realloc()`, and `free()` are used to allocate and deallocate memory in the heap.

2. **Larger and More Flexible**  
   - The heap is generally much larger than the stack.
   - It allows for variable-sized memory allocation, which is useful when the required memory size is not known at compile time.

3. **Persists Beyond Function Calls**  
   - Memory allocated on the heap remains valid until explicitly freed, even after a function call has ended.

4. **Slower Access than Stack**  
   - Since heap memory is dynamically managed and requires pointer dereferencing, accessing it is generally slower than accessing stack memory.

### **Example of Heap Memory Allocation in C**
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    // Allocating memory for an integer on the heap
    int *ptr = (int *)malloc(sizeof(int)); 

    if (ptr == NULL) {
        printf("Memory allocation failed!\n");
        return 1;
    }

    *ptr = 42;
    printf("Value stored in heap: %d\n", *ptr);

    // Freeing the allocated memory
    free(ptr);

    return 0;
}
```

### **Common Issues with Heap Memory**
1. **Memory Leaks**  
   - Forgetting to call `free()` results in unused memory staying allocated, leading to memory leaks.
   
2. **Dangling Pointers**  
   - Using memory after it has been freed can cause undefined behavior.
   
3. **Fragmentation**  
   - If memory is allocated and freed repeatedly in different sizes, fragmentation can occur, reducing efficiency.

---


## **🔹 What is `mmap()`?**
`mmap()` is a **system call** that maps files or anonymous memory into a program’s address space. Unlike `sbrk()`, which expands the heap, `mmap()` can allocate memory **anywhere in the virtual address space**, making it **more flexible and efficient**.

### **📌 Prototype of `mmap()`**
```c
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>

void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
```
| Parameter | Description |
|-----------|------------|
| `addr` | Suggested memory address (usually `NULL` to let the OS decide). |
| `length` | Number of bytes to allocate. |
| `prot` | Memory protection (e.g., `PROT_READ`, `PROT_WRITE`). |
| `flags` | Allocation type (`MAP_ANONYMOUS` for heap-like memory). |
| `fd` | File descriptor (use `-1` for anonymous memory). |
| `offset` | File offset (use `0` for anonymous memory). |

### **🔹 How `malloc()` Uses `mmap()`**
- **For small allocations** → `malloc()` calls `sbrk()`.  
- **For large allocations (>128 KB)** → `malloc()` uses `mmap()` to prevent fragmentation.  
- **For freeing large blocks** → `free()` calls `munmap()` instead of keeping memory in the heap.

## **🔹 Allocating Memory Using `mmap()`**
Here’s an example of using `mmap()` to allocate memory without using `malloc()`:

```c
#include <stdio.h>
#include <sys/mman.h>
#include <unistd.h>

int main() {
    size_t size = 4096; // 4 KB (page size)
    
    // Allocate memory using mmap()
    void *ptr = mmap(NULL, size, PROT_READ | PROT_WRITE,
                     MAP_ANONYMOUS | MAP_PRIVATE, -1, 0);
    
    if (ptr == MAP_FAILED) {
        perror("mmap failed");
        return 1;
    }

    printf("Memory allocated at: %p\n", ptr);

    // Store data
    int *arr = (int *)ptr;
    arr[0] = 42;
    printf("Value stored: %d\n", arr[0]);

    // Free memory using munmap()
    if (munmap(ptr, size) == -1) {
        perror("munmap failed");
        return 1;
    }

    printf("Memory freed!\n");
    return 0;
}
```

### **📌 Explanation**
1. **Allocate 4 KB (1 page) using `mmap()`**.
2. **Write and read a value in the allocated memory**.
3. **Free memory using `munmap()`**.

## **🔹 How `mmap()` is Better Than `sbrk()`**
| Feature | `sbrk()` | `mmap()` |
|---------|---------|---------|
| Expands Heap? | ✅ Yes | ❌ No, allocates anywhere |
| Used by `malloc()`? | ✅ Yes (small allocations) | ✅ Yes (large allocations) |
| Multi-thread Safe? | ❌ No | ✅ Yes |
| Supports Freeing? | ❌ No (heap only grows) | ✅ Yes (`munmap()`) |
| Fragmentation? | ⚠️ High | ✅ Low |

✅ **`mmap()` is more modern, safer, and supports freeing memory properly!**  
✅ **`malloc()` secretly uses `mmap()` for large allocations!**  

## **🔹 Final Thoughts**
- `sbrk()` expands the heap but **cannot free memory**.
- `mmap()` allocates **anywhere** in memory and can be **freed**.
- Modern memory managers **prefer `mmap()`** for large allocations.

Would you like to explore how `malloc()` dynamically switches between `sbrk()` and `mmap()`? 🚀

---

## Let's write a **real-time memory usage monitor** for a running C program.  

### **🔹 How to Measure Memory Usage of a Process in Real-Time?**  
Every process in Linux has a **status file** under `/proc/<PID>/status`, which contains information about memory usage.  

🔹 **Key fields in `/proc/<PID>/status`**:
- **`VmSize`** → Total virtual memory allocated.
- **`VmRSS`** → Actual RAM (Resident Set Size) used.
- **`VmData`** → Heap memory usage.
- **`VmStk`** → Stack memory usage.

## **🔹 C Program to Monitor Its Own Memory Usage**
This program reads memory usage data from `/proc/self/status` in real time.  

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

void print_memory_usage() {
    FILE *fp = fopen("/proc/self/status", "r");
    if (!fp) {
        perror("Error opening /proc/self/status");
        return;
    }

    char line[256];
    while (fgets(line, sizeof(line), fp)) {
        if (strncmp(line, "VmSize:", 7) == 0 ||   // Total virtual memory
            strncmp(line, "VmRSS:", 6) == 0 ||    // Resident memory (RAM)
            strncmp(line, "VmData:", 7) == 0 ||   // Heap size
            strncmp(line, "VmStk:", 6) == 0) {    // Stack size
            printf("%s", line);
        }
    }

    fclose(fp);
}

int main() {
    printf("Memory usage before allocation:\n");
    print_memory_usage();
    sleep(2);

    // Allocate 10MB dynamically
    size_t size = 10 * 1024 * 1024;
    void *ptr = malloc(size);
    if (!ptr) {
        perror("Memory allocation failed");
        return 1;
    }

    printf("\nMemory usage after 10MB allocation:\n");
    print_memory_usage();
    sleep(2);

    // Free memory
    free(ptr);
    printf("\nMemory usage after freeing memory:\n");
    print_memory_usage();

    return 0;
}
```

## **🔹 How the Program Works**
1. **Before allocation** → Prints the initial memory usage.  
2. **After allocating 10MB** → Prints memory usage after dynamic allocation (`malloc()`).  
3. **After freeing memory** → Prints memory usage again after `free()`.  


## **🔹 Expected Output (Varies by System)**
```
Memory usage before allocation:
VmSize:     10240 kB
VmRSS:       5000 kB
VmData:      2048 kB
VmStk:         132 kB

Memory usage after 10MB allocation:
VmSize:     20480 kB
VmRSS:      15000 kB
VmData:     12288 kB
VmStk:         132 kB

Memory usage after freeing memory:
VmSize:     10240 kB
VmRSS:       5000 kB
VmData:      2048 kB
VmStk:         132 kB
```
✅ **Confirms that memory increases after allocation and returns to normal after `free()`.**  

Would you like to extend this to **continuously monitor memory usage over time**? 🚀

---

Great! Let's modify the program to **continuously monitor memory usage** at regular intervals.  

---

## **🔹 Continuous Real-Time Memory Monitoring in C**
This program will **loop infinitely** and print memory usage every second.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

void print_memory_usage() {
    FILE *fp = fopen("/proc/self/status", "r");
    if (!fp) {
        perror("Error opening /proc/self/status");
        return;
    }

    char line[256];
    while (fgets(line, sizeof(line), fp)) {
        if (strncmp(line, "VmSize:", 7) == 0 ||   // Total virtual memory
            strncmp(line, "VmRSS:", 6) == 0 ||    // Resident memory (RAM)
            strncmp(line, "VmData:", 7) == 0 ||   // Heap size
            strncmp(line, "VmStk:", 6) == 0) {    // Stack size
            printf("%s", line);
        }
    }

    fclose(fp);
}

int main() {
    printf("Continuous Memory Monitoring (Press Ctrl+C to stop)\n");

    while (1) {
        printf("\nMemory usage at time: %ld seconds\n", time(NULL));
        print_memory_usage();
        sleep(1); // Wait for 1 second before checking again
    }

    return 0;
}
```

---

## **🔹 How This Works**
1. The program runs **infinitely** (until you press `Ctrl+C` to stop it).
2. It reads memory details from `/proc/self/status` every **1 second**.
3. It prints:
   - **VmSize** → Total memory allocated by the process.
   - **VmRSS** → Actual RAM used by the process.
   - **VmData** → Heap memory usage.
   - **VmStk** → Stack memory usage.

---

## **🔹 Example Output (Live Memory Monitoring)**
```
Continuous Memory Monitoring (Press Ctrl+C to stop)

Memory usage at time: 1712403725 seconds
VmSize:     10240 kB
VmRSS:       5000 kB
VmData:      2048 kB
VmStk:         132 kB

Memory usage at time: 1712403726 seconds
VmSize:     10240 kB
VmRSS:       5000 kB
VmData:      2048 kB
VmStk:         132 kB

Memory usage at time: 1712403727 seconds
VmSize:     10240 kB
VmRSS:       5000 kB
VmData:      2048 kB
VmStk:         132 kB
```
✅ Keeps updating every second!  
✅ You can **observe memory changes live** when allocating/freeing memory.  

---

## **🔹 Extend This Program**
- **Log memory usage to a file** instead of printing to the screen.
- **Track a specific process** (e.g., monitor another program’s memory by its PID).
- **Visualize memory usage** using Python (plot graphs using Matplotlib).  

Would you like help adding **logging** or **tracking another process**? 🚀
