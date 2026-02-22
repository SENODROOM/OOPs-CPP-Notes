# 🤖 Smart Pointers in C++
### "Pointers that clean up after themselves — like a robot maid for memory."

---

## 🤔 The Problem with Regular Pointers

In the last chapter, we learned that with DMA you MUST manually call `delete`.
But humans forget. And forgetting causes **memory leaks** and **crashes**.

```cpp
void riskyFunction() {
    int* data = new int[1000];

    if (someConditionFails) {
        return;   // ❌ OOPS! We returned early — delete[] never called!
    }

    // more code...
    delete[] data;   // Only reached if no early return
}
```

**Smart Pointers solve this problem entirely.** They automatically delete memory when they're done — no matter what happens.

> Think of a regular pointer like driving a car without a seatbelt.
> A smart pointer is the seatbelt — it protects you automatically.

---

## 📦 What is a Smart Pointer?

A **smart pointer** is a special object that:
1. Acts like a regular pointer (you can use `*` and `->` on it)
2. **Automatically deletes** the memory when it's no longer needed
3. Lives in the `<memory>` header file

```cpp
#include <memory>   // Required for smart pointers
```

There are **3 types** of smart pointers in C++:
| Type          | Ownership       | Best For                        |
|---------------|-----------------|----------------------------------|
| `unique_ptr`  | Single owner    | Most situations (default choice) |
| `shared_ptr`  | Multiple owners | When data is shared              |
| `weak_ptr`    | No ownership    | Observing without owning         |

---

## 1️⃣ `unique_ptr` — The Sole Owner

### What is it?
`unique_ptr` means **ONE and only ONE** pointer owns the resource.
When that pointer goes away (out of scope), the memory is automatically freed.

### Real-Life Analogy
> Like owning a car. Only YOU own it. You can sell it (transfer ownership),
> but you can't give copies of it to multiple people.

### Creating a `unique_ptr`

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
    // Old way (regular pointer — manual delete needed)
    int* old = new int(42);
    cout << *old;
    delete old;   // must remember this!

    // New way (unique_ptr — automatic!)
    unique_ptr<int> smart = make_unique<int>(42);
    cout << *smart;   // works just like a regular pointer
    // NO delete needed! Memory freed automatically when smart goes out of scope
}
```

### How to Create (Syntax)

```cpp
// unique_ptr<DataType> name = make_unique<DataType>(value);

unique_ptr<int>    uptr = make_unique<int>(100);
unique_ptr<double> dptr = make_unique<double>(3.14);
unique_ptr<char>   cptr = make_unique<char>('A');
```

### Automatic Cleanup in Action

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
    cout << "Start of main" << endl;

    {   // New block (scope)
        unique_ptr<int> ptr = make_unique<int>(99);
        cout << "Inside block: " << *ptr << endl;
    }   // ← ptr goes out of scope HERE — memory AUTOMATICALLY freed!

    cout << "After block — memory is already freed!" << endl;

    return 0;
}
```

**Output:**
```
Start of main
Inside block: 99
After block — memory is already freed!
```

### You CANNOT Copy a `unique_ptr`

```cpp
unique_ptr<int> ptr1 = make_unique<int>(42);
unique_ptr<int> ptr2 = ptr1;   // ❌ ERROR! Can't copy — unique means ONE owner!
```

### But You CAN Transfer Ownership (Move)

```cpp
unique_ptr<int> ptr1 = make_unique<int>(42);
unique_ptr<int> ptr2 = move(ptr1);   // ✅ Transfer ownership to ptr2

// Now ptr1 is EMPTY (nullptr), ptr2 owns the resource
cout << *ptr2;    // 42
// cout << *ptr1; // ❌ CRASH! ptr1 no longer owns anything
```

```
Before move:
ptr1 ──────────────► [ 42 ]     ptr2 ──► nothing

After move:
ptr1 ──► nothing     ptr2 ──────────────► [ 42 ]
```

### `unique_ptr` with Arrays

```cpp
unique_ptr<int[]> arr = make_unique<int[]>(5);   // array of 5 ints

arr[0] = 10;
arr[1] = 20;
arr[2] = 30;

cout << arr[0];   // 10
// Automatically deleted when arr goes out of scope — no delete[] needed!
```

