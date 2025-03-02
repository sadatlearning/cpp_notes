### **How Linked Lists Are Implemented in C++ (Deep Dive)**  

A **linked list** is a **dynamic data structure** consisting of nodes, where each node contains:  
1. **Data** – the actual value stored in the list.  
2. **Pointer(s)** – address of the next (and sometimes previous) node.  

There are **three main types** of linked lists:  
- **Singly Linked List** (one direction)  
- **Doubly Linked List** (both directions)  
- **Circular Linked List** (loops back to start)

---

## **1️⃣ How a Singly Linked List Works Internally**  
A **singly linked list** has **one pointer per node**, pointing to the next node.  

### **🔹 Node Structure**
```cpp
struct Node {
    int data;   // Data value
    Node* next; // Pointer to next node

    Node(int val) : data(val), next(nullptr) {}
};
```

🔹 **Example Linked List:**
```cpp
Head -> [10 | *] -> [20 | *] -> [30 | nullptr]
```
Each node has a **value** and a **pointer** to the next node.

---

### **🔹 Basic Implementation of Singly Linked List**
```cpp
#include <iostream>

struct Node {
    int data;
    Node* next;
    
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedList {
public:
    Node* head;

    LinkedList() : head(nullptr) {}

    // Insert at the end
    void insert(int val) {
        Node* newNode = new Node(val);
        if (!head) {
            head = newNode;
            return;
        }
        Node* temp = head;
        while (temp->next) {
            temp = temp->next;
        }
        temp->next = newNode;
    }

    // Print the list
    void print() {
        Node* temp = head;
        while (temp) {
            std::cout << temp->data << " -> ";
            temp = temp->next;
        }
        std::cout << "NULL\n";
    }
};

int main() {
    LinkedList list;
    list.insert(10);
    list.insert(20);
    list.insert(30);

    list.print();  // Output: 10 -> 20 -> 30 -> NULL
}
```
✔️ **Time Complexity**:  
- Insert at end: **O(n)** (traverses to last node)  
- Print: **O(n)**  

---

## **2️⃣ How a Doubly Linked List Works Internally**  
A **doubly linked list** has **two pointers per node**:  
1. `next` – points to the next node  
2. `prev` – points to the previous node  

🔹 **Example Doubly Linked List:**
```
NULL <- [10 | * | *] <-> [20 | * | *] <-> [30 | * | NULL]
```

### **🔹 Node Structure**
```cpp
struct Node {
    int data;
    Node* next;
    Node* prev;
    
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};
```

---

### **🔹 Basic Implementation of Doubly Linked List**
```cpp
#include <iostream>

struct Node {
    int data;
    Node* next;
    Node* prev;

    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};

class DoublyLinkedList {
public:
    Node* head;

    DoublyLinkedList() : head(nullptr) {}

    // Insert at end
    void insert(int val) {
        Node* newNode = new Node(val);
        if (!head) {
            head = newNode;
            return;
        }
        Node* temp = head;
        while (temp->next) {
            temp = temp->next;
        }
        temp->next = newNode;
        newNode->prev = temp;
    }

    // Print forward
    void printForward() {
        Node* temp = head;
        while (temp) {
            std::cout << temp->data << " <-> ";
            temp = temp->next;
        }
        std::cout << "NULL\n";
    }

    // Print backward
    void printBackward() {
        if (!head) return;
        Node* temp = head;
        while (temp->next) {
            temp = temp->next;
        }
        while (temp) {
            std::cout << temp->data << " <-> ";
            temp = temp->prev;
        }
        std::cout << "NULL\n";
    }
};

int main() {
    DoublyLinkedList list;
    list.insert(10);
    list.insert(20);
    list.insert(30);

    list.printForward();  // Output: 10 <-> 20 <-> 30 <-> NULL
    list.printBackward(); // Output: 30 <-> 20 <-> 10 <-> NULL
}
```
✔️ **Advantages of Doubly Linked List:**  
- **Can traverse both ways (`O(n)`)**  
- **Deleting a node is faster (`O(1)`)** if we already have a pointer to it  
- **Uses more memory (`O(2n)`)** because of extra `prev` pointer  

