# Move semantics in C++
- Is one of the most important modern C++ concepts (introduced in **C++11**) because it enables **efficient resource management** and **performance improvements**
-  when objects involve heavy allocations (e.g., strings, vectors, large classes).
- Step by step, from **fundamental intuition** to **advanced usage**.

---

# 1. Motivation: Why Do We Need Move Semantics?

Before C++11, objects were copied by:

* **Copy constructor** (on initialization)
* **Copy assignment operator** (on assignment)

That means, when you pass/return big objects, the compiler would **copy all data**, which is costly.

Example:

```cpp
#include <iostream>
#include <vector>

std::vector<int> createVector() {
    std::vector<int> v(1000000, 42); // big vector
    return v;  // expensive copy (pre-C++11)
}

int main() {
    auto vec = createVector(); // copy constructor called
}
```

👉 Pre-C++11: `createVector` would **copy 1 million ints** when returning.
👉 With **C++11 move semantics**, instead of copying, it **steals the data buffer**.

---

# 2. What Are Rvalues and Lvalues?

* **Lvalue**: has a name, addressable.
  Example:

  ```cpp
  int x = 10;   // x is lvalue
  ```
* **Rvalue**: temporary, no name, usually created on the fly.
  Example:

  ```cpp
  int y = x + 5;   // (x + 5) is rvalue
  ```

So move semantics is all about **treating rvalues differently** (because they are temporary and safe to "steal" from).

---

# 3. Move Constructor and Move Assignment Operator

C++11 introduced:

```cpp
ClassName(ClassName&& other);             // move constructor
ClassName& operator=(ClassName&& other);  // move assignment
```

They take an **rvalue reference** (`&&`).

---

# 4. Example: Without vs With Move

```cpp
#include <iostream>
#include <string>

class MyString {
    char* data;
public:
    MyString(const char* s) {
        data = new char[strlen(s)+1];
        strcpy(data, s);
        std::cout << "Constructed\n";
    }

    // Copy constructor
    MyString(const MyString& other) {
        data = new char[strlen(other.data)+1];
        strcpy(data, other.data);
        std::cout << "Copied\n";
    }

    // Move constructor
    MyString(MyString&& other) noexcept {
        data = other.data;  // steal pointer
        other.data = nullptr;
        std::cout << "Moved\n";
    }

    ~MyString() { delete[] data; }
};

int main() {
    MyString a("Hello");
    MyString b = a;            // calls copy
    MyString c = MyString("World"); // calls move (rvalue)
}
```

Output:

```
Constructed
Copied
Constructed
Moved
```

👉 Notice: `c` steals memory from the temporary `"World"` instead of copying.
This avoids allocating and copying again.

---

# 5. std::move

`std::move` **converts an lvalue into an rvalue reference**, telling the compiler: "This object can be moved."

Example:

```cpp
MyString d("Hello");
MyString e = std::move(d); // forces move constructor
```

⚠️ After this, `d` is in a **valid but unspecified state** (can only be destructed or reassigned safely).

---

# 6. Move Assignment Operator Example

```cpp
class MyString {
    char* data;
public:
    // ... ctor, copy ctor, dtor like before

    // Move assignment
    MyString& operator=(MyString&& other) noexcept {
        if (this != &other) {
            delete[] data;           // free old
            data = other.data;       // steal
            other.data = nullptr;
            std::cout << "Move Assigned\n";
        }
        return *this;
    }
};
```

---

# 7. Move Semantics with STL

Most STL containers (like `std::vector`, `std::string`, `std::map`) are move-enabled.

```cpp
#include <vector>
#include <iostream>

std::vector<int> makeBigVector() {
    std::vector<int> v(1000000, 42);
    return v;   // move, no copy
}

int main() {
    auto v = makeBigVector();  // efficient
    std::vector<int> w = std::move(v); // steal buffer
    std::cout << "Size of v: " << v.size() << "\n"; // v is valid but empty
}
```

---

# 8. When Should You Implement Move?

You should provide move constructor/assignment if:

* Your class **manages resources** (heap memory, file handles, sockets, etc.)
* Copying is expensive
* You want STL compatibility & efficiency

If your class is simple (no raw resource management), compiler-generated moves are usually fine.

---

# 9. Rule of Five

If you define:

1. Destructor
2. Copy constructor
3. Copy assignment
   👉 You should also define:
4. Move constructor
5. Move assignment operator

This ensures full correctness.

---

# 10. Advanced — Perfect Forwarding

Move semantics also enables **perfect forwarding** with templates:

```cpp
template<typename T>
void wrapper(T&& arg) {
    func(std::forward<T>(arg));  // preserves lvalue/rvalue-ness
}
```

Used in **emplace functions** like `emplace_back` in STL.

---

✅ So to summarize:

* Move semantics **avoids expensive deep copies** by stealing resources.
* Uses **rvalue references (`&&`)**.
* `std::move` forces move when needed.
* STL containers fully support it.
* Must be careful: moved-from objects are valid but unspecified.

---
