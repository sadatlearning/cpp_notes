# Quiz - #01

⚡ - Practice & debugging tasks are the best way to **really** master move semantics.
   - Here’s a **quiz-style practice set** (with increasing difficulty), similar to interview exercises.

---

# 📝 Move Semantics Practice & Debugging Tasks

---

## **Section 1: Predict the Output**

*(Try to answer before running the code — think about which constructor/operator gets called.)*

### Q1

```cpp
#include <iostream>
#include <string>
struct X {
    std::string s;
    X(const std::string& str) : s(str) { std::cout << "Constructed\n"; }
    X(const X& other) : s(other.s) { std::cout << "Copied\n"; }
    X(X&& other) noexcept : s(std::move(other.s)) { std::cout << "Moved\n"; }
};

int main() {
    X a("hello");
    X b = a;
    X c = std::move(a);
}
```

👉 **Question:** What’s the exact output?
(Hint: `a` is lvalue, `std::move(a)` makes it rvalue.)

---

### Q2

```cpp
#include <iostream>
#include <vector>
#include <string>
int main() {
    std::vector<std::string> v;
    std::string s = "world";
    v.push_back(s);
    v.push_back(std::move(s));
}
```

👉 **Question:** Which call copies, which call moves?
👉 What happens to `s` after the second push?

---

---

## **Section 2: Debugging Tasks**

### Q3 — Wrong use of `std::move`

```cpp
#include <iostream>
#include <string>

std::string makeString() {
    std::string str = "data";
    return std::move(str);
}

int main() {
    std::string s = makeString();
    std::cout << s << "\n";
}
```

👉 **Question:** Is `std::move(str)` needed here?
(Hint: Return Value Optimization / NRVO rules apply.)

---

### Q4 — Forgotten Move Assignment

```cpp
#include <iostream>
#include <vector>

struct Buffer {
    std::vector<int> data;
    Buffer(size_t n) : data(n, 42) {}
    // Missing move constructor & move assignment!
};

int main() {
    Buffer b1(1000000);
    Buffer b2 = std::move(b1); // what happens?
}
```

👉 **Question:** Does this move or copy?
👉 Fix it by adding a move constructor.

---

---

## **Section 3: Conceptual Traps**

### Q5 — Copy vs Move Priority

```cpp
#include <iostream>
struct A {
    A() {}
    A(const A&) { std::cout << "Copied\n"; }
    A(A&&) { std::cout << "Moved\n"; }
};

A makeA(A a) { return a; }

int main() {
    A x;
    A y = makeA(x);
}
```

👉 **Question:** Will it print `"Copied"` or `"Moved"`? Why?
(Hint: function parameters are lvalues inside the function body.)

---

### Q6 — Moved-From State

```cpp
#include <iostream>
#include <string>

int main() {
    std::string s = "test";
    std::string t = std::move(s);

    std::cout << "s = [" << s << "]\n";
    std::cout << "t = [" << t << "]\n";
}
```

👉 **Question:** Is `s` empty after the move?
👉 What does the standard guarantee about `s`?

---

---

## **Section 4: Mini Debug Challenge**

### Q7 — Perfect Forwarding Bug

```cpp
#include <iostream>
#include <utility>

void foo(const std::string& s) { std::cout << "foo(const&): " << s << "\n"; }
void foo(std::string&& s) { std::cout << "foo(&&): " << s << "\n"; }

template<typename T>
void wrapper(T&& arg) {
    foo(arg);  // bug?
}

int main() {
    std::string s = "Hello";
    wrapper(s);
    wrapper(std::string("World"));
}
```

👉 **Question:** Why does the second call not use the rvalue overload?
👉 Fix it with the correct forwarding.

---

# ✅ Deliverables

* **Section 1:** Think through output — run to confirm.
* **Section 2:** Debug and fix by adding move constructors/assignments.
* **Section 3:** Explain why copy/move is chosen.
* **Section 4:** Fix forwarding bug with `std::forward<T>(arg)`.

---