---

## **3️⃣ How a Circular Linked List Works Internally**  
A **circular linked list** is a variation where:  
✔️ The last node **points back to the first node**, instead of `nullptr`.

🔹 **Example Circular Singly Linked List:**
```
Head -> [10 | *] -> [20 | *] -> [30 | *] --\
             \___________________________/
```

### **🔹 Basic Implementation of Circular Singly Linked List**
```cpp
#include <iostream>

struct Node {
    int data;
    Node* next;

    Node(int val) : data(val), next(nullptr) {}
};

class CircularLinkedList {
public:
    Node* head;

    CircularLinkedList() : head(nullptr) {}

    // Insert at end
    void insert(int val) {
        Node* newNode = new Node(val);
        if (!head) {
            head = newNode;
            head->next = head;  // Circular link
            return;
        }
        Node* temp = head;
        while (temp->next != head) {  // Stop at last node
            temp = temp->next;
        }
        temp->next = newNode;
        newNode->next = head;  // Make it circular
    }

    // Print the circular list
    void print() {
        if (!head) return;
        Node* temp = head;
        do {
            std::cout << temp->data << " -> ";
            temp = temp->next;
        } while (temp != head);
        std::cout << "(back to head)\n";
    }
};

int main() {
    CircularLinkedList list;
    list.insert(10);
    list.insert(20);
    list.insert(30);

    list.print();  // Output: 10 -> 20 -> 30 -> (back to head)
}
```
✔️ **Advantages of Circular Linked List:**  
- **No `nullptr` elements** → **continuous looping**  
- **Can be used in scheduling algorithms (like round-robin CPU scheduling)**  

---

## **4️⃣ Comparing Different Linked List Types**
| Feature | Singly Linked List | Doubly Linked List | Circular Linked List |
|---------|-------------------|-------------------|---------------------|
| **Memory Usage** | `O(n)` | `O(2n)` (extra `prev` pointer) | `O(n)` |
| **Traversal** | One direction | Both directions | Continuous |
| **Insertion/Deletion** | `O(1)` at head, `O(n)` at end | `O(1)` anywhere if pointer available | `O(1)` at head or end |
| **Loop Detection** | Slow (`O(n)`) | Slow (`O(n)`) | Instant (`head->next == head`) |

✔️ **Use Singly Linked List for simple, lightweight storage.**  
✔️ **Use Doubly Linked List for bidirectional traversal.**  
✔️ **Use Circular Linked List for circular structures like buffer queues.**  

---

### **Would you like me to explain how STL `std::list` optimizes these operations?** 🚀



---

### **How `std::list` Works Internally in C++ (Deep Dive into STL Implementation)**  

Unlike `std::vector`, which uses **contiguous memory**, `std::list` is implemented as a **doubly linked list**. This means:  
✔️ **Fast insertions and deletions (`O(1)`)** at any position.  
❌ **Slow element access (`O(n)`)**, since you must traverse node by node.  

---

## **1️⃣ `std::list` Internal Implementation**
🔹 **Each node contains:**
1. **Value** (data stored in the list)  
2. **Pointer to the next node** (`next`)  
3. **Pointer to the previous node** (`prev`)  

### **🔹 Node Structure in STL (`std::list` uses doubly linked list)**
```cpp
template <typename T>
struct ListNode {
    T data;        // Stored value
    ListNode* next; // Pointer to next node
    ListNode* prev; // Pointer to previous node

    ListNode(T val) : data(val), next(nullptr), prev(nullptr) {}
};
```

🔹 **Example: Memory Layout of `std::list<int>`**
```
Head → [10 | * | *] ⇄ [20 | * | *] ⇄ [30 | * | nullptr]
```

