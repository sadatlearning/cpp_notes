## Some core object-oriented and memory management concepts
### Particularly
   - **operator overloading**
   - **copy control**
   - **deep copy**
   - **shallow copy**
commonly found in languages like **C++**.

Go through these **from fundamentals to advanced** step by step.

## ✅ 1. Operator Overloading

### 📌 What is it?

Operator overloading allows you to define **custom behavior** for operators (`+`, `-`, `=`, `==`, etc.) when they are used with **user-defined types** (like classes).

---

### 🔰 Basic Example: Overloading `+`

```cpp
#include <iostream>
using namespace std;

class Point {
  int x, y;

public:
  Point(int a = 0, int b = 0) : x(a), y(b) {}

  // Overload '+' operator
  Point operator+(const Point& p) {
    return Point(x + p.x, y + p.y);
  }

  void display() { cout << "(" << x << "," << y << ")" << endl; }
};

int main() {
  Point p1(1, 2), p2(3, 4);
  Point p3 = p1 + p2;
  p3.display(); // Output: (4,6)
}
```
---
### Explanation of below line in detail:

```cpp
public:
    Point(int a=0, int b=0): x(a), y(b) {}
```

- is a **constructor definition** in C++ for a class named `Point`. Let’s break this down **piece by piece**, from beginner to advanced level.

## 🔍 Full Explanation

### 1️⃣ `public:`

This is an **access specifier**. It means that anything declared under `public:` is **accessible from outside** the class.

For example:

```cpp
Point p(3, 4);  // Allowed because the constructor is public
```

### 2️⃣ `Point(int a=0, int b=0)`

This is a **constructor declaration**:

* It’s a **special function** with the **same name as the class**.
* It is used to **initialize an object** of the class.
* The parameters `int a = 0` and `int b = 0` have **default values**, meaning:

  * You can call `Point()` with **zero, one, or two arguments**.

#### Valid calls:

```cpp
Point p1;        // Uses default 0,0
Point p2(5);     // a = 5, b = 0
Point p3(3, 7);  // a = 3, b = 7
```

### 3️⃣ `: x(a), y(b)`

This is an **initializer list**, which initializes member variables **before** the constructor body executes.

#### 🧠 Why use initializer lists?

* It is **more efficient** and **sometimes necessary**, especially for:

  * **const** members
  * **reference** members
  * **base classes** (in inheritance)
* It directly initializes members rather than assigning after default construction.

#### 🔎 Example:

Suppose the class looks like this:

```cpp
class Point {
    int x, y;
public:
    Point(int a=0, int b=0) : x(a), y(b) {}
};
```

Here's what happens under the hood for `Point p(3, 4)`:

1. `a = 3`, `b = 4`
2. `x` is initialized to 3 **before** constructor body starts
3. `y` is initialized to 4 **before** constructor body starts

### 4️⃣ `{}` — Constructor Body

In this case, the body is **empty** because initialization is handled in the initializer list.
If you needed to add logic after initialization (like logging or validation), you’d do it inside `{}`.

## 🧠 Summary Table

| Part                      | Meaning                                                            |
| ------------------------- | ------------------------------------------------------------------ |
| `public:`                 | Access specifier, constructor is publicly accessible               |
| `Point(int a=0, int b=0)` | Constructor with default parameters                                |
| `: x(a), y(b)`            | Member initializer list, initializes `x` with `a` and `y` with `b` |
| `{}`                      | Empty constructor body                                             |


Let's walk through this C++ program step by step, focusing on the **operator overloading** concept and how everything works.

---

## 🔍 What Does the Code Do?

You're creating a simple class `Point` that stores 2D coordinates and **overloads the `+` operator** to allow you to add two `Point` objects together.

## 🔧 Line-by-Line Explanation

### 🔹 Class Definition

```cpp
class Point {
  int x, y;
```

* Two private member variables `x` and `y` store the coordinates.

### 🔹 Constructor

```cpp
Point(int a = 0, int b = 0) : x(a), y(b) {}
```

* This constructor uses an **initializer list** to initialize `x` and `y`.
* You can create a point with custom coordinates or default (0,0).

### 🔹 Operator Overloading

```cpp
Point operator+(const Point& p) {
  return Point(x + p.x, y + p.y);
}
```

This is where the magic of **operator overloading** happens:

* You're **overloading the `+` operator** for the `Point` class.
* It returns a **new `Point`** where:

  * `x = this->x + p.x`
  * `y = this->y + p.y`
* Note: `p.x` and `p.y` are accessible because `p` is a `const Point&`.

### 🔹 Display Method

```cpp
void display() { cout << "(" << x << "," << y << ")" << endl; }
```

Prints the `Point` in coordinate format.

### 🔹 Main Function

```cpp
Point p1(1, 2), p2(3, 4);
```

* Creates two points:

  * `p1`: (1, 2)
  * `p2`: (3, 4)

```cpp
Point p3 = p1 + p2;
```

* Triggers the overloaded `+` operator
* Internally calls:

  ```cpp
  Point operator+(const Point& p)
  ```

  with `this = p1` and `p = p2`, resulting in `(1+3, 2+4) = (4,6)`

```cpp
p3.display(); // Output: (4,6)
```

* Prints the result.

## ✅ Output

```
(4,6)
```

## 📌 Visual Summary

| Object | x | y |
| ------ | - | - |
| p1     | 1 | 2 |
| p2     | 3 | 4 |
| p3     | 4 | 6 |







---
## ✅ Advanced Example

```cpp
class Point {
    const int id;
    int x, y;

public:
    // 'id' must be initialized in initializer list since it's const
    Point(int a = 0, int b = 0, int id_val = 1) : x(a), y(b), id(id_val) {
        cout << "Constructed Point with ID: " << id << endl;
    }
};
```


---

### 🔁 Common Overloadable Operators:

* Arithmetic: `+`, `-`, `*`, `/`
* Comparison: `==`, `!=`, `<`, `>`
* Assignment: `=`
* I/O: `<<`, `>>`
* Subscript: `[]`
* Function call: `()`

### ⚠️ Cannot Overload:

* `::`, `.*`, `.`, `?:`

### 🧠 Advanced Tip:

For stream overloading, use friend functions:

```cpp
friend ostream& operator<<(ostream& out, const Point& p);
```
