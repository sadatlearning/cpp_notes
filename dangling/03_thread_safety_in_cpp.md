## What is thread safety in C++?

**Thread safety** in C++ means that **a piece of code or data structure can be safely used by multiple threads at the same time without causing race conditions, data corruption, or undefined behavior.**

### ✅ **Thread-safe code**:

* Prevents simultaneous conflicting accesses to shared data.
* Ensures **consistency** and **predictability** in multithreaded programs.

### 🔥 Common Issues Without Thread Safety:

* **Race conditions**: Two threads read/write the same data simultaneously.
* **Data races**: Undefined behavior due to unsynchronized access to shared variables.
* **Deadlocks**: Threads block each other waiting for resources.

### 🛠️ Techniques to Ensure Thread Safety in C++:

#### 1. **Mutexes (`std::mutex`, `std::lock_guard`)**

```cpp
#include <mutex>

std::mutex mtx;
int counter = 0;

void increment() {
    std::lock_guard<std::mutex> lock(mtx);
    ++counter;  // Protected by mutex
}
```

#### 2. **Atomic operations (`std::atomic`)**

```cpp
#include <atomic>

std::atomic<int> counter{0};

void increment() {
    counter.fetch_add(1);  // Thread-safe without mutex
}
```

#### 3. **Thread-safe containers and data structures**

* Use thread-safe wrappers or specialized libraries (e.g. Intel TBB, concurrent queues).
* Standard containers like `std::vector` are **not** thread-safe by default.

#### 4. **Avoid shared state when possible**

* Prefer thread-local storage (`thread_local`) or pass data by value.

#### 5. **Use `std::thread`, `std::async`, or thread pools with care**

* Always coordinate access to shared variables.

### ✅ Thread-safe Function Example:

```cpp
#include <mutex>

std::mutex mtx;

void print_safe(const std::string& msg) {
    std::lock_guard<std::mutex> lock(mtx);
    std::cout << msg << std::endl;
}
```
This function is safe to be called from multiple threads.

---

##  a hands-on example showing unsafe vs. safe counter updates using threads?


Absolutely! Let's explore **thread safety in C++** with a **hands-on example** using a counter shared across threads.

## 🔒 What is Thread Safety?

**Thread safety** means a function or data structure can be safely used by multiple threads **at the same time** without causing data races or undefined behavior.

## ⚠️ Example: **Unsafe Counter Update**

```cpp
#include <iostream>
#include <thread>

int counter = 0;

void increment() {
    for (int i = 0; i < 100000; ++i) {
        ++counter;  // 🚫 Not thread-safe
    }
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Final counter (unsafe): " << counter << "\n";  // Likely < 200000
    return 0;
}
```

### ❌ Problem:

* `++counter` is **not atomic**.
* Multiple threads read and write `counter` simultaneously → **data race**.
* Final result is **non-deterministic**.

## ✅ Example: **Safe Counter Using `std::mutex`**

```cpp
#include <iostream>
#include <thread>
#include <mutex>

int counter = 0;
std::mutex mtx;

void safe_increment() {
    for (int i = 0; i < 100000; ++i) {
        std::lock_guard<std::mutex> lock(mtx);  // lock per increment
        ++counter;
    }
}

int main() {
    std::thread t1(safe_increment);
    std::thread t2(safe_increment);

    t1.join();
    t2.join();

    std::cout << "Final counter (safe): " << counter << "\n";  // Always 200000
    return 0;
}
```

### ✅ Fix:

* `std::mutex` ensures **mutual exclusion**.
* Only one thread at a time updates `counter`.

## 🚀 Alternative: Use `std::atomic<int>`

```cpp
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<int> counter{0};

void atomic_increment() {
    for (int i = 0; i < 100000; ++i) {
        ++counter;  // ✅ Atomic, thread-safe
    }
}

int main() {
    std::thread t1(atomic_increment);
    std::thread t2(atomic_increment);

    t1.join();
    t2.join();

    std::cout << "Final counter (atomic): " << counter << "\n";  // Always 200000
    return 0;
}
```

## 🧠 Summary

| Method             | Thread Safe? | Notes                     |
| ------------------ | ------------ | ------------------------- |
| Raw `int`          | ❌ No         | Race condition            |
| `std::mutex`       | ✅ Yes        | Slower, flexible          |
| `std::atomic<int>` | ✅ Yes        | Fast, only for simple ops |

---

## A version using C++20 `std::jthread` or benchmarks comparing `mutex` vs `atomic` performance?

 - Let's extend the previous example to include:

1. ✅ A version using **C++20 `std::jthread`** (which auto-joins).
2. 📊 A simple **performance comparison** between `std::mutex` and `std::atomic`.

## ✅ C++20 Version with `std::jthread`

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <chrono>
#include <mutex>

int mutex_counter = 0;
std::atomic<int> atomic_counter = 0;
std::mutex mtx;

constexpr int INCREMENTS = 100000;

void mutex_increment() {
    for (int i = 0; i < INCREMENTS; ++i) {
        std::lock_guard<std::mutex> lock(mtx);
        ++mutex_counter;
    }
}

void atomic_increment() {
    for (int i = 0; i < INCREMENTS; ++i) {
        ++atomic_counter;
    }
}

int main() {
    using namespace std::chrono;

    std::cout << "--- Running with std::jthread (C++20) ---\n";

    // Measure mutex performance
    auto start_mutex = high_resolution_clock::now();
    {
        std::jthread t1(mutex_increment);
        std::jthread t2(mutex_increment);
    }
    auto end_mutex = high_resolution_clock::now();
    auto duration_mutex = duration_cast<milliseconds>(end_mutex - start_mutex);

    // Measure atomic performance
    auto start_atomic = high_resolution_clock::now();
    {
        std::jthread t3(atomic_increment);
        std::jthread t4(atomic_increment);
    }
    auto end_atomic = high_resolution_clock::now();
    auto duration_atomic = duration_cast<milliseconds>(end_atomic - start_atomic);

    // Results
    std::cout << "\nFinal mutex_counter  : " << mutex_counter << "\n";
    std::cout << "Final atomic_counter : " << atomic_counter << "\n";

    std::cout << "\n⏱️ Performance:\n";
    std::cout << "Mutex duration  : " << duration_mutex.count() << " ms\n";
    std::cout << "Atomic duration : " << duration_atomic.count() << " ms\n";

    return 0;
}
```

## 🧪 Sample Output (will vary by CPU):

```
--- Running with std::jthread (C++20) ---

Final mutex_counter  : 200000
Final atomic_counter : 200000

⏱️ Performance:
Mutex duration  : 30 ms
Atomic duration : 10 ms
```

## 🧠 Summary

| Feature            | `std::mutex`                  | `std::atomic`                |
| ------------------ | ----------------------------- | ---------------------------- |
| Safety             | ✅ Yes                         | ✅ Yes                        |
| Overhead           | 🐢 Higher (locks)             | ⚡ Lower (lock-free)          |
| Use case           | Complex compound operations   | Simple atomic operations     |
| C++ version needed | C++11                         | C++11                        |
| Best for           | Blocks of logic needing locks | Basic types (int, bool, ptr) |

---

Would you like a `thread_local` demo or an example where `atomic` is **not** sufficient and `mutex` is required (e.g., compound updates)?
