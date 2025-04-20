# Function Overloading

### Experimenting with **function overloading** — a key C++ feature — but your code has a couple of issues that we can use as learning opportunities.

- Let's walk through them carefully and then dive deep into how **overloading works under the hood**.

### 🛑 Review of Your Code

```cpp
int test(int num);
float test(int num);

int main(void)
{
    test(100);
    test(200);
    erturn (0);
}

int test(int num)
{
    return num * 10;
}

float test(int num)
{
    return ((float)num/7.0;);
}
```

### 🐞 Compilation Errors

1. **Overloading on return type only is not allowed** in C++.  
   These two:
   ```cpp
   int test(int num);
   float test(int num);
   ```
   are **not valid overloads** — they have the **same parameter list** but different return types. The compiler cannot resolve which one to call based on the return type alone because calls like `test(100);` don’t say which return value to expect.

2. **Syntax errors**:
   - `erturn (0);` → typo: should be `return 0;`
   - `return ((float)num/7.0;);` → unbalanced parentheses and semicolon in the wrong place. Should be:
     ```cpp
     return (float)num / 7.0;
     ```

### ✅ Fixed Version with Proper Overloading

You **must** change the parameter types for valid overloading:

```cpp
#include <iostream>
using namespace std;

// Proper overloads
int test(int num) {
    return num * 10;
}

float test(float num) {
    return num / 7.0f;
}

int main() {
    int a = test(100);       // calls int version
    float b = test(200.0f);  // calls float version

    cout << "int test: " << a << endl;
    cout << "float test: " << b << endl;

    return 0;
}
```

---

## 💡 C++ Overloading Rules – In Depth

Function **overloading** means defining **multiple functions** with the **same name** but **different signatures**.

> 🔒 **C++ does not consider the return type in overload resolution**.

#### Overload Resolution Depends On:

- Number of parameters
- Types of parameters
- Order of parameters
- Whether parameters are references or pointers
- `const` vs non-`const` in references
- Variadic functions (`...`)

✅ Valid Overloads:
```cpp
void foo(int);
void foo(float);
void foo(int, float);
void foo(const int&);
```

🛑 Invalid Overloads:
```cpp
int foo(int);
float foo(int); // ❌ Ambiguous – same signature
```

---

### 🧬 Under the Hood: Name Mangling

Each overload gets a **unique mangled name**:

```cpp
int test(int);     // _Z4testi
float test(float); // _Z4testf
```

You can inspect these using `nm` and `c++filt`.

---

### 🔬 Want to Experiment?

Here’s a small playground idea:
```cpp
void fun(int);
void fun(double);
void fun(int, double);
void fun(double, int);
void fun(const int&);

int main() {
    fun(10);         // Which one is called?
    fun(10.5);       // And this?
    fun(10, 2.5);
    fun(3.14, 5);
    const int x = 42;
    fun(x);
}
```

### Walk you through what overloads get picked in that example and why?

Perfect! Let's walk through the overload resolution **step by step** using your test functions. First, here's the full code to refer to:

---

### 🔢 Overload Variants

```cpp
#include <iostream>
using namespace std;

void fun(int) {
    cout << "fun(int)" << endl;
}

void fun(double) {
    cout << "fun(double)" << endl;
}

void fun(int, double) {
    cout << "fun(int, double)" << endl;
}

void fun(double, int) {
    cout << "fun(double, int)" << endl;
}

void fun(const int&) {
    cout << "fun(const int&)" << endl;
}

int main() {
    fun(10);          // 🔍 call #1
    fun(10.5);        // 🔍 call #2
    fun(10, 2.5);     // 🔍 call #3
    fun(3.14, 5);     // 🔍 call #4
    const int x = 42;
    fun(x);           // 🔍 call #5
    return 0;
}
```

---

### 🧠 Overload Resolution Logic (What gets picked and why)

#### 🔍 Call #1: `fun(10);`

- `10` is an `int`
- Matches:
  - `fun(int)` → **exact match**
  - `fun(const int&)` → also valid, but **not better than `fun(int)`**
- ✅ **Picked:** `fun(int)`

> 🎯 **Exact match wins** over reference match.

---

#### 🔍 Call #2: `fun(10.5);`

- `10.5` is a `double`
- Matches:
  - `fun(double)` → **exact match**
- ✅ **Picked:** `fun(double)`

> 🎯 Again, exact match.

---

#### 🔍 Call #3: `fun(10, 2.5);`