- **Each node has a `prev` and `next` pointer.**
- **Traversal takes `O(n)` but insert/delete is `O(1)`.**

---

## **2️⃣ How `std::list` Stores Internal Data**
The **actual implementation of `std::list` in the STL (Standard Template Library) is more complex** than a basic linked list.  
Internally, `std::list` maintains a **sentinel node** (dummy node) to simplify operations.

### **🔹 Sentinel Node Concept**
The STL `std::list` implementation **does not use `nullptr`** for empty lists. Instead, it uses a **special "head" node** that always exists.

```
[Sentinel] ⇄ [10 | * | *] ⇄ [20 | * | *] ⇄ [30 | * | Sentinel]
```
✔️ **Advantages of Sentinel Node:**
- No need to check for `nullptr` when inserting/deleting.
- Easier to implement circular lists.

---

## **3️⃣ Internal Representation of `std::list`**
The `std::list` class in STL is implemented like this:

```cpp
template <typename T>
class List {
private:
    struct Node {
        T data;
        Node* next;
        Node* prev;
        Node(T val) : data(val), next(nullptr), prev(nullptr) {}
    };

    Node* sentinel;  // Dummy node for easier operations
    size_t size;     // Stores the number of elements

public:
    List() {
        sentinel = new Node(T()); // Create a dummy node
        sentinel->next = sentinel;
        sentinel->prev = sentinel;
        size = 0;
    }

    void push_back(T val) {
        Node* newNode = new Node(val);
        Node* last = sentinel->prev;

        last->next = newNode;
        newNode->prev = last;
        newNode->next = sentinel;
        sentinel->prev = newNode;

        size++;
    }

    void print() {
        Node* temp = sentinel->next;
        while (temp != sentinel) {
            std::cout << temp->data << " <-> ";
            temp = temp->next;
        }
        std::cout << "Sentinel\n";
    }
};
```
✔️ **STL `std::list` uses a circular doubly linked list with a sentinel node** to make operations faster.

---

## **4️⃣ Why `std::list` Iterators Never Get Invalidated**
In `std::vector`, iterators become **invalid** if the container resizes.  
However, in `std::list`, iterators remain **valid** even after insertions and deletions.

🔹 **Example: `std::vector` Iterator Invalidates**
```cpp
std::vector<int> vec = {10, 20, 30};
auto it = vec.begin();
vec.push_back(40); // Reallocation happens
std::cout << *it; // ⚠️ Undefined behavior (iterator invalid)
```
❌ **In `std::vector`, adding elements may invalidate iterators because memory is reallocated.**

🔹 **Example: `std::list` Iterator Stays Valid**
```cpp
std::list<int> lst = {10, 20, 30};
auto it = lst.begin();
lst.push_back(40); // Safe!
std::cout << *it;  // ✅ Works fine
```
✔️ **In `std::list`, iterators never become invalid after insertions or deletions!**

---

## **5️⃣ `std::list` Iterator Types**
The iterators of `std::list` are **bidirectional**, meaning they support:
- **Forward iteration (`++it`)**
- **Backward iteration (`--it`)**
- ❌ **No random access (`it + n` is not allowed)**

🔹 **Example: Using `std::list` Iterators**
```cpp
#include <iostream>
#include <list>

int main() {
    std::list<int> lst = {10, 20, 30};

    std::list<int>::iterator it = lst.begin();
    while (it != lst.end()) {
        std::cout << *it << " ";
        ++it;  // Move forward
    }

    return 0;
}
```
✔️ **List iterators work like node pointers.**  

---

## **6️⃣ `std::list` vs. `std::vector` Performance**
| Feature               | `std::vector` | `std::list` |
|----------------------|-------------|------------|
| **Memory Layout** | Contiguous (array) | Linked Nodes |
| **Access Time** | `O(1)` (fast) | `O(n)` (slow) |
| **Insert/Delete** | `O(n)` (slow) | `O(1)` (fast) |
| **Iterator Stability** | ❌ Invalidated | ✅ Always valid |
| **Random Access (`it + n`)** | ✅ Yes | ❌ No |

