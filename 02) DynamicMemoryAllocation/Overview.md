# 🏗️ Dynamic Memory Allocation (DMA) in C++
### "Renting memory when you need it, returning it when you're done."

---

## 🧠 First — Two Types of Memory

Your computer has two main areas where your program stores data:

### 🥞 The Stack — "The Automatic Shelf"
- Memory is automatically given to you when you create a variable
- Memory is automatically taken back when the variable goes out of scope (when the `{}` block ends)
- Limited in size
- Very fast

```cpp
void myFunction() {
    int x = 10;   // Stack memory — created automatically
}                 // x is DESTROYED here automatically
```

### 🏗️ The Heap — "The Warehouse"
- A much larger area of memory
- YOU decide when to take memory and when to return it
- YOU are responsible for cleaning up
- This is where **Dynamic Memory Allocation** happens

```
Your Program's Memory:
┌─────────────────────────────┐
│         STACK               │ ← automatic, limited, fast
│   (your normal variables)   │
├─────────────────────────────┤
│                             │
│                             │
│          HEAP               │ ← manual, large, flexible
│   (dynamic memory)          │
│                             │
└─────────────────────────────┘
```

---

## 🤔 Why Do We Need DMA?

### Problem 1: You don't always know the size ahead of time

```cpp
// What if the user decides how many scores to store?
int n;
cout << "How many scores? ";
cin >> n;

// ❌ This is problematic in many C++ versions:
int scores[n];   // Can't use a variable as array size on stack

// ✅ DMA Solution:
int* scores = new int[n];   // Works perfectly!
```

### Problem 2: Data needs to survive beyond a function

```cpp
// Stack memory dies when function ends
int* badFunction() {
    int x = 10;
    return &x;   // ❌ DANGER! x dies when function ends!
}

// Heap memory stays alive until YOU delete it
int* goodFunction() {
    int* x = new int(10);
    return x;   // ✅ Safe! Heap memory persists!
}
```

---

## 🆕 The `new` Keyword — Renting Memory

`new` asks the operating system: **"Give me some memory on the heap!"**

### Allocating a Single Variable

```cpp
// Syntax: Type* pointer = new Type;
int* ptr = new int;       // allocate space for one integer
*ptr = 42;                // store 42 in that space

cout << *ptr;             // prints: 42
```

### Allocating with an Initial Value

```cpp
int* ptr = new int(100);      // allocate AND set value to 100
double* d = new double(3.14); // allocate AND set to 3.14

cout << *ptr;   // 100
cout << *d;     // 3.14
```

### What Happens in Memory?

```
int* ptr = new int(42);

STACK:                        HEAP:
┌──────────────┐             ┌──────────────┐
│  ptr         │────────────►│  42          │
│  (holds addr)│             │  (our data)  │
└──────────────┘             └──────────────┘
    small var                  actual data lives here
    on stack                   on the heap
```

---

## 🗑️ The `delete` Keyword — Returning Memory

When you're done with heap memory, you MUST give it back using `delete`.

```cpp
int* ptr = new int(42);   // rent memory
cout << *ptr;              // use it: prints 42

delete ptr;                // RETURN the memory!
ptr = nullptr;             // good habit: clear the pointer
```

> **Why must we delete?**
> Imagine renting a hotel room but never checking out. The room stays "occupied" even though you left. Other guests can't use it. This is called a **memory leak** and it's a serious bug!

---

## 📦 Allocating Arrays with DMA

### The Syntax

```cpp
// Syntax: Type* pointer = new Type[size];
int* arr = new int[5];    // allocate array of 5 integers
```

### Using the Dynamic Array

```cpp
int* arr = new int[5];

// Store values
arr[0] = 10;
arr[1] = 20;
arr[2] = 30;
arr[3] = 40;
arr[4] = 50;

// Read values
for (int i = 0; i < 5; i++) {
    cout << arr[i] << " ";   // 10 20 30 40 50
}
```

### Deleting an Array

```cpp
delete[] arr;   // NOTE: use delete[] for arrays (with square brackets!)
arr = nullptr;
```

> ⚠️ **Important:** Use `delete` for single values, `delete[]` for arrays. Using the wrong one is a bug!

---

## 🧪 Complete Step-by-Step Example

