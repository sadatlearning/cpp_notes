## What is object chaining in C++?

 - **Object chaining** in C++ usually refers to **method chaining**, a design technique where multiple method calls are linked (or “chained”) together in a single expression, typically for improving readability and fluency.

### 🔗 **Example: Method Chaining**

```cpp
class Printer {
public:
    Printer& print(const std::string& msg) {
        std::cout << msg;
        return *this;  // Return current object by reference
    }

    Printer& newline() {
        std::cout << std::endl;
        return *this;
    }
};

int main() {
    Printer p;
    p.print("Hello, ").print("world!").newline();  // Chained calls
}
```

### ✅ Key Features:

* Each method returns a reference to the current object (`*this`), enabling the next method call.
* Helps create **fluent interfaces**, often used in:

  * Builder patterns
  * Stream operations
  * Configuration objects

### 🔨 Why Use Object Chaining?

* **Cleaner syntax**
* **Improved readability**
* **Concise code**

### ⚠️ Tips:

* Always return `*this` **by reference** (`ClassName&`) to avoid unnecessary copies.
* Be cautious with const correctness: if the method doesn't modify the object, consider returning `const ClassName&`.

---

##  A full builder pattern example using object chaining in C++?

Absolutely! Let’s first define **object chaining**, then show a hands-on **Builder Pattern** example using it in modern C++.

---

## 🔗 What is Object Chaining in C++?

**Object chaining** (also called **method chaining**) is a programming style where multiple method calls are linked together in a single statement. This is typically done by having each method return a reference to the object itself (`*this`).

### Example:

```cpp
object.setName("Foo").setAge(30).setAddress("123 Street");
```

This is particularly common in **Builder Pattern**, **fluent interfaces**, and DSL-style APIs.

---

## 🏗️ Full Example: Builder Pattern with Object Chaining

Let’s build a `Person` using a `PersonBuilder`.

```cpp
#include <iostream>
#include <string>

class Person {
    std::string name_;
    int age_;
    std::string address_;

public:
    void print() const {
        std::cout << "Name: " << name_
                  << ", Age: " << age_
                  << ", Address: " << address_ << "\n";
    }

    // Allow PersonBuilder to access private members
    friend class PersonBuilder;
};

class PersonBuilder {
    Person person_;

public:
    PersonBuilder& setName(const std::string& name) {
        person_.name_ = name;
        return *this;
    }

    PersonBuilder& setAge(int age) {
        person_.age_ = age;
        return *this;
    }

    PersonBuilder& setAddress(const std::string& address) {
        person_.address_ = address;
        return *this;
    }

    Person build() {
        return person_;  // Return the constructed person
    }
};

int main() {
    Person person = PersonBuilder()
                        .setName("Alice")
                        .setAge(30)
                        .setAddress("42 Wonderland Ave")
                        .build();

    person.print();  // Output: Name: Alice, Age: 30, Address: 42 Wonderland Ave
    return 0;
}
```

---

## ✅ Benefits of Object Chaining

* Clean, readable code
* Fewer temporary variables
* Enables fluent interface design
* Helps implement patterns like Builder and Configurators

---

## a **version with inheritance** (e.g., Director class), or an example using **templates for chaining configuration across base classes**?

- Let’s take it a step further by showing a **Builder Pattern with inheritance**, where a `Director` class orchestrates the building process.
- This setup is especially useful when you want to enforce consistent construction logic while still using **object chaining** for flexibility.

## 🏗️ Builder Pattern with Inheritance and a Director

We’ll extend the previous example to include:

* A `Person`
* A `PersonBuilder` (for fluent chaining)
* A `PersonDirector` (encapsulates building logic)


### ✅ Code

