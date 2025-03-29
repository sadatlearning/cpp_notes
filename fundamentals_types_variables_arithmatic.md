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

---

Here’s an expanded **Practice Problems** section with solutions in Markdown format:

```markdown
---

## 6. Practice Problems

### Problem 1: Temperature Converter
Write a program that converts Celsius to Fahrenheit:
```cpp
#include <iostream>
using namespace std;

int main() {
    double celsius;
    cout << "Enter temperature in Celsius: ";
    cin >> celsius;
    
    double fahrenheit = (celsius * 9/5) + 32;
    cout << "Temperature in Fahrenheit: " << fahrenheit << endl;
    
    return 0;
}
```
**Key Concepts**: Arithmetic operations, I/O, variable types

---

### Problem 2: Integer Overflow Check
Detect overflow when adding two large integers:
```cpp
#include <iostream>
#include <limits>
using namespace std;

int main() {
    int a = 2000000000;
    int b = 2000000000;
    
    if (b > numeric_limits<int>::max() - a) {
        cout << "Overflow will occur!" << endl;
    } else {
        int sum = a + b;
        cout << "Sum: " << sum << endl;
    }
    
    return 0;
}
```
**Key Concepts**: Integer limits, overflow prevention

---

### Problem 3: Type Conversion Puzzle
Predict the output:
```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 5;
    double y = 2;
    auto result = x / y;
    
    cout << "Result type: " << typeid(result).name() << endl;
    cout << "Result value: " << result << endl;
    
    return 0;
}
```
**Solution**:  
Output:
```
Result type: d (double)
Result value: 2.5
```
**Key Concepts**: Implicit conversion, `auto` type deduction

---

### Problem 4: Bitmask Operations
Create a flag system using bitwise operators:
```cpp
#include <iostream>
using namespace std;

const int FLAG_A = 1 << 0; // 0001
const int FLAG_B = 1 << 1; // 0010
const int FLAG_C = 1 << 2; // 0100

int main() {
    int settings = FLAG_A | FLAG_C; // 0101
    
    cout << "Has FLAG_A: " << (settings & FLAG_A) << endl;
    cout << "Has FLAG_B: " << (settings & FLAG_B) << endl;
    
    return 0;
}
```
**Key Concepts**: Bitwise operations, constants

---

### Problem 5: Floating-Point Precision
Demonstrate precision limitations:
```cpp
#include <iomanip>
#include <iostream>
using namespace std;

int main() {
    float f = 0.1f;
    double d = 0.1;
    
    cout << setprecision(20);
    cout << "Float:  " << f << endl;
    cout << "Double: " << d << endl;
    
    return 0;
}
```
**Expected Output**:
```
Float:  0.10000000149011611938
Double: 0.10000000000000000555
```
**Key Concepts**: Floating-point precision, `setprecision`

---

## 7. Solutions Discussion

### Common Mistakes to Avoid
1. **Integer Division**: 
   ```cpp
   double avg = sum / count; // Wrong if both are integers
   double avg = static_cast<double>(sum) / count; // Correct
   ```

2. **Uninitialized Variables**:
   ```cpp
   int x; // Dangerous!
   cout << x; // Undefined behavior
   ```

3. **Narrowing Conversions**:
   ```cpp
   int big = 1000;
   char small = big; // Silent truncation!
   ```

---

## 8. Advanced Challenge
**Write a program that:**
1. Takes radius as input
2. Calculates both circumference (2πr) and area (πr²)
3. Uses `constexpr` for π
4. Validates input (must be positive)

**Solution**:
```cpp
#include <iostream>
#include <cmath>
using namespace std;

constexpr double PI = 3.141592653589793;

int main() {
    double radius;
    cout << "Enter radius: ";
    cin >> radius;
    
    if (radius <= 0) {
        cerr << "Error: Radius must be positive" << endl;
        return 1;
    }
    
    cout << "Circumference: " << 2 * PI * radius << endl;
    cout << "Area: " << PI * pow(radius, 2) << endl;
    
    return 0;
}
```
```

This section adds:
- **5 practical problems** with solutions
- **Common pitfalls** explanations
- **An advanced challenge** integrating multiple concepts
- **Ready-to-run code snippets**

Would you like me to add:
1. More bit manipulation exercises?
2. A section on debugging type-related issues?
3. Real-world application examples (e.g., game physics calculations)? 🎯
