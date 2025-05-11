## ✅ Learning Objectives

By the end of this guide, you’ll understand:

1. What is **deep copy** vs **shallow copy**.
2. How to implement:

   * **Copy Constructor**
   * **Copy Assignment Operator**
   * **Destructor**
3. Why these are necessary when your class contains **heap-allocated memory** (e.g., pointers).

---

## 🔍 1. What is Copying?

### 🔹 Shallow Copy

A **shallow copy** copies the pointer address — both objects point to the **same memory**.

### 🔹 Deep Copy

A **deep copy** creates a **new memory allocation** and copies values into it — objects are **independent**.

---

## 🔧 Example Problem

You're managing a class `Person` which holds a pointer to `char* name` (dynamically allocated). You need to avoid two objects sharing the same memory location.

---

## 📘 Learning Plan with Code Examples

### ✅ Step 1: Define a Simple Class with a Pointer

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Person {
    char* name;

public:
    // Constructor
    Person(const char* n) {
        name = new char[strlen(n) + 1];
        strcpy(name, n);
        cout << "Constructor called\n";
    }

    // Copy Constructor (Deep Copy)
    Person(const Person& p) {
        name = new char[strlen(p.name) + 1];
        strcpy(name, p.name);
        cout << "Copy Constructor called\n";
    }

    // Copy Assignment Operator (Deep Copy)
    Person& operator=(const Person& p) {
        cout << "Copy Assignment Operator called\n";
        if (this != &p) {
            delete[] name;  // Free existing memory
            name = new char[strlen(p.name) + 1];
            strcpy(name, p.name);
        }
        return *this;
    }

    // Destructor
    ~Person() {
        cout << "Destructor called for: " << name << endl;
        delete[] name;
    }

    void show() {
        cout << "Name: " << name << endl;
    }
};
```

---

### ✅ Step 2: Test the Behavior

```cpp
int main() {
    Person p1("Alice");
    Person p2 = p1;             // Calls copy constructor
    Person p3("Bob");
    p3 = p1;                    // Calls copy assignment operator

    p1.show();
    p2.show();
    p3.show();
    return 0;
}
```

---

## 📝 Notes

### 📌 Destructor

* Used to **free memory** to prevent leaks.
* Always needed when using `new`.

### 📌 Copy Constructor

* Triggered on:

  * Object copy by initialization: `Person p2 = p1;`
  * Passing object **by value**

### 📌 Copy Assignment Operator

* Triggered when assigning an object: `p3 = p1;`

### ⚠️ If You Don't Implement These:

* Default versions will **shallow copy** → two objects will point to the **same memory** → double deletion crash.

---

## 🚀 Learning Schedule

| Day | Topic                    | Practice Task                                                 |
| --- | ------------------------ | ------------------------------------------------------------- |
| 1   | Constructor & Destructor | Create a class with `char* data`, use `new/delete`            |
| 2   | Shallow vs Deep Copy     | Create both shallow and deep copy classes, observe destructor |
| 3   | Copy Constructor         | Implement and test deep copy constructor                      |
| 4   | Copy Assignment Operator | Add assignment operator and test all 3                        |
| 5   | Edge Cases               | Handle self-assignment `if (this != &rhs)`                    |
| 6   | Debug                    | Use print logs inside constructors/destructors                |
| 7   | Final Review             | Build small class with all rules (Rule of 3)                  |

---

## 💡 Tip

In modern C++, use `std::string`, `std::vector`, etc., that handle memory automatically. But it's critical to learn these basics for understanding object lifecycles.

