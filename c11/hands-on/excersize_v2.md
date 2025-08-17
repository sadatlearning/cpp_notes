# excersize_v2
 - let’s do this step-by-step with **full C++ code solutions** for each exercise.
I’ll give you **ready-to-compile code**, with comments so you can see what’s happening.
You can copy-paste and run each step separately.

---

# 🛠 Move Semantics Hands-On Exercises with Solutions

---

## **Step 1: First Move Constructor**

```cpp
#include <iostream>
#include <string>

class Simple {
    std::string name;
public:
    // Constructor
    Simple(const std::string& n) : name(n) {
        std::cout << "Constructed: " << name << "\n";
    }

    // Copy constructor
    Simple(const Simple& other) : name(other.name) {
        std::cout << "Copied\n";
    }

    // Move constructor
    Simple(Simple&& other) noexcept : name(std::move(other.name)) {
        std::cout << "Moved\n";
    }
};

int main() {
    Simple a("A");
    Simple b = a;              // calls copy
    Simple c = Simple("Temp"); // calls move
}
```

✅ Expected Output:

```
Constructed: A
Copied
Constructed: Temp
Moved
```

---

## **Step 2: Move Assignment**

```cpp
#include <iostream>
#include <string>

class Simple {
    std::string name;
public:
    Simple(const std::string& n) : name(n) {
        std::cout << "Constructed: " << name << "\n";
    }

    Simple(const Simple& other) : name(other.name) {
        std::cout << "Copied\n";
    }

    Simple(Simple&& other) noexcept : name(std::move(other.name)) {
        std::cout << "Moved\n";
    }

    // Copy assignment
    Simple& operator=(const Simple& other) {
        if (this != &other) {
            name = other.name;
            std::cout << "Copy Assigned\n";
        }
        return *this;
    }

    // Move assignment
    Simple& operator=(Simple&& other) noexcept {
        if (this != &other) {
            name = std::move(other.name);
            std::cout << "Move Assigned\n";
        }
        return *this;
    }
};

int main() {
    Simple a("One"), b("Two");
    b = a;                     // copy assignment
    b = Simple("Three");       // move assignment
}
```

✅ Expected Output:

```
Constructed: One
Constructed: Two
Copied
Copy Assigned
Constructed: Three
Move Assigned
```

---

## **Step 3: Resource Management (Rule of Five)**

```cpp
#include <iostream>

class Buffer {
    int* data;
    size_t size;
public:
    // Constructor
    Buffer(size_t s) : size(s) {
        data = new int[size];
        std::cout << "Constructed Buffer of size " << size << "\n";
    }

    // Destructor
    ~Buffer() {
        delete[] data;
        std::cout << "Destroyed Buffer of size " << size << "\n";
    }

    // Copy constructor
    Buffer(const Buffer& other) : size(other.size) {
        data = new int[size];
        std::copy(other.data, other.data + size, data);
        std::cout << "Copied Buffer of size " << size << "\n";
    }

    // Copy assignment
    Buffer& operator=(const Buffer& other) {
        if (this != &other) {
            delete[] data;
            size = other.size;
            data = new int[size];
            std::copy(other.data, other.data + size, data);
            std::cout << "Copy Assigned Buffer of size " << size << "\n";
        }
        return *this;
    }

    // Move constructor
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
        std::cout << "Moved Buffer of size " << size << "\n";
    }

    // Move assignment
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            size = other.size;
            other.data = nullptr;
            other.size = 0;
            std::cout << "Move Assigned Buffer of size " << size << "\n";
        }
        return *this;
    }

    size_t getSize() const { return size; }
};

int main() {
    Buffer b1(1000000);
    Buffer b2 = b1;             // copy constructor
    Buffer b3 = std::move(b1);  // move constructor
}
```

---

## **Step 4: STL Integration**

```cpp
#include <iostream>
#include <vector>

class Buffer {
    int* data;
    size_t size;
public:
    Buffer(size_t s) : size(s) {
        data = new int[size];
        std::cout << "Constructed Buffer of size " << size << "\n";
    }
    ~Buffer() { delete[] data; }

    Buffer(const Buffer& other) : size(other.size) {
        data = new int[size];
        std::copy(other.data, other.data + size, data);
        std::cout << "Copied Buffer of size " << size << "\n";
    }
    Buffer& operator=(const Buffer& other) {
        if (this != &other) {
            delete[] data;
            size = other.size;
            data = new int[size];
            std::copy(other.data, other.data + size, data);
            std::cout << "Copy Assigned Buffer of size " << size << "\n";
        }
        return *this;
    }

    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
        std::cout << "Moved Buffer of size " << size << "\n";
    }
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            size = other.size;
            other.data = nullptr;
            other.size = 0;
            std::cout << "Move Assigned Buffer of size " << size << "\n";
        }
        return *this;
    }
};

Buffer makeBuffer() {
    Buffer b(1000);
    return b;  // should move, not copy
}

int main() {
    Buffer b = makeBuffer(); // move constructor

    std::vector<Buffer> v;
    v.push_back(Buffer(500));   // rvalue → move
    Buffer b2(200);
    v.push_back(b2);            // lvalue → copy
    v.push_back(std::move(b2)); // force move
}
```

---

## **Step 5: Moved-From State**

```cpp
#include <iostream>

class Buffer {
    int* data;
    size_t size;
public:
    Buffer(size_t s) : size(s) {
        data = new int[size];
        std::cout << "Constructed\n";
    }
    ~Buffer() { delete[] data; }

    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
        std::cout << "Moved\n";
    }

    size_t getSize() const { return size; }
};

int main() {
    Buffer b1(10);
    Buffer b2 = std::move(b1);

    std::cout << "b1.size = " << b1.getSize() << "\n"; // should be 0
    std::cout << "b2.size = " << b2.getSize() << "\n"; // should be 10
}
```

---

## **Step 6: std::move vs std::forward**

```cpp
#include <iostream>
#include <utility>

void foo(const std::string& s) {
    std::cout << "foo(const&): " << s << "\n";
}
void foo(std::string&& s) {
    std::cout << "foo(&&): " << s << "\n";
}

template<typename T>
void wrapper(T&& arg) {
    std::cout << "Wrapper forwarding\n";
    foo(std::forward<T>(arg));  // preserves lvalue/rvalue
}

int main() {
    std::string s = "Hello";

    foo(s);               // calls lvalue overload
    foo(std::move(s));    // calls rvalue overload

    std::string t = "World";
    wrapper(t);           // passes as lvalue
    wrapper(std::string("Temp")); // passes as rvalue
}
```

---

## **Step 7: Mini Project — StringPool**

```cpp
#include <iostream>
#include <vector>
#include <string>

class StringPool {
    std::vector<std::string> pool;
public:
    void add(const std::string& s) { // copy
        pool.push_back(s);
        std::cout << "Added (copy): " << s << "\n";
    }

    void add(std::string&& s) { // move
        pool.push_back(std::move(s));
        std::cout << "Added (move): " << pool.back() << "\n";
    }

    void show() const {
        std::cout << "Pool contents:\n";
        for (auto& s : pool) {
            std::cout << " - " << s << "\n";
        }
    }
};

int main() {
    StringPool pool;

    pool.add("Hello"); // move
    std::string temp = "World";
    pool.add(temp);    // copy
    pool.add(std::move(temp)); // move

    pool.show();
}
```

---

✅ With these 7 exercises, you’ll **master move semantics step-by-step**, from basics → Rule of Five → STL integration → perfect forwarding → mini-project.

---