- `10` → `int`
- `2.5` → `double`
- Matches:
  - `fun(int, double)` → **perfect match**
- ✅ **Picked:** `fun(int, double)`

---

#### 🔍 Call #4: `fun(3.14, 5);`

- `3.14` → `double`
- `5` → `int`
- Matches:
  - `fun(double, int)` → **perfect match**
- ✅ **Picked:** `fun(double, int)`

---

#### 🔍 Call #5: `fun(x);` (where `x` is `const int`)

- Matches:
  - `fun(int)` → okay (const can bind to copy)
  - `fun(const int&)` → perfect match
- ❗ Here, **both are viable**:
  - `fun(int)` would make a copy
  - `fun(const int&)` uses binding (no copy)

- ✅ **Picked:** `fun(int)` — **because it's non-reference** and an exact match to a by-value overload (C++ prefers non-reference when ambiguity exists and performance penalty is negligible)

> 🎯 But if `fun(int)` was missing, `fun(const int&)` would be picked.

---

### 🧠 Summary Table

| Call             | Picked Function         | Reason                          |
|------------------|--------------------------|----------------------------------|
| `fun(10)`        | `fun(int)`               | Exact match                     |
| `fun(10.5)`      | `fun(double)`            | Exact match                     |
| `fun(10, 2.5)`   | `fun(int, double)`       | Exact match for both args       |
| `fun(3.14, 5)`   | `fun(double, int)`       | Exact match for both args       |
| `fun(x)`         | `fun(int)`               | Exact match; `const&` is valid but not preferred |

---

### Want to go deeper?

I can show:
- What happens when there's **implicit conversion** (e.g. `fun(char)` or `fun(short)`).
- How **template functions** interact with overloading.
- How **default arguments** affect overload resolution.
- How overloads behave with **rvalue references** (`&&`), `const`, and `volatile`.

### Explore next?

---
Awesome — let's explore each of these **advanced overload resolution topics** step by step with hands-on examples and detailed reasoning.

---

## 1. 🎭 Implicit Conversions in Overload Resolution

### Example:

```cpp
void fun(int x) {
    std::cout << "fun(int)" << std::endl;
}

void fun(double x) {
    std::cout << "fun(double)" << std::endl;
}

int main() {
    fun('A');   // char literal
    fun(5.0f);  // float literal
}
```

### What happens:

- `'A'` → `char` → implicitly convertible to `int` → `fun(int)` is picked
- `5.0f` → `float` → implicitly convertible to `double` → `fun(double)` is picked

> 🔍 Implicit conversions are **considered only if no exact match exists**

---

## 2. 🧬 Template Functions vs Normal Overloads

### Example:

```cpp
#include <iostream>
using namespace std;

void fun(int x) {
    cout << "Regular fun(int)" << endl;
}

template<typename T>
void fun(T x) {
    cout << "Template fun(T)" << endl;
}

int main() {
    fun(42);   // Which one?
    fun(3.14); // Which one?
}
```

### Resolution:

- `fun(42)` → exact match to non-template `fun(int)` → regular wins
- `fun(3.14)` → no non-template match → template instantiated with `T = double`

> 🧠 **Non-template overloads have higher priority than templates**, unless templates provide a better match.

---

## 3. 🧩 Default Arguments and Overload Ambiguity

### Example:

```cpp
#include <iostream>
using namespace std;

void fun(int x, int y = 10) {
    cout << "fun(int, int)" << endl;
}

void fun(int x) {
    cout << "fun(int)" << endl;
}

int main() {
    fun(5);  // Ambiguous?
}
```

### Output:

✅ No ambiguity — calls `fun(int)` directly  
> Even though `fun(int, int)` could be called with default `y = 10`, it's a **less preferred** match when an exact overload exists.

---

### If you remove `fun(int)`:

```cpp
// Only this remains
void fun(int x, int y = 10);
```

Then `fun(5)` resolves to that overload using the default value.

---

## 4. ⚡ `const`, `volatile`, and Rvalue Reference Overloads

### Example:

```cpp
void fun(int& x) {
    std::cout << "fun(int&)" << std::endl;
}

void fun(const int& x) {
    std::cout << "fun(const int&)" << std::endl;
}

void fun(int&& x) {
    std::cout << "fun(int&&)" << std::endl;
}

int main() {
    int a = 10;
    const int b = 20;

    fun(a);        // lvalue → fun(int&)
    fun(b);        // const lvalue → fun(const int&)
    fun(30);       // rvalue → fun(int&&)
}
```

