## mangling
- In C++, a **mangled name** (also known as a **decorated name**) is the result of a transformation that the compiler performs on the name of a function, variable, or other symbols to encode additional information such as:
  - The function's namespace or class
  - Argument types (function overloading info)
  - Calling convention
  - Const/volatile qualifiers

This process is known as **name mangling**, and it allows C++ to support features like function overloading and namespaces while still linking correctly using traditional linkers (which don't understand C++ features).

---

### 🔧 Example

```cpp
// C++ code
namespace MyNamespace {
    class MyClass {
    public:
        void myFunction(int x);
    };
}
```

The function `MyNamespace::MyClass::myFunction(int)` might get mangled into something like:

```
_ZN11MyNamespace7MyClass10myFunctionEi
```

(depending on the compiler — this is how **GCC** would mangle it)

---

### 🧰 Demangling

If you want to see what a mangled name means, you can **demangle** it.

#### Linux (GCC or Clang):
Use `c++filt`:
```bash
c++filt _ZN11MyNamespace7MyClass10myFunctionEi
```
Output:
```
MyNamespace::MyClass::myFunction(int)
```

---

### 💡 Why It Matters

- You see mangled names in **linker errors**.
- It helps linkers distinguish between overloaded functions.
- Needed for **interfacing C++ with C** (you'll use `extern "C"` to avoid mangling).

---

### 📦 Disable Mangling for C Interop

If you’re writing a function to be called from C:
```cpp
extern "C" void my_c_function();  // No mangling!
```

---

Awesome! Let’s do a hands-on example. Here’s a small C++ snippet with some overloaded and namespaced functions. You can compile it and inspect the mangled names.

---

### 🔍 Example C++ Code

```cpp
// file: mangling.cpp
#include <iostream>

namespace Demo {
    class Test {
    public:
        void sayHello();
        void sayHello(int);
    };
}

void Demo::Test::sayHello() {
    std::cout << "Hello (no args)\n";
}

void Demo::Test::sayHello(int x) {
    std::cout << "Hello with int: " << x << "\n";
}

int main() {
    Demo::Test t;
    t.sayHello();
    t.sayHello(42);
    return 0;
}
```

---

### 🧱 Step 1: Compile with Symbol Info

Use `-c` (compile only) and `-g` (include debug symbols):
```bash
g++ -c -g mangling.cpp
```

---

### 🧱 Step 2: Inspect Symbols (Mangled)

```bash
nm mangling.o | grep sayHello
```

Example output:
```
000000000000002a T _ZN4Demo4Test8sayHelloEv
000000000000006e T _ZN4Demo4Test8sayHelloEi
```

---

### 🧩 Step 3: Demangle

```bash
c++filt _ZN4Demo4Test8sayHelloEv
```

Result:
```
Demo::Test::sayHello()
```

```bash
c++filt _ZN4Demo4Test8sayHelloEi
```

Result:
```
Demo::Test::sayHello(int)
```

---

### How virtual functions, templates, or const methods get mangled?

- Those are **great cases** to explore because they produce interesting and more complex mangled names. Let’s look at how each one affects the name mangling process.

## 1. 🧩 Virtual Functions

```cpp
class Animal {
public:
    virtual void speak();
};
```

Mangled name (GCC):
```
_ZN6Animal5speakEv
```

Even though it’s virtual, the name looks like a regular function. The "virtual" aspect doesn’t change the mangling itself — it's handled by the **vtable**, not the symbol name.

## 2. 🧬 Templates

```cpp
template<typename T>
class Box {
public:
    void store(T item);
};
```

Instantiation for `int`:

```cpp
Box<int> b;
b.store(42);
```

Mangled name:
```
_ZN3BoxIiE5storeEi
```

Let’s break it down:
- `BoxIiE`: `Box<int>` (template instantiation)
- `5store`: function `store`
- `Ei`: takes an `int`

## 3. ❄️ `const` Methods

```cpp
class Printer {
public:
    void print() const;
};
```

Mangled name:
```
_ZN7Printer5printEv
```

If there’s a **non-const** and a **const** version:
```cpp
class Printer {
public:
    void print();
    void print() const;
};
```

You'll see:
```
_ZN7Printer5printEv         // non-const
_ZNK7Printer5printEv        // const
```

Notice the `K` in `_ZNK...` — it means the function is `const`.

## ⚙️ Want to Try It Yourself?

You can test this in your own `.cpp` file and run:
```bash
g++ -c -g test.cpp
nm test.o | c++filt
```

---

### A test program with all three (virtual, template, const) for you to run and explore?
Perfect! Here's a single C++ file that includes:

✅ A virtual function  
✅ A template class  
✅ A const and non-const method  
✅ A `main()` that uses them (so symbols actually get emitted)

### 🧪 Full Test Program — `mangling_test.cpp`

```cpp
#include <iostream>
#include <string>

class Animal {
public:
    virtual void speak() {
        std::cout << "Animal speaks\n";
    }
};

class Printer {
public:
    void print() {
        std::cout << "Printer: non-const\n";
    }

    void print() const {
        std::cout << "Printer: const\n";
    }
};

template<typename T>
class Box {
public:
    void store(T item) {
        std::cout << "Storing: " << item << "\n";
    }
};

int main() {
    // Virtual function usage
    Animal* a = new Animal();
    a->speak();

    // Const vs non-const method
    Printer p1;
    const Printer p2;
    p1.print();
    p2.print();

    // Template instantiation
    Box<int> b;
    b.store(123);

    return 0;
}
```

### 🧱 Compile & Analyze

```bash
g++ -c -g mangling_test.cpp
nm mangling_test.o | c++filt | grep -E 'speak|print|store'
```

Expected demangled output:

```
Animal::speak()
Printer::print()
Printer::print() const
Box<int>::store(int)
```

Mangled names (GCC):

- `Animal::speak()` → `_ZN6Animal5speakEv`
- `Printer::print()` → `_ZN7Printer5printEv`
- `Printer::print() const` → `_ZNK7Printer5printEv`
- `Box<int>::store(int)` → `_ZN3BoxIiE5storeEi`

