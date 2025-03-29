**Types, Variables, and Arithmetic in C++**, suitable for quick reference or study:

```markdown
# C++ Fundamentals: Types, Variables & Arithmetic

## 1. Basic Types in C++

### Fundamental Data Types
| Type       | Size (bytes) | Description                     | Example           |
|------------|-------------|---------------------------------|-------------------|
| `int`      | 4           | Integer numbers                 | `int x = 42;`     |
| `float`    | 4           | Single-precision floating point | `float y = 3.14f;`|
| `double`   | 8           | Double-precision floating point | `double z = 2.718;`|
| `char`     | 1           | Single character                | `char c = 'A';`   |
| `bool`     | 1           | Boolean (true/false)            | `bool flag = true;`|

### Type Modifiers
```cpp
signed int a = -10;    // Default (can be negative)
unsigned int b = 5;    // Only positive (0 to 2^32-1)
short int c;           // Typically 2 bytes
long int d;            // At least 4 bytes
long long int e;       // At least 8 bytes
```

---

## 2. Variables

### Variable Declaration & Initialization
```cpp
int x;          // Declaration
x = 10;         // Assignment
int y = 20;     // Initialization
int z{30};      // Uniform initialization (C++11)
auto n = 5;     // Type inference (C++11)
```

### Constants
```cpp
const double PI = 3.14159;     // Compile-time constant
constexpr int MAX = 100;       // Evaluated at compile-time (C++11)
```

---

## 3. Arithmetic Operations

### Basic Operators
| Operator | Description          | Example         |
|----------|----------------------|-----------------|
| `+`      | Addition             | `int s = a + b;`|
| `-`      | Subtraction          | `int d = a - b;`|
| `*`      | Multiplication       | `int p = a * b;`|
| `/`      | Division             | `int q = a / b;`|
| `%`      | Modulus (integers)   | `int r = a % b;`|

### Compound Assignment
```cpp
x += 5;    // Equivalent to x = x + 5
y *= 2;    // Equivalent to y = y * 2
```

### Increment/Decrement
```cpp
int i = 0;
i++;    // Post-increment (returns value before increment)
++i;    // Pre-increment (returns value after increment)
```

---

## 4. Type Conversion

### Implicit Conversion
```cpp
int num_int = 10;
double num_double = num_int;  // int → double (widening)
```

### Explicit Conversion (Casting)
```cpp
double x = 3.14;
int y = (int)x;           // C-style cast
int z = static_cast<int>(x);  // Preferred C++ cast
```

---

## 5. Common Pitfalls

1. **Integer Division**:
   ```cpp
   int a = 5 / 2;    // Result: 2 (not 2.5)
   ```

2. **Overflow**:
   ```cpp
   unsigned char n = 255;
   n++;    // Wraps around to 0
   ```

3. **Uninitialized Variables**:
   ```cpp
   int x;    // Undefined value!
   ```

---

## Best Practices

✔ Always initialize variables  
✔ Use `const` for values that shouldn't change  
✔ Prefer `static_cast` over C-style casts  
✔ Use `auto` when type is obvious (C++11+)  
✔ Avoid magic numbers (use named constants)  
```

This Markdown is:
- **Ready-to-use** in Obsidian/VS Code/GitHub
- **Formatted for clarity** with tables/code blocks
- **Covers modern C++** (C++11/14 features included)
- **Highlighted common mistakes**

Would you like me to add a **practice problem section** with solutions? 🚀