```cpp
#include <iostream>
using namespace std;

int main() {
    // Step 1: Ask user how many numbers
    int n;
    cout << "How many numbers do you want to store? ";
    cin >> n;

    // Step 2: Allocate array of that size on the heap
    int* numbers = new int[n];
    cout << "Memory allocated for " << n << " numbers!" << endl;

    // Step 3: Fill the array
    for (int i = 0; i < n; i++) {
        cout << "Enter number " << (i + 1) << ": ";
        cin >> numbers[i];
    }

    // Step 4: Display the numbers
    cout << "\nYou entered: ";
    for (int i = 0; i < n; i++) {
        cout << numbers[i] << " ";
    }
    cout << endl;

    // Step 5: Calculate sum
    int sum = 0;
    for (int i = 0; i < n; i++)
        sum += numbers[i];
    cout << "Sum: " << sum << endl;

    // Step 6: FREE the memory!
    delete[] numbers;
    numbers = nullptr;
    cout << "Memory freed!" << endl;

    return 0;
}
```

**Sample Output:**
```
How many numbers do you want to store? 4
Memory allocated for 4 numbers!
Enter number 1: 10
Enter number 2: 20
Enter number 3: 30
Enter number 4: 40

You entered: 10 20 30 40
Sum: 100
Memory freed!
```

---

## 🐛 Common Mistakes and How to Avoid Them

### ❌ Mistake 1: Memory Leak (Forgetting to delete)

```cpp
void badFunction() {
    int* ptr = new int(100);
    // ... do stuff ...
    // FORGOT to delete ptr!
}   // ptr is destroyed (stack), but the heap memory is STILL OCCUPIED!
    // This happens every time badFunction() is called — memory fills up!
```

```cpp
void goodFunction() {
    int* ptr = new int(100);
    // ... do stuff ...
    delete ptr;   // ✅ Memory returned
    ptr = nullptr;
}
```

### ❌ Mistake 2: Dangling Pointer (Using memory after delete)

```cpp
int* ptr = new int(42);
delete ptr;          // memory freed

cout << *ptr;        // ❌ UNDEFINED BEHAVIOR! Memory no longer yours!
// This might print garbage, crash, or seem to work — all bad!
```

```cpp
int* ptr = new int(42);
delete ptr;
ptr = nullptr;       // ✅ Now ptr is null

if (ptr != nullptr)  // ✅ Safe check
    cout << *ptr;
```

### ❌ Mistake 3: Double Delete

```cpp
int* ptr = new int(42);
delete ptr;   // freed
delete ptr;   // ❌ CRASH! Can't delete something twice!
```

```cpp
int* ptr = new int(42);
delete ptr;
ptr = nullptr;   // ✅ Set to null
delete ptr;      // ✅ Safe — deleting nullptr does nothing
```

### ❌ Mistake 4: Wrong Delete for Arrays

```cpp
int* arr = new int[10];
delete arr;     // ❌ Wrong! Should be delete[]
delete[] arr;   // ✅ Correct for arrays
```

---

## 📊 Stack vs Heap — Side by Side

| Feature            | Stack                    | Heap (DMA)                      |
|--------------------|--------------------------|----------------------------------|
| Size               | Small (1-8 MB typical)   | Large (GBs available)           |
| Speed              | Very fast                | Slightly slower                 |
| Management         | Automatic                | Manual (you control it)         |
| Lifetime           | Ends with scope `}`      | Until you `delete`              |
| Keyword to create  | Just declare a variable  | `new`                           |
| Keyword to free    | Automatic                | `delete` or `delete[]`          |
| Risk               | Stack overflow           | Memory leaks, dangling pointers |

---

## 🔄 DMA with Different Data Types

```cpp
// Integer
int* i = new int(42);
cout << *i;           // 42
delete i;

// Double
double* d = new double(3.14159);
cout << *d;           // 3.14159
delete d;

// Character
char* c = new char('A');
cout << *c;           // A
delete c;

// Boolean
bool* b = new bool(true);
cout << *b;           // 1
delete b;

// Array of doubles
double* arr = new double[3];
arr[0] = 1.1;
arr[1] = 2.2;
arr[2] = 3.3;
delete[] arr;
```

---

## 🏨 Real-World Analogy: Hotel Rooms

| Concept       | Hotel Analogy                                          |
|---------------|--------------------------------------------------------|
| Stack memory  | Your own home — always available, but limited space   |
| Heap memory   | A hotel — you rent rooms as needed                    |
| `new`         | Checking into a hotel room                            |
| `delete`      | Checking out (freeing the room for others)            |
| Memory leak   | Leaving forever without checking out — room is wasted |
| Dangling ptr  | Trying to use your room after checking out            |

---

## 🎯 Key Takeaways

1. **Stack** = automatic, small, fast. **Heap** = manual, large, flexible.
2. Use `new` to allocate heap memory
3. Use `delete` for single values, `delete[]` for arrays
4. ALWAYS `delete` what you `new` — or you'll have memory leaks
5. After deleting, set the pointer to `nullptr`
6. Never use a pointer after deleting it (dangling pointer)
7. Never delete the same memory twice

---
*Next up: Smart Pointers — C++'s way of making DMA automatic and safe!* →