✔️ **Use `std::vector` for fast access.**  
✔️ **Use `std::list` for frequent insertions/deletions.**  

---

## **7️⃣ `std::list` Internal Optimizations**
### **🔹 Splice Optimization**
The `std::list::splice()` function allows **moving elements in `O(1)`, without copying**.

🔹 **Example: Moving elements from one list to another (Fast `O(1)`)**
```cpp
std::list<int> list1 = {1, 2, 3};
std::list<int> list2 = {4, 5, 6};

auto it = list1.begin();
std::advance(it, 2); // Move iterator to position

list2.splice(list2.begin(), list1, it);  // Moves '3' to list2

// list1: {1, 2}
// list2: {3, 4, 5, 6}
```
✔️ **`splice()` is an `O(1)` operation, unlike `vector`'s `O(n)` insert.**  

---

## **Final Takeaways**
✅ **`std::list` is implemented as a doubly linked list with a sentinel node.**  
✅ **Iterator stability makes it ideal for frequent insertions/deletions.**  
✅ **Random access (`it + n`) is NOT possible.**  
✅ **Use `splice()` for efficient list modifications (`O(1)`).**  

Would you like to see **how `std::list::sort()` is optimized compared to `std::vector::sort()`?** 🚀

---

### **How `std::list::sort()` Works Internally (Compared to `std::vector::sort()`)**  

Sorting is a crucial operation, and the way **linked lists (`std::list`) and dynamic arrays (`std::vector`) sort elements is fundamentally different** due to their underlying structures.

---

## **1️⃣ How `std::vector::sort()` Works (QuickSort/Introsort)**
### **🔹 Sorting a `std::vector`**
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {3, 1, 4, 1, 5, 9, 2};
    std::sort(vec.begin(), vec.end()); // Uses QuickSort / Introsort
    for (int x : vec) std::cout << x << " ";
}
```
🔹 **How it works internally:**  
✔️ `std::sort()` in `std::vector` uses **Introsort**, which is a hybrid sorting algorithm combining:
- **QuickSort** (for average-case speed `O(n log n)`)
- **HeapSort** (to avoid worst-case `O(n²)`)
- **InsertionSort** (for small subarrays)

✔️ **Why `std::vector::sort()` is fast?**  
- **Elements are stored in contiguous memory**, so sorting is cache-friendly.  
- **Random access (`vec[i]`) makes partitioning easy.**  
- **Works in-place**, meaning no extra memory is needed (`O(1)` additional space).  

❌ **Limitations:**  
- Every swap **moves elements in memory**, which is costly for large objects.  

---

## **2️⃣ How `std::list::sort()` Works (Merge Sort)**
### **🔹 Sorting a `std::list`**
```cpp
#include <iostream>
#include <list>

int main() {
    std::list<int> lst = {3, 1, 4, 1, 5, 9, 2};
    lst.sort(); // Uses Merge Sort internally
    for (int x : lst) std::cout << x << " ";
}
```
🔹 **How it works internally:**  
✔️ `std::list::sort()` uses **Merge Sort**, which is well-suited for linked lists.  

✔️ **Why Merge Sort for `std::list`?**  
- **Linked lists allow fast node splitting/merging (`O(1)`)**.  
- **No need to move elements in memory (only pointers change)**.  
- **Stable sorting**: keeps equal elements in their original order.  

---

## **3️⃣ Why `std::list::sort()` Uses Merge Sort**
### **🔹 Why not QuickSort?**
❌ **Partitioning in QuickSort requires random access (`it + n`), which `std::list` lacks.**  
❌ **Swapping in QuickSort moves actual data in memory, which is expensive for lists.**  

✔️ **Merge Sort is the best choice for `std::list` because:**
- **Works well with linked structures (only pointer operations, no shifting).**
- **Runs in `O(n log n)` time (same as QuickSort).**
- **`std::list` can split and merge sublists efficiently.**

### **🔹 Merge Sort Steps in `std::list::sort()`**
1️⃣ **Split the list into two halves (fast with `O(1)` pointer changes)**  
2️⃣ **Recursively sort each half (`O(log n)` depth)**  
3️⃣ **Merge the sorted halves (`O(n)`)**  

**Example (Sorting `{3, 1, 4, 1, 5, 9, 2}` using Merge Sort):**
```
Step 1: Split list
{3, 1, 4}      {1, 5, 9, 2}

