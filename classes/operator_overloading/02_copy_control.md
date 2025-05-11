## ✅ 2. Copy Control

### 📌 What is it?

**Copy control** is the set of mechanisms that govern **how objects are copied, assigned, and destroyed**.

### 🔑 The “Big Three” (also called Rule of Three in C++):

1. **Copy Constructor**
2. **Copy Assignment Operator**
3. **Destructor**

### 🔰 Example:

```cpp
class MyClass {
  int* data;

public:
  MyClass(int value) {
    data = new int(value);
  }

  // Copy Constructor
  MyClass(const MyClass& other) {
    data = new int(*other.data);
  }

  // Copy Assignment
  MyClass& operator=(const MyClass& other) {
    if (this != &other) {
      delete data;
      data = new int(*other.data);
    }
    return *this;
  }

  // Destructor
  ~MyClass() {
    delete data;
  }

  void display() {
    cout << "Value: " << *data << endl;
  }
};
```

## ✅ 3. Deep Copy

### 📌 What is Deep Copy?

A **deep copy** duplicates not just the object, but also all objects **it points to**.

### 🔁 When is it needed?

When your class contains **pointers**, and you want to **clone** the actual memory, not just the pointer address.

> ⚠️ Failing to do this leads to double-delete or dangling pointer errors.

#### 🔰 In the above `MyClass`, we performed **deep copy**.

## ✅ 4. Shallow Copy

### 📌 What is Shallow Copy?

A **shallow copy** just copies the pointer address, **not the content it points to**.

```cpp
class Shallow {
  int* data;

public:
  Shallow(int val) { data = new int(val); }

  // Default Copy Constructor = Shallow Copy
  void display() { cout << *data << endl; }
};
```

Now if you copy:

```cpp
Shallow a(10);
Shallow b = a; // Both point to same memory!
```

Modifying `*b.data` will affect `a` too.

## ✅ Summary Table

| Feature       | Shallow Copy               | Deep Copy                   |
| ------------- | -------------------------- | --------------------------- |
| Memory copied | Pointer address            | Actual data                 |
| Performance   | Faster                     | Slightly slower             |
| Risk          | Dangling/duplicate deletes | Safer, more memory overhead |

## ✅ Best Practices

* If your class manages resources (pointers, files, network handles), implement the **Big Three**.
* Use **smart pointers** (like `std::unique_ptr` or `std::shared_ptr`) in modern C++ to avoid manual memory management.
* In modern C++11+, also consider the **Rule of Five** (add Move Constructor & Move Assignment Operator).
