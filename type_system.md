## Type System

- C++ has a **strong and statically typed** system, meaning that types are checked at compile time and conversions between types must be explicit in many cases.
  - The type system in C++ is quite rich and powerful, supporting features like inheritance, templates, and operator overloading.

- A breakdown of the **C++ type system**:

---

### 🔹 1. **Basic Built-in Types**
These are the primitive types:

- **Integer types**: `int`, `short`, `long`, `long long`, with `signed` and `unsigned` variants.
- **Floating point types**: `float`, `double`, `long double`
- **Character types**: `char`, `wchar_t`, `char16_t`, `char32_t`
- **Boolean**: `bool`
- **Void**: `void` (used to represent no value or return type)

---

### 🔹 2. **Derived Types**
Formed from built-in or user-defined types:

- **Pointers**: `int*`, `char*`
- **References**: `int&`, `const string&`
- **Arrays**: `int arr[10]`
- **Functions**: `int func(int, int)`
- **Function pointers**: `int (*fptr)(int, int)`
- **Member pointers**: `int (Class::*)(int)`

---

### 🔹 3. **User-Defined Types**
Let you define complex behavior:

- **Classes and Structs**
- **Unions**
- **Enums**: traditional `enum` and scoped `enum class`
- **Typedefs / using**: to define aliases
  ```cpp
  typedef unsigned int uint;
  using uint = unsigned int;
  ```

---

### 🔹 4. **CV Qualifiers**
A type can be **qualified** as:

- `const int` → cannot be modified
- `volatile int` → tells the compiler not to optimize the variable (used in embedded / multithreading)
- `mutable` → allows modification even if the containing object is `const`

---

### 🔹 5. **Type Modifiers**
- `signed`, `unsigned`, `short`, `long`
  ```cpp
  unsigned long long int bigNumber;
  ```

---

### 🔹 6. **Templates (Generic Types)**
Templates allow for **generic programming**:

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

These are types that aren’t fixed until compile-time **instantiation**.

---

### 🔹 7. **Type Inference (`auto`, `decltype`)**
Introduced in C++11 and enhanced in C++14/17:

```cpp
auto x = 5;           // deduced as int
decltype(x) y = x;    // y is same type as x
```

---

### 🔹 8. **Type Casting**
C++ has four types of **explicit casts**:

- `static_cast<>` – for well-defined conversions
- `const_cast<>` – to add/remove `const`
- `reinterpret_cast<>` – for low-level reinterpretations
- `dynamic_cast<>` – for safe downcasting in polymorphic classes

---

### 🔹 9. **Type Traits (C++11 and above)**
Used in **template metaprogramming** to query or modify types at compile-time:

```cpp
#include <type_traits>

std::is_integral<int>::value;   // true
std::is_floating_point<float>::value; // true
```

---

### 🔹 10. **Polymorphic Types**
If a class has **at least one virtual function**, it's considered **polymorphic**. You can then use `dynamic_cast` on it.

```cpp
class Base { virtual void foo(); };
class Derived : public Base {};
```

---

### 🔹 11. **Type Aliases, Namespaces, and Type Scoping**
C++ allows for:

- **Type aliasing** via `using`
- **Namespace-qualified types**
- **Nested types** within classes

---

### Want a visual or hands-on demo?
- I can give you a C++ example that explores or prints type traits or shows how the type system works in a real project.
- Here's a simple but powerful C++ example that **uses type traits** from `<type_traits>` to explore and print type information at compile-time and runtime.

---

### ✅ Example: Type Traits Explorer in C++