### Key Concepts:

- `int&` binds to modifiable **lvalues**
- `const int&` binds to **any** lvalue (modifiable or not)
- `int&&` binds to **rvalues only**

> 🧠 Rvalue refs (`&&`) enable move semantics and perfect forwarding in modern C++.

---

### Next Steps

Let’s deepen any one of these with:
- Hands-on interactive puzzles
- Internals of compiler decision-making (like overload ranking)
- Integration with templates + SFINAE (`enable_if`, concepts)

---
#### Integration with templates + SFINAE (`enable_if`, concepts)

Alright, now we're stepping into 🔬 **Advanced Overload Control** using **Templates + SFINAE + Concepts** — powerful tools to **constrain overloads**, control instantiation, and write more robust, type-safe code.

---

## 🧠 1. What is SFINAE?

**S**ubstitution **F**ailure **I**s **N**ot **A**n **E**rror  
When template argument substitution fails, C++ **silently removes** that overload from the set — instead of producing a compile error.

> This enables conditional overloads based on type traits or constraints.

---

## ✨ Example 1: `enable_if` Based Overload

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

template<typename T>
typename std::enable_if<std::is_integral<T>::value>::type
fun(T val) {
    cout << "Integral: " << val << endl;
}

template<typename T>
typename std::enable_if<std::is_floating_point<T>::value>::type
fun(T val) {
    cout << "Floating point: " << val << endl;
}

int main() {
    fun(42);    // calls integral version
    fun(3.14);  // calls floating point version
}
```

### 🔍 Breakdown:
- `enable_if<true>::type` → yields `void`
- `enable_if<false>::type` → **does not exist**, so that overload is ignored

---

## 🚀 Modern C++20: Concepts

C++20 introduces **`concepts`**, a cleaner, more readable way to constrain templates.

---

## ✨ Example 2: Concepts-Based Overloading

```cpp
#include <iostream>
using namespace std;

template<typename T>
concept Integral = std::is_integral_v<T>;

template<typename T>
concept Floating = std::is_floating_point_v<T>;

void fun(Integral auto x) {
    cout << "Integral: " << x << endl;
}

void fun(Floating auto x) {
    cout << "Floating point: " << x << endl;
}

int main() {
    fun(10);     // Integral
    fun(3.14);   // Floating
}
```

✅ Way cleaner and more readable than `enable_if`.

---

## 🧬 Example 3: Controlling Template Overload with `enable_if` on Return Type

```cpp
template<typename T>
auto fun(T x) -> std::enable_if_t<std::is_integral_v<T>, int> {
    return x * 10;
}

template<typename T>
auto fun(T x) -> std::enable_if_t<std::is_floating_point_v<T>, float> {
    return x / 2.0f;
}
```

Used when you can't put `enable_if` in the parameter list directly.

---

## ⚔️ `enable_if` vs `concepts`

| Feature                    | `enable_if`                     | `concepts` (C++20+)         |
|----------------------------|----------------------------------|------------------------------|
| Readability                | More verbose                    | Much cleaner syntax         |
| Error messages             | Often cryptic                   | Cleaner diagnostics         |
| Compile-time filtering     | ✅ Yes                          | ✅ Yes                      |
| Requires C++ version       | C++11+                          | C++20+                      |

---

## 🔁 Practical Use: Type-Specific `print()`

```cpp
template<typename T>
std::enable_if_t<std::is_integral_v<T>>
print(T val) {
    std::cout << "Integral: " << val << "\n";
}

template<typename T>
std::enable_if_t<std::is_floating_point_v<T>>
print(T val) {
    std::cout << "Floating: " << val << "\n";
}
```

Try calling with:
```cpp
print(100);     // Integral
print(3.1415);  // Floating
```

---

## 🧪 Challenge for You

Want me to create a small **SFINAE/Concept overload resolution puzzle** for you to solve interactively, or would you prefer to explore how **perfect forwarding** works with SFINAE/Concepts next?


Awesome! Here's a hands-on **SFINAE + Concepts overload resolution puzzle** — let's test your intuition and help you **master it deeply**.

---

## 🧩 Puzzle: Which `fun()` Gets Called?

Given the following code:

```cpp
#include <iostream>
#include <type_traits>

template<typename T>
std::enable_if_t<std::is_integral_v<T>>
fun(T val) {
    std::cout << "Integral version: " << val << std::endl;
}