---

## 2️⃣ `shared_ptr` — Multiple Owners

### What is it?
`shared_ptr` allows **MULTIPLE pointers** to share ownership of the same resource.
The memory is only freed when the **LAST** owner is gone.

It keeps a **reference count** — a counter of how many pointers own the resource.

### Real-Life Analogy
> Like a shared Netflix subscription. Multiple people can use it.
> The subscription (resource) exists as long as at least one person is subscribed.
> When the LAST person cancels, it's gone.

### Creating a `shared_ptr`

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
    shared_ptr<int> sp1 = make_shared<int>(100);
    cout << "Value: " << *sp1 << endl;           // 100
    cout << "Owners: " << sp1.use_count() << endl; // 1

    shared_ptr<int> sp2 = sp1;   // ✅ Copy is ALLOWED! Both own it now.
    cout << "Owners: " << sp1.use_count() << endl; // 2

    shared_ptr<int> sp3 = sp1;   // Another owner
    cout << "Owners: " << sp1.use_count() << endl; // 3

    sp3.reset();   // sp3 gives up ownership
    cout << "Owners: " << sp1.use_count() << endl; // 2

}   // sp1 and sp2 go out of scope → count goes to 0 → memory freed!
```

### Reference Count Visualization

```
Step 1: shared_ptr<int> sp1 = make_shared<int>(100);
        sp1 ──────────────► [ 100 | count: 1 ]

Step 2: shared_ptr<int> sp2 = sp1;
        sp1 ──────────────► [ 100 | count: 2 ] ◄────── sp2

Step 3: shared_ptr<int> sp3 = sp1;
        sp1 ──► [ 100 | count: 3 ] ◄── sp2
                              ▲
                              └────── sp3

Step 4: sp3.reset();
        sp1 ──► [ 100 | count: 2 ] ◄── sp2
        sp3 ──► nothing

Step 5: sp1 and sp2 go out of scope → count = 0 → DELETE!
```

### Useful Methods

```cpp
shared_ptr<int> sp = make_shared<int>(50);

sp.use_count();    // how many owners?
sp.get();          // get the raw pointer
sp.reset();        // give up ownership
sp == nullptr;     // check if empty
```

---

## 3️⃣ `weak_ptr` — The Observer

### What is it?
`weak_ptr` can **look at** a `shared_ptr`'s resource WITHOUT becoming an owner.
It doesn't increase the reference count.

### Why Do We Need It?

Imagine two objects that each hold a `shared_ptr` to each other:

```
ObjectA ──shared_ptr──► ObjectB ──shared_ptr──► ObjectA
  ↑__________________________________________________↑
         (circular reference — neither ever deleted!)
```

This creates a **circular reference** — count never reaches 0, memory is never freed!

`weak_ptr` breaks this cycle by observing without owning.

### Real-Life Analogy
> Imagine a club with members. `shared_ptr` is a full member (counted in attendance).
> `weak_ptr` is a guest observer — they're present but not counted as members.
> The club closes when all MEMBERS (not guests) leave.

### Using `weak_ptr`

```cpp
#include <iostream>
#include <memory>
using namespace std;

int main() {
    shared_ptr<int> sp = make_shared<int>(77);
    weak_ptr<int> wp = sp;   // observe without owning

    cout << "Shared count: " << sp.use_count() << endl;  // 1 (weak_ptr not counted!)

    // To USE a weak_ptr, you must "lock" it (get a temporary shared_ptr)
    if (auto locked = wp.lock()) {   // lock() returns shared_ptr if still alive
        cout << "Value: " << *locked << endl;   // 77
    } else {
        cout << "Resource is gone!" << endl;
    }

    sp.reset();   // destroy the shared_ptr

    // Check if still alive
    if (auto locked = wp.lock()) {
        cout << "Value: " << *locked << endl;
    } else {
        cout << "Resource is gone!" << endl;   // This prints now
    }
}
```

**Output:**
```
Shared count: 1
Value: 77
Resource is gone!
```

---

## ⚔️ Smart Pointers vs Raw Pointers — Side by Side

```cpp
// ============ RAW POINTER (old, risky) ============
int* raw = new int(42);
cout << *raw;
delete raw;        // ← must remember
raw = nullptr;     // ← must remember
// If you forget delete → memory leak!
// If exception thrown → memory leak!

