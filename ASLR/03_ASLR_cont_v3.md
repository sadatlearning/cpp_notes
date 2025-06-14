# 🚀 **C++ ASLR + Exploit Learning Lab: Complete Guide**

---

## ✅ **1️⃣ Full Memory Map Diagram (ASLR & no-ASLR)**

Let’s visualize your process memory layout.

---

### 🖼 **Without ASLR (no-PIE compile)**

```
Memory layout (fixed addresses):

+---------------------+ 0x7fffffffe000  <-- top of stack
|        Stack         | (fixed address stack)
+---------------------+
|       Heap           | (starts at fixed 0x602000)
+---------------------+
|  .data / .bss / .ro  | 0x601000
+---------------------+
|      Code / text     | 0x400000 (fixed code base)
+---------------------+
```

👉 `secret()` always at same address: e.g., `0x400656`

---

### 🖼 **With ASLR + PIE compile**

```
Memory layout (randomized):

+---------------------+ 0x7ffcfabc0000 (random stack base)
|        Stack         | (random stack address)
+---------------------+
|       Heap           | (randomized heap base)
+---------------------+
|  .data / .bss / .ro  | (randomized with PIE)
+---------------------+
|      Code / text     | 0x55ac2e300000 (random code base)
+---------------------+
```

👉 `secret()` at: e.g., `0x55ac2e301234` (changes each run!)

---

## ✅ **2️⃣ Minimal Info Leak C++ Example**

Let’s write a tiny bug that could leak an address.

```cpp
#include <iostream>

void leak() {
    int x = 12345;
    std::cout << "Address of x: " << &x << "\n";  // Unintended leak of stack addr
}

int main() {
    leak();
    return 0;
}
```

👉 Compiled with PIE + ASLR, this leaks stack address every time!
👉 Attacker can use this to compute where code/stack is.

Compile:

```bash
g++ -fPIE -pie -o leak_demo leak_demo.cpp
```

Run a few times — address changes (ASLR), but leak reveals the address each time.

---

## ✅ **3️⃣ GDB automation + payload tester**

Let’s create two files:

### `gdb_script.gdb`

```gdb
start
info address secret
info address vulnerable
quit
```

### Run automation

```bash
for i in {1..5}; do
  echo "Run $i"
  gdb -q -x gdb_script.gdb ./overflow_lab_pie | grep address
done
```

✅ This prints `secret()` + `vulnerable()` addresses across multiple runs — see ASLR randomness.

---

### Payload test (non-PIE)

Suppose `secret()` = `0x400656`:

```bash
python3 -c 'print("A"*16 + "B"*8 + "\x56\x06\x40\x00\x00\x00\x00\x00")' | ./overflow_lab_no_pie
```

✅ This jumps to `secret()`!

Test same payload with PIE:

```bash
python3 -c 'print("A"*16 + "B"*8 + "\x56\x06\x40\x00\x00\x00\x00\x00")' | ./overflow_lab_pie
```

❌ Fails → address is wrong → ASLR wins!

---

## ✅ **4️⃣ Compile with Canary, NX, RELRO**

🔹 **Stack Canary**

```bash
g++ -fstack-protector-strong -o overflow_lab_canary overflow_lab.cpp
```

✅ Detects overflow at return time, aborts if canary corrupted

---

🔹 **NX / DEP (default on Linux)**

```bash
g++ -z noexecstack -o overflow_lab_nx overflow_lab.cpp
```

✅ Marks stack non-executable → blocks injected shellcode

---

🔹 **RELRO**

```bash
g++ -Wl,-z,relro,-z,now -o overflow_lab_relro overflow_lab.cpp
```

✅ Protects GOT from overwrite

---

🔹 **Combine all**

```bash
g++ -fstack-protector-strong -fPIE -pie -z noexecstack -Wl,-z,relro,-z,now -o overflow_lab_secure overflow_lab.cpp
```

✅ Hardened binary: ASLR + canary + NX + RELRO + PIE
✅ Very hard to exploit!

---

## 🌟 **Summary**

👉 You now have:
✅ Full memory map understanding
✅ Example of info leak
✅ GDB script + tester automation
✅ Compile-time hardening options

---

## 🚀 **What next?**

💬 Would you like me to:

* 🖌 Draw a **proper graphic memory map** (I can generate an image!)
* 📜 Provide a ready **Makefile or shell script** to compile/test all configurations?
* 🐞 Help you **write a simple overflow exploit (for education)** that works without ASLR?


