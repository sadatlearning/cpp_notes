
Let’s now extend the **CRTP-based generic builder** to support **multiple object types** like `Person`, `Car`, and `House`, all sharing the same fluent chaining base.
Each type will have its own specific builder subclass but will reuse the same generic infrastructure.

## 🏗️ Multi-Type Builder Using CRTP

We’ll build:

* `Person`, `Car`, and `House` classes
* A generic `BuilderBase<T, Derived>`
* Type-specific builders: `PersonBuilder`, `CarBuilder`, `HouseBuilder`

### ✅ Full Code Example

```cpp
#include <iostream>
#include <string>

// ==== Entities ====
class Person {
    std::string name_;
    int age_;
    std::string address_;
public:
    void print() const {
        std::cout << "[Person] Name: " << name_ << ", Age: " << age_ << ", Address: " << address_ << "\n";
    }
    friend class PersonBuilder;
};

class Car {
    std::string model_;
    int year_;
public:
    void print() const {
        std::cout << "[Car] Model: " << model_ << ", Year: " << year_ << "\n";
    }
    friend class CarBuilder;
};

class House {
    std::string street_;
    int rooms_;
public:
    void print() const {
        std::cout << "[House] Street: " << street_ << ", Rooms: " << rooms_ << "\n";
    }
    friend class HouseBuilder;
};

// ==== Generic CRTP Builder Base ====
template <typename T, typename Derived>
class BuilderBase {
protected:
    T object_;

public:
    T build() {
        return object_;
    }
};

// ==== PersonBuilder ====
class PersonBuilder : public BuilderBase<Person, PersonBuilder> {
public:
    PersonBuilder& setName(const std::string& name) {
        object_.name_ = name;
        return *this;
    }

    PersonBuilder& setAge(int age) {
        object_.age_ = age;
        return *this;
    }

    PersonBuilder& setAddress(const std::string& address) {
        object_.address_ = address;
        return *this;
    }
};

// ==== CarBuilder ====
class CarBuilder : public BuilderBase<Car, CarBuilder> {
public:
    CarBuilder& setModel(const std::string& model) {
        object_.model_ = model;
        return *this;
    }

    CarBuilder& setYear(int year) {
        object_.year_ = year;
        return *this;
    }
};

// ==== HouseBuilder ====
class HouseBuilder : public BuilderBase<House, HouseBuilder> {
public:
    HouseBuilder& setStreet(const std::string& street) {
        object_.street_ = street;
        return *this;
    }

    HouseBuilder& setRooms(int rooms) {
        object_.rooms_ = rooms;
        return *this;
    }
};

// ==== Demo ====
int main() {
    Person person = PersonBuilder()
                        .setName("Eve")
                        .setAge(29)
                        .setAddress("Moonlight Street")
                        .build();

    Car car = CarBuilder()
                  .setModel("Tesla Model 3")
                  .setYear(2024)
                  .build();

    House house = HouseBuilder()
                      .setStreet("Baker Street")
                      .setRooms(5)
                      .build();

    person.print();
    car.print();
    house.print();

    return 0;
}
```

---

### ✅ Sample Output

```
[Person] Name: Eve, Age: 29, Address: Moonlight Street
[Car] Model: Tesla Model 3, Year: 2024
[House] Street: Baker Street, Rooms: 5
```

## 🧠 Benefits of This Pattern

| Feature            | Benefit                                     |
| ------------------ | ------------------------------------------- |
| Type-safe chaining | Each builder is fluent and strongly typed   |
| Reusable base      | Reduces duplication                         |
| Easy to extend     | Just subclass and add type-specific methods |
| Open for growth    | Add `PhoneBuilder`, `RobotBuilder`, etc.    |

---

Would you now like to add **thread-safety** (e.g. for multithreaded construction scenarios), or an example where the **builder validates the object** before returning it?
