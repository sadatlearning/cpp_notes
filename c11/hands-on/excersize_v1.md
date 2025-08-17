Perfect 👍 let’s build a **step-by-step hands-on exercise series** for learning **move semantics in C++**, very similar to the way we explored **copy control (Rule of 3/5)** earlier.

We’ll start from the fundamentals and gradually increase complexity.

---

# 🛠 Move Semantics Exercise Series (Step-by-Step)

---

## **Step 1: First Move Constructor**

👉 Goal: Understand the difference between copy and move.

### Task

1. Create a `Simple` class holding a `std::string name`.
2. Implement:

   * **Default constructor** (prints `"Constructed"`).
   * **Copy constructor** (prints `"Copied"`).
   * **Move constructor** (prints `"Moved"`).
3. In `main()`, do:

   ```cpp
   Simple a("A");
   Simple b = a;              // should call copy
   Simple c = Simple("Temp"); // should call move
   ```

### Expected Output

```
Constructed: A
Copied
Constructed: Temp
Moved
```

---

## **Step 2: Implement Move Assignment**

👉 Goal: Learn how assignment differs from construction.

### Task

1. Extend your `Simple` class with:

   * **Copy assignment** (prints `"Copy Assigned"`).
   * **Move assignment** (prints `"Move Assigned"`).
2. In `main()`:

   ```cpp
   Simple a("One"), b("Two");
   b = a;                     // copy assignment
   b = Simple("Three");       // move assignment
   ```

### Expected Output

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

👉 Goal: See why move matters when managing resources.

### Task

1. Write a class `Buffer` that holds:

   ```cpp
   int* data;
   size_t size;
   ```
2. Implement:

   * Constructor: allocates `new int[size]`.
   * Destructor: deletes `data`.
   * Copy constructor: deep copy.
   * Copy assignment: deep copy.
   * Move constructor: steal pointer.
   * Move assignment: steal pointer.
3. Test with:

   ```cpp
   Buffer b1(1000000);     // big buffer
   Buffer b2 = b1;         // copy (expensive)
   Buffer b3 = std::move(b1); // move (cheap)
   ```

👉 Compare performance by printing messages for copy vs move.

---

## **Step 4: std::vector and Return Value Optimization**

👉 Goal: Observe how STL containers benefit from move.

### Task

1. Write:

   ```cpp
   Buffer makeBuffer() {
       Buffer b(1000000);
       return b; // should move, not copy
   }

   int main() {
       Buffer b = makeBuffer();
   }
   ```
2. Check if **Move constructor** is called.
3. Add:

   ```cpp
   std::vector<Buffer> v;
   v.push_back(Buffer(5000));  // move into vector
   v.push_back(b);             // copy (since lvalue)
   v.push_back(std::move(b));  // move (force)
   ```

---

## **Step 5: Moved-From State**

👉 Goal: Understand object validity after move.

### Task

1. Print buffer size before and after move:

   ```cpp
   Buffer b1(10);
   Buffer b2 = std::move(b1);
   std::cout << "b1.size = " << b1.getSize() << "\n";
   std::cout << "b2.size = " << b2.getSize() << "\n";
   ```
2. Decide how to represent a “moved-from” object (commonly set size = 0 and data = nullptr).

---

## **Step 6: std::move vs std::forward**

👉 Goal: Intro to perfect forwarding.

### Task

1. Write:

   ```cpp
   template<typename T>
   void wrapper(T&& arg) {
       std::cout << "Wrapper called\n";
       foo(std::forward<T>(arg));  // preserves lvalue/rvalue
   }
   ```
2. Test with both lvalues and rvalues.
3. Observe how `std::move` always moves, but `std::forward` keeps the “value category”.

---

## **Step 7: Integration Project**

👉 Goal: Apply everything.

Build a small **StringPool** class:

* Internally holds `std::vector<MyString>`.
* Supports:

  ```cpp
  void add(std::string s);              // move into vector
  void add(const std::string& s);       // copy
  void add(std::string&& s);            // move (overload)
  ```
* Test with:

  ```cpp
  pool.add("Hello");    // move
  std::string temp = "World";
  pool.add(temp);       // copy
  pool.add(std::move(temp)); // move
  ```

---

# ✅ Learning Roadmap

* Step 1 → Understand basics (`copy` vs `move`)
* Step 2 → Assignment operators
* Step 3 → Resource management (Rule of 5)
* Step 4 → See real performance with STL
* Step 5 → Handle moved-from state
* Step 6 → Perfect forwarding (`std::forward`)
* Step 7 → Mini project to integrate

---