Step 2: Sort each half
{1, 3, 4}      {1, 2, 5, 9}

Step 3: Merge
{1, 1, 2, 3, 4, 5, 9}
```

---

## **4️⃣ `std::list::sort()` vs. `std::vector::sort()` Performance**
| Feature               | `std::vector::sort()` (Introsort) | `std::list::sort()` (Merge Sort) |
|----------------------|---------------------------------|------------------------------|
| **Time Complexity**  | `O(n log n)`                    | `O(n log n)`                |
| **Best for**        | Small elements, fast access     | Large objects, frequent insert/delete |
| **Memory Usage**    | `O(1)` (in-place)               | `O(n)` (extra memory for merging) |
| **Stability**       | Not guaranteed                  | ✅ Stable (preserves order) |
| **Random Access?**  | ✅ Yes (`vec[i]`)               | ❌ No (`it + n` not possible) |
| **Speed**          | 🚀 **Faster** (`O(n log n)`)    | 🐢 **Slightly slower** (`O(n log n)`) |
| **Cache Efficiency** | ✅ Yes (contiguous memory)      | ❌ No (pointer traversal) |

---

## **5️⃣ Key Takeaways**
✅ **Use `std::vector::sort()` for fast sorting when data fits in memory.**  
✅ **Use `std::list::sort()` for large objects where copying is expensive.**  
✅ **`std::list::sort()` is stable, while `std::vector::sort()` is not guaranteed to be.**  
✅ **Merge Sort is optimal for linked lists because it avoids unnecessary memory moves.**  

Would you like a **step-by-step breakdown of Merge Sort working on a linked list?** 🚀

---

### **Step-by-Step Breakdown of Merge Sort on a Linked List (`std::list::sort()`)**  

Unlike `std::vector`, which sorts elements in-place using **Introsort**, `std::list` sorts elements using **Merge Sort**. This is because **Merge Sort efficiently works with linked lists without requiring random access**.

---

## **1️⃣ Why Merge Sort for `std::list`?**
✔ **Efficient for Linked Lists** → Only pointer changes, no element shifting.  
✔ **Stable Sorting** → Maintains the relative order of equal elements.  
✔ **`O(n log n)` Time Complexity** → Same as QuickSort but better for linked lists.  

❌ **QuickSort requires random access (`it + n`), which `std::list` lacks.**  

---

## **2️⃣ Merge Sort Steps for Linked List**
Merge Sort works by:
1. **Splitting the list into two halves** (using slow & fast pointer technique).  
2. **Recursively sorting each half.**  
3. **Merging the sorted halves into a single sorted list.**  

### **Example: Sorting `{4, 2, 1, 3}`**
```
Original List:   4 → 2 → 1 → 3
```
#### **Step 1: Split into two halves**
```
Left Half:  4 → 2
Right Half: 1 → 3
```
#### **Step 2: Recursively Sort Each Half**
Sorting `{4, 2}` results in `{2 → 4}`.  
Sorting `{1, 3}` results in `{1 → 3}`.  
```
Sorted Left:  2 → 4
Sorted Right: 1 → 3
```
#### **Step 3: Merge Two Sorted Lists**
```
2 → 4
1 → 3

Merge Step:
1 → 2 → 3 → 4  ✅ Sorted List!
```

---

## **3️⃣ Implementation of Merge Sort for Linked Lists**
Here’s how `std::list::sort()` internally works:

```cpp
#include <iostream>
using namespace std;