// ============ SMART POINTER (modern, safe) ============
auto smart = make_unique<int>(42);
cout << *smart;
// That's it! No delete, no nullptr setting — all automatic!
```

---

## 🧪 Complete Practical Example — A Student Record

```cpp
#include <iostream>
#include <memory>
#include <string>
using namespace std;

struct Student {
    string name;
    int grade;

    Student(string n, int g) : name(n), grade(g) {
        cout << "Student " << name << " created" << endl;
    }

    ~Student() {
        cout << "Student " << name << " destroyed" << endl;
    }
};

void demonstrateUnique() {
    cout << "\n--- unique_ptr Demo ---" << endl;

    auto s1 = make_unique<Student>("Alice", 95);
    cout << s1->name << " got " << s1->grade << endl;

    // Transfer ownership
    auto s2 = move(s1);
    cout << s2->name << " (now owned by s2)" << endl;

}   // s2 goes out of scope → Student Alice destroyed

void demonstrateShared() {
    cout << "\n--- shared_ptr Demo ---" << endl;

    auto s1 = make_shared<Student>("Bob", 88);
    cout << "Owners: " << s1.use_count() << endl;   // 1

    {
        auto s2 = s1;   // Both own Bob
        cout << "Owners: " << s1.use_count() << endl;  // 2
        cout << s2->name << " still alive" << endl;
    }   // s2 gone, but Bob is NOT deleted yet!

    cout << "Owners: " << s1.use_count() << endl;   // 1
    cout << s1->name << " still alive" << endl;

}   // s1 gone → count = 0 → Bob destroyed

int main() {
    demonstrateUnique();
    demonstrateShared();
    cout << "\nMain ends" << endl;
    return 0;
}
```

**Output:**
```
--- unique_ptr Demo ---
Student Alice created
Alice got 95
Alice (now owned by s2)
Student Alice destroyed

--- shared_ptr Demo ---
Student Bob created
Owners: 1
Owners: 2
Bob still alive
Owners: 1
Bob still alive
Student Bob destroyed

Main ends
```

---

## 📊 Comparison Table

| Feature              | `unique_ptr`          | `shared_ptr`              | `weak_ptr`               |
|----------------------|-----------------------|---------------------------|--------------------------|
| Ownership            | Single                | Shared (many)             | None (observer)          |
| Can be copied?       | ❌ No                 | ✅ Yes                    | ✅ Yes                   |
| Can be moved?        | ✅ Yes                | ✅ Yes                    | ✅ Yes                   |
| Reference count?     | No                    | Yes                       | No (doesn't count)       |
| Auto-delete when?    | Goes out of scope     | Last owner gone           | Never (doesn't own)      |
| Overhead             | Zero (same as raw)    | Small (counter)           | Small                    |
| Use when             | Default choice        | Multiple owners needed    | Breaking circular refs   |

---

## 🎯 When to Use Which?

```
Need a pointer?
│
├── Will only ONE thing own it?
│   └── YES → use unique_ptr ✅ (most common choice)
│
├── Will MULTIPLE things share it?
│   └── YES → use shared_ptr ✅
│
└── Need to OBSERVE a shared_ptr without owning?
    └── YES → use weak_ptr ✅
```

---

## 🎯 Key Takeaways

1. Smart pointers **automatically manage memory** — no manual `delete` needed
2. `unique_ptr` = one owner, zero overhead, cannot be copied (only moved)
3. `shared_ptr` = multiple owners, tracks count, deletes when count = 0
4. `weak_ptr` = observer only, doesn't own, used to break circular references
5. Use `make_unique<>()` and `make_shared<>()` to create them
6. Access members with `->` just like regular pointers: `ptr->name`
7. **Prefer smart pointers over raw pointers** in modern C++ (C++11 and later)

---
*Next up: 1D Arrays — storing multiple values in a row!* →