template<typename T>
std::enable_if_t<std::is_floating_point_v<T>>
fun(T val) {
    std::cout << "Floating-point version: " << val << std::endl;
}

int main() {
    fun(10);        // (A)
    fun(3.14);      // (B)
    fun('c');       // (C)
    fun(true);      // (D)
}
```

---

## ❓Your Task:

For each of the calls **(A) to (D)**, answer:
1. **Which version is called?** (integral or floating-point)
2. **Why?** (What's the underlying type trait resolution?)

---

## 🧠 Bonus: What if we used `concepts` like this?

```cpp
template<typename T>
concept Integral = std::is_integral_v<T>;

template<typename T>
concept Floating = std::is_floating_point_v<T>;

void fun(Integral auto x) {
    std::cout << "Integral: " << x << std::endl;
}

void fun(Floating auto x) {
    std::cout << "Floating: " << x << std::endl;
}
```

Would your answers change?

---

I’ll walk you through each one after your answers.

Alright, let’s walk through each of the function calls step by step, and I'll explain how overload resolution works with **SFINAE** and **Concepts**.

### 🧠 Puzzle Walkthrough:

#### (A) `fun(10);`  (Passing an `int`)

1. **Which version is called?**
   - **Integral version** (`std::enable_if_t<std::is_integral_v<T>>`)

2. **Why?**
   - The type of `10` is `int`, which is an **integral type**.  
   - `std::is_integral_v<int>` evaluates to `true`, so the `enable_if` version for integral types is **enabled**.
   - Therefore, the **integral version** of `fun(int)` gets called.

---

#### (B) `fun(3.14);`  (Passing a `double`)

1. **Which version is called?**
   - **Floating-point version** (`std::enable_if_t<std::is_floating_point_v<T>>`)

2. **Why?**
   - The type of `3.14` is `double`, which is a **floating-point type**.  
   - `std::is_floating_point_v<double>` evaluates to `true`, so the `enable_if` version for floating-point types is **enabled**.
   - Hence, the **floating-point version** of `fun(double)` gets called.

---

#### (C) `fun('c');`  (Passing a `char`)

1. **Which version is called?**
   - **Integral version** (`std::enable_if_t<std::is_integral_v<T>>`)

2. **Why?**
   - The type of `'c'` is `char`. In C++, `char` is considered an **integral type** (a small integer type).
   - `std::is_integral_v<char>` evaluates to `true`, so the `enable_if` version for integral types is **enabled**.
   - Therefore, the **integral version** of `fun(char)` gets called.

---

#### (D) `fun(true);`  (Passing a `bool`)

1. **Which version is called?**
   - **Integral version** (`std::enable_if_t<std::is_integral_v<T>>`)

2. **Why?**
   - The type of `true` is `bool`, which is technically an **integral type** in C++ (although it’s not usually thought of as a number).
   - `std::is_integral_v<bool>` evaluates to `true`, so the `enable_if` version for integral types is **enabled**.
   - Therefore, the **integral version** of `fun(bool)` gets called.

---

### 🧑‍💻 **Bonus Walkthrough with Concepts**

If you used **C++20 Concepts**, the behavior is **essentially the same**, but with cleaner syntax:

```cpp
template<typename T>
concept Integral = std::is_integral_v<T>;

template<typename T>
concept Floating = std::is_floating_point_v<T>;

void fun(Integral auto x) {
    std::cout << "Integral: " << x << std::endl;
}

void fun(Floating auto x) {
    std::cout << "Floating: " << x << std::endl;
}
```

#### What happens with `fun(10);`, `fun(3.14);`, etc.?

1. **`fun(10);`** calls `fun(Integral auto x)` because `10` is an integral type (it matches the `Integral` concept).
2. **`fun(3.14);`** calls `fun(Floating auto x)` because `3.14` is a floating-point number (it matches the `Floating` concept).
3. **`fun('c');`** calls `fun(Integral auto x)` because `'c'` is a `char`, which is integral.
4. **`fun(true);`** calls `fun(Integral auto x)` because `true` is a `bool`, which is integral.

So, in both cases — SFINAE and Concepts — the **answers remain the same**. Concepts just make the code **more readable** and the constraints **clearer**.

---

### Key Takeaways:

- **SFINAE** (via `enable_if`) allows us to conditionally enable overloads based on type traits.
- **Concepts** (C++20) offer a cleaner way to define those constraints with more readable syntax.
- Types like `char` and `bool` can be **considered integral** in C++ for these purposes, so overloads that work with integral types will also match `char` and `bool`.


