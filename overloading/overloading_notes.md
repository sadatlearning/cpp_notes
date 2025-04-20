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

## Walk you through what overloads get picked in that example and why?