// Node structure for linked list
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};

// Function to split the list into two halves
ListNode* split(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head->next;

    // Move fast two steps, slow one step
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    ListNode* mid = slow->next;
    slow->next = nullptr; // Split into two lists
    return mid;
}

// Function to merge two sorted linked lists
ListNode* merge(ListNode* l1, ListNode* l2) {
    if (!l1) return l2;
    if (!l2) return l1;

    if (l1->val < l2->val) {
        l1->next = merge(l1->next, l2);
        return l1;
    } else {
        l2->next = merge(l1, l2->next);
        return l2;
    }
}

// Merge Sort function
ListNode* mergeSort(ListNode* head) {
    if (!head || !head->next) return head; // Base case: 1 or 0 elements

    ListNode* mid = split(head); // Split list
    ListNode* left = mergeSort(head);
    ListNode* right = mergeSort(mid);

    return merge(left, right); // Merge sorted halves
}

// Helper function to print linked list
void printList(ListNode* head) {
    while (head) {
        cout << head->val << " → ";
        head = head->next;
    }
    cout << "NULL\n";
}

int main() {
    ListNode* head = new ListNode(4);
    head->next = new ListNode(2);
    head->next->next = new ListNode(1);
    head->next->next->next = new ListNode(3);

    cout << "Before Sorting: ";
    printList(head);

    head = mergeSort(head);

    cout << "After Sorting:  ";
    printList(head);

    return 0;
}
```
---

## **4️⃣ Explanation of Code**
### **🔹 Step 1: Splitting the List (Fast & Slow Pointer)**
```cpp
ListNode* split(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head->next;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    ListNode* mid = slow->next;
    slow->next = nullptr;
    return mid;
}
```
✔ **Fast pointer moves twice as fast as slow pointer.**  
✔ When `fast` reaches the end, `slow` is at the middle.  

---

### **🔹 Step 2: Recursively Sorting Each Half**
```cpp
ListNode* left = mergeSort(head);
ListNode* right = mergeSort(mid);
```
✔ **Recursively calls `mergeSort()` on both halves.**  
✔ **Base case:** A list with `0` or `1` node is already sorted.  

---

### **🔹 Step 3: Merging Two Sorted Lists**
```cpp
ListNode* merge(ListNode* l1, ListNode* l2) {
    if (!l1) return l2;
    if (!l2) return l1;

    if (l1->val < l2->val) {
        l1->next = merge(l1->next, l2);
        return l1;
    } else {
        l2->next = merge(l1, l2->next);
        return l2;
    }
}
```
✔ **Compares values and merges recursively.**  
✔ **Pointer updates only—no data shifting (efficient for linked lists).**  

---

## **5️⃣ Complexity Analysis**
| Operation  | Time Complexity |
|------------|---------------|
| Splitting List | `O(n)` |
| Recursively Sorting Halves | `O(log n)` |
| Merging Sorted Lists | `O(n)` |
| **Total Complexity** | **`O(n log n)`** |

✔ **Same time complexity as QuickSort.**  
✔ **But better for linked lists because no element swapping is needed.**  

---

## **6️⃣ `std::list::sort()` vs. `std::vector::sort()`**
| Feature               | `std::vector::sort()` | `std::list::sort()` |
|----------------------|---------------------|------------------|
| **Sorting Algorithm** | QuickSort/Introsort | Merge Sort |
| **Time Complexity** | `O(n log n)` | `O(n log n)` |
| **Memory Usage** | `O(1)` (in-place) | `O(n)` (extra nodes) |
| **Random Access?** | ✅ Yes | ❌ No |
| **Swaps Elements?** | ✅ Yes (expensive for large objects) | ❌ No (only pointer changes) |
| **Stable Sorting?** | ❌ No guarantee | ✅ Yes |

✔ **Use `std::vector::sort()` for small elements and fast access.**  
✔ **Use `std::list::sort()` when dealing with large objects to avoid unnecessary swaps.**  

---

## **Final Takeaways**
✅ **`std::list::sort()` uses Merge Sort because it avoids expensive element movement.**  
✅ **Merge Sort works well for linked lists because only pointers change (`O(1)`).**  
✅ **The split operation (`O(n)`) and merge step (`O(n)`) give an overall `O(n log n)`.**  
✅ **Unlike `std::vector::sort()`, `std::list::sort()` is stable and does not require extra memory allocations.**  

Would you like a **visual step-by-step diagram of how Merge Sort operates on a linked list?** 🚀

---

### **🔹 Visual Step-by-Step Diagram of Merge Sort on a Linked List (`std::list::sort()`)**  

Let’s take an example **unsorted linked list**:  
```
Head → [4] → [2] → [1] → [3] → NULL
```
We will visualize how **Merge Sort** works step by step.

---

## **1️⃣ Step 1: Split the List into Two Halves**
To find the **middle**, we use the **fast & slow pointer** method:  
```
Fast moves twice as fast as Slow:
Slow → [4] → [2] → [1] → [3]
Fast → [4] → [1] → NULL  (reaches end)
```
At this point, **slow is at node `2`**, so we split:
```
Left Half:   [4] → [2] → NULL
Right Half:  [1] → [3] → NULL
```
🚀 **Now, we recursively sort both halves.**

---

## **2️⃣ Step 2: Recursively Sort the Left Half**  

Sorting `{4, 2}`:
```
Left Half:   [4] → NULL
Right Half:  [2] → NULL
```
🔹 **Base Case:** Each half has only one node (`O(1)`, already sorted).  

✅ **Merge `[4]` and `[2]`:**  
```
Result:  [2] → [4] → NULL
```

---

## **3️⃣ Step 3: Recursively Sort the Right Half**  

Sorting `{1, 3}`:
```
Left Half:   [1] → NULL
Right Half:  [3] → NULL
```
🔹 **Base Case:** Each half has only one node (`O(1)`, already sorted).  

✅ **Merge `[1]` and `[3]`:**  
```
Result:  [1] → [3] → NULL
```

---

## **4️⃣ Step 4: Merge Two Sorted Lists**
Now we **merge** `{2 → 4}` and `{1 → 3}`.

🛠 **Compare first elements**:
```
1 < 2 → Take [1]
```
🛠 **Compare remaining**:
```
2 < 3 → Take [2]
```
🛠 **Compare remaining**:
```
3 < 4 → Take [3]
```
🛠 **Last element**:
```
Take [4]
```

✅ **Final Sorted List:**  
```
Head → [1] → [2] → [3] → [4] → NULL
```

---

## **🔹 Complete Merge Sort Visualization**
```
Original List:   4 → 2 → 1 → 3
Split:           (4 → 2)      (1 → 3)
Sort Left:       (2 → 4)      (1 → 3)
Merge:           1 → 2 → 3 → 4
```

🔹 **Each recursive step takes `O(n)`, and there are `log n` splits.**
🔹 **Final time complexity: `O(n log n)`.**

---

## **🎨 Visual Diagram**
Here’s a step-by-step breakdown:

```
Step 1: Split into halves
  4 → 2 → 1 → 3
  /        \
(4 → 2)   (1 → 3)

Step 2: Split again
  4 → 2     1 → 3
 /    \    /    \
4      2  1      3

Step 3: Merge sorted halves
  (2 → 4)    (1 → 3)

Step 4: Final merge
  1 → 2 → 3 → 4
```

---

### **✅ Final Summary**
- **`std::list::sort()` uses Merge Sort** instead of QuickSort.
- **Splitting the list (`O(n)`) and merging (`O(n)`) lead to `O(n log n)` complexity.**
- **Only pointer changes, no element swapping (efficient for large objects).**
- **Stable sorting (preserves the order of equal elements).**

🚀 **Would you like a C++ program to visualize each step dynamically?**