```cpp
#include <iostream>
#include <string>

class Person {
    std::string name_;
    int age_;
    std::string address_;

public:
    void print() const {
        std::cout << "Name: " << name_
                  << ", Age: " << age_
                  << ", Address: " << address_ << "\n";
    }

    friend class PersonBuilder;
};

// Base Builder
class PersonBuilder {
protected:
    Person person_;

public:
    PersonBuilder& setName(const std::string& name) {
        person_.name_ = name;
        return *this;
    }

    PersonBuilder& setAge(int age) {
        person_.age_ = age;
        return *this;
    }

    PersonBuilder& setAddress(const std::string& address) {
        person_.address_ = address;
        return *this;
    }

    virtual Person build() {
        return person_;
    }

    virtual ~PersonBuilder() = default;
};

// Director — configures the builder in a specific way
class PersonDirector {
public:
    Person createDefaultEmployee() {
        PersonBuilder builder;
        return builder
            .setName("Employee")
            .setAge(25)
            .setAddress("Company HQ")
            .build();
    }

    Person createTeenager(const std::string& name) {
        PersonBuilder builder;
        return builder
            .setName(name)
            .setAge(16)
            .setAddress("Unknown")
            .build();
    }
};

int main() {
    // Direct object chaining by user
    Person alice = PersonBuilder()
                    .setName("Alice")
                    .setAge(28)
                    .setAddress("42 Wonderland")
                    .build();
    alice.print();

    // Using a director for predefined construction logic
    PersonDirector director;
    Person employee = director.createDefaultEmployee();
    Person teen = director.createTeenager("Tom");

    employee.print();
    teen.print();

    return 0;
}
```

### ✅ Output

```
Name: Alice, Age: 28, Address: 42 Wonderland
Name: Employee, Age: 25, Address: Company HQ
Name: Tom, Age: 16, Address: Unknown
```

## 🧠 Benefits of Adding a Director

| Feature                     | Benefit                              |
| --------------------------- | ------------------------------------ |
| Centralized logic           | Ensures standard object construction |
| Decouples policy from steps | Easier testing and reuse             |
| Encourages consistency      | Avoids duplication across builders   |

---

Would you like a **template-based generic builder** that works across multiple types, or a **thread-safe version** of this pattern?

Let's now build a **template-based generic builder** in C++ that works with multiple types using **CRTP (Curiously Recurring Template Pattern)**. 
This enables reusable, type-safe object chaining across many builders.

## 🏗️ Generic Builder with CRTP

This allows defining a reusable base class for builders that return the derived type for chaining, and it avoids code duplication.


### ✅ Full Code

```cpp
#include <iostream>
#include <string>

// ==== Base entity ====
class Person {
    std::string name_;
    int age_;
    std::string address_;

public:
    void print() const {
        std::cout << "Name: " << name_
                  << ", Age: " << age_
                  << ", Address: " << address_ << "\n";
    }

    friend class PersonBuilder;
};

// ==== CRTP Generic Builder ====
template <typename T, typename Derived>
class BuilderBase {
protected:
    T object_;

public:
    Derived& setName(const std::string& name) {
        object_.name_ = name;
        return static_cast<Derived&>(*this);
    }

    Derived& setAge(int age) {
        object_.age_ = age;
        return static_cast<Derived&>(*this);
    }

    Derived& setAddress(const std::string& address) {
        object_.address_ = address;
        return static_cast<Derived&>(*this);
    }

    T build() {
        return object_;
    }
};

// ==== Person-specific Builder ====
class PersonBuilder : public BuilderBase<Person, PersonBuilder> {
    // Could add person-specific logic here
};

// ==== Example usage ====
int main() {
    Person p = PersonBuilder()
                    .setName("Charlie")
                    .setAge(40)
                    .setAddress("123 CRTP Blvd")
                    .build();

    p.print();
    return 0;
}
```

## ✅ Output

```
Name: Charlie, Age: 40, Address: 123 CRTP Blvd
```

## 🧠 Why Use CRTP for Builders?

| Feature              | Benefit                                |
| -------------------- | -------------------------------------- |
| Generic and reusable | Works across types                     |
| Chain-safe           | Maintains type safety in method chains |
| DRY                  | No duplicated code                     |
| Extensible           | Subclass and customize freely          |

---

Would you like to see a **thread-safe builder** using `std::mutex`, or a **builder that builds different object types** (e.g., Car, House) using the same CRTP base?


