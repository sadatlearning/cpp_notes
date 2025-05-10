## template-based generic builder

- Would you like a template-based generic builder that works across multiple types, or a thread-safe version of this pattern?

 - Let's now build a **template-based generic builder** in C++ that works with multiple types using **CRTP (Curiously Recurring Template Pattern)**.
 - This enables reusable, type-safe object chaining across many builders.

## 🏗️ Generic Builder with CRTP

 - This allows defining a reusable base class for builders that return the derived type for chaining, and it avoids code duplication.

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
