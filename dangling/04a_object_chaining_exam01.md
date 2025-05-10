## directory class 

- Would you like a version with inheritance (e.g., Director class), or an example using templates for chaining configuration across base classes?

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