```cpp
#include <iostream>
#include <type_traits>
#include <string>
#include <vector>

template<typename T>
void exploreTypeTraits(const std::string& typeName) {
    std::cout << "Exploring traits for: " << typeName << "\n";
    std::cout << "------------------------------\n";
    std::cout << "Is void?                : " << std::boolalpha << std::is_void<T>::value << "\n";
    std::cout << "Is integral?            : " << std::is_integral<T>::value << "\n";
    std::cout << "Is floating point?      : " << std::is_floating_point<T>::value << "\n";
    std::cout << "Is arithmetic?          : " << std::is_arithmetic<T>::value << "\n";
    std::cout << "Is pointer?             : " << std::is_pointer<T>::value << "\n";
    std::cout << "Is reference?           : " << std::is_reference<T>::value << "\n";
    std::cout << "Is const?               : " << std::is_const<T>::value << "\n";
    std::cout << "Is class?               : " << std::is_class<T>::value << "\n";
    std::cout << "Is array?               : " << std::is_array<T>::value << "\n";
    std::cout << "Is same as int?         : " << std::is_same<T, int>::value << "\n";
    std::cout << "Is trivially copyable?  : " << std::is_trivially_copyable<T>::value << "\n";
    std::cout << "Is default constructible: " << std::is_default_constructible<T>::value << "\n";
    std::cout << "\n";
}

// Custom class
class MyClass {};

int main() {
    exploreTypeTraits<int>("int");
    exploreTypeTraits<const int>("const int");
    exploreTypeTraits<float>("float");
    exploreTypeTraits<void>("void");
    exploreTypeTraits<int*>("int*");
    exploreTypeTraits<int&>("int&");
    exploreTypeTraits<MyClass>("MyClass");
    exploreTypeTraits<std::vector<int>>("std::vector<int>");

    return 0;
}
```

---

### 🔍 Sample Output:
```
Exploring traits for: int
------------------------------
Is void?                : false
Is integral?            : true
Is floating point?      : false
Is arithmetic?          : true
Is pointer?             : false
Is reference?           : false
Is const?               : false
Is class?               : false
Is array?               : false
Is same as int?         : true
Is trivially copyable?  : true
Is default constructible: true
```

---

### 🧠 What You Learn From This:

- **How C++ sees types internally**
- How to use **`std::is_*`** traits to make decisions in template metaprogramming
- You can build **type-safe generic code** that adapts based on type traits

---

## Want to go further with SFINAE, `if constexpr`, or create a compile-time type filter? Just say the word 😎

Awesome! Let’s level up by combining **type traits**, **`if constexpr`**, and **SFINAE** (Substitution Failure Is Not An Error) to build a **compile-time type filter**.

We'll write a function that **behaves differently based on type** — no runtime cost, fully resolved at compile-time!

---

### ✅ Example: Type-Aware Dispatcher with `if constexpr`

```cpp
#include <iostream>
#include <type_traits>
#include <string>

template<typename T>
void process(const T& value) {
    if constexpr (std::is_integral<T>::value) {
        std::cout << "Integral type: " << value << "\n";
    }
    else if constexpr (std::is_floating_point<T>::value) {
        std::cout << "Floating point type: " << value << "\n";
    }
    else if constexpr (std::is_pointer<T>::value) {
        std::cout << "Pointer to value: " << *value << "\n";
    }
    else if constexpr (std::is_class<T>::value) {
        std::cout << "Class type\n";
    }
    else {
        std::cout << "Unknown type\n";
    }
}

// A simple class
struct MyClass {
    int x = 42;
};

int main() {
    int i = 10;
    float f = 3.14f;
    int* p = &i;
    MyClass obj;

    process(i);     // Integral
    process(f);     // Float
    process(p);     // Pointer
    process(obj);   // Class
}
```

---

### 🔍 Output:
```
Integral type: 10
Floating point type: 3.14
Pointer to value: 10
Class type
```

---

### 🔧 How It Works:
- `if constexpr` ensures only **one block gets compiled** based on the type — efficient and elegant.
- `std::is_*<T>` traits help decide the category.
- This is **compile-time polymorphism**, often used in **template metaprogramming** and **type-safe generic code**.

---

### ⚡ Bonus: SFINAE Example – Only Enable for Arithmetic Types

```cpp
template<typename T>
typename std::enable_if<std::is_arithmetic<T>::value>::type
safeAdd(T a, T b) {
    std::cout << "Safe add result: " << (a + b) << "\n";
}
```

Or using modern style:
```cpp
template<typename T>
requires std::is_arithmetic_v<T>
void safeAdd(T a, T b) {
    std::cout << "Safe add result: " << (a + b) << "\n";
}
```

---

Let me know if you'd like to dive deeper into:
- Building your own **custom type traits**
- Using **`std::conditional`**, **`std::remove_const`**, etc.
- Metaprogramming with **type lists** or **concepts** (C++20)

We can even build a **template-based type filter pipeline** together.


Let me know how deep you'd like to go—foundational, intermediate, or into meta-programming and type erasure techniques?


