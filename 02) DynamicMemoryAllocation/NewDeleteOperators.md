# 🆕 New and Delete Operators
### "The keys to the warehouse - renting and returning memory"

---

## 🎯 Core Concept

The `new` and `delete` operators are C++'s tools for **dynamic memory allocation**. They allow you to request memory from the heap and return it when you're done.

### The Rental Car Analogy

```
new = Rent a car from rental agency
delete = Return the car to rental agency

┌─────────────────┐                ┌─────────────────┐
│  Rental Agency │                │  Rental Agency │
│  (Heap Manager) │                │  (Heap Manager) │
│ ┌─────────────┐ │                │ ┌─────────────┐ │
│ │ Car #123   │ │                │ │ Car #123   │ │
│ │ Available  │ │                │ │ Rented     │ │
│ └─────────────┘ │                │ └─────────────┘ │
└─────────────────┘                └─────────────────┘
      ▲                                   ▲
      │                                   │
      └───────────────────────────────────┘
           Your Program
```

---

## 🏗️ The `new` Operator

### Basic Syntax

```cpp
// Single object
pointer = new Type;

// Array
pointer = new Type[size];

// With initialization
pointer = new Type(initial_value);
```

### How `new` Works

1. **Calculate size** needed for the object
2. **Find free memory** on the heap
3. **Allocate the memory** and mark it as used
4. **Call constructor** (if any)
5. **Return pointer** to allocated memory

### Single Object Allocation

```cpp
#include <iostream>

void basicNewExample() {
    std::cout << "=== Basic new Example ===" << std::endl;
    
    // Allocate integer
    int* intPtr = new int(42);
    std::cout << "Allocated integer: " << *intPtr << std::endl;
    
    // Allocate double
    double* doublePtr = new double(3.14159);
    std::cout << "Allocated double: " << *doublePtr << std::endl;
    
    // Allocate character
    char* charPtr = new char('Z');
    std::cout << "Allocated character: " << *charPtr << std::endl;
    
    // Don't forget to clean up!
    delete intPtr;
    delete doublePtr;
    delete charPtr;
}
```

### Array Allocation

```cpp
void arrayNewExample() {
    std::cout << "=== Array new Example ===" << std::endl;
    
    // Allocate array of integers
    int* intArray = new int[5];
    
    // Initialize the array
    for (int i = 0; i < 5; i++) {
        intArray[i] = i * 10;
    }
    
    std::cout << "Array: ";
    for (int i = 0; i < 5; i++) {
        std::cout << intArray[i] << " ";
    }
    std::cout << std::endl;
    
    // Clean up with delete[]
    delete[] intArray;
}
```

### Object Allocation

```cpp
class Student {
private:
    std::string name;
    int age;
    
public:
    Student(const std::string& n, int a) : name(n), age(a) {
        std::cout << "Student " << name << " (age " << age << ") created" << std::endl;
    }
    
    ~Student() {
        std::cout << "Student " << name << " destroyed" << std::endl;
    }
    
    void display() const {
        std::cout << name << " is " << age << " years old" << std::endl;
    }
};

void objectNewExample() {
    std::cout << "=== Object new Example ===" << std::endl;
    
    // Allocate object (constructor called automatically)
    Student* student = new Student("Alice", 20);
    
    student->display();
    
    // Clean up (destructor called automatically)
    delete student;
}
```

**Output:**
```
=== Object new Example ===
Student Alice (age 20) created
Alice is 20 years old
Student Alice destroyed
```

---

## 🗑️ The `delete` Operator

### Basic Syntax

```cpp
// Single object
delete pointer;

// Array
delete[] pointer;
```

### How `delete` Works

1. **Call destructor** (if any)
2. **Mark memory as free** on the heap
3. **Return memory** to available pool
4. **Pointer becomes invalid** (dangling)

### Single Object Deallocation

```cpp
void basicDeleteExample() {
    std::cout << "=== Basic delete Example ===" << std::endl;
    
    // Allocate
    int* ptr = new int(99);
    std::cout << "Before delete: " << *ptr << std::endl;
    
    // Deallocate
    delete ptr;
    ptr = nullptr;  // Good practice: set to nullptr
    
    std::cout << "After delete: pointer is " << (ptr ? "valid" : "null") << std::endl;
}
```

### Array Deallocation

```cpp
void arrayDeleteExample() {
    std::cout << "=== Array delete Example ===" << std::endl;
    
    // Allocate array
    int* arr = new int[3];
    arr[0] = 10;
    arr[1] = 20;
    arr[2] = 30;
    
    std::cout << "Before delete: ";
    for (int i = 0; i < 3; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    // Deallocate array
    delete[] arr;
    arr = nullptr;
    
    std::cout << "After delete: array is deallocated" << std::endl;
}
```

---

## 🔀 Advanced New/Delete Operations

### Placement New

```cpp
#include <new>

void placementNewExample() {
    std::cout << "=== Placement new Example ===" << std::endl;
    
    // Allocate raw memory
    char* buffer = new char[sizeof(Student)];
    
    // Construct object in pre-allocated memory
    Student* student = new(buffer) Student("Bob", 22);
    student->display();
    
    // Explicitly call destructor (don't use delete!)
    student->~Student();
    
    // Free the raw memory
    delete[] buffer;
}
```

### Nothrow New

```cpp
void nothrowNewExample() {
    std::cout << "=== Nothrow new Example ===" << std::endl;
    
    // Try to allocate huge amount of memory
    int* ptr = new(std::nothrow) int[1000000000];
    
    if (ptr) {
        std::cout << "Allocation successful!" << std::endl;
        delete[] ptr;
    } else {
        std::cout << "Allocation failed!" << std::endl;
    }
}
```

### Custom New/Delete

```cpp
class CustomMemory {
private:
    static int allocationCount;
    
public:
    // Custom new operator
    void* operator new(size_t size) {
        std::cout << "Custom new called, size: " << size << std::endl;
        allocationCount++;
        return ::operator new(size);
    }
    
    // Custom delete operator
    void operator delete(void* ptr) {
        std::cout << "Custom delete called" << std::endl;
        allocationCount--;
        ::operator delete(ptr);
    }
    
    static int getAllocationCount() { return allocationCount; }
};

int CustomMemory::allocationCount = 0;

void customNewDeleteExample() {
    std::cout << "=== Custom new/delete Example ===" << std::endl;
    
    std::cout << "Allocations: " << CustomMemory::getAllocationCount() << std::endl;
    
    CustomMemory* obj = new CustomMemory();
    std::cout << "Allocations: " << CustomMemory::getAllocationCount() << std::endl;
    
    delete obj;
    std::cout << "Allocations: " << CustomMemory::getAllocationCount() << std::endl;
}
```

---

## ⚠️ Common Mistakes

### 1. Memory Leak

```cpp
void memoryLeak() {
    int* ptr = new int(42);
    // ❌ Forgot to delete ptr!
    // Memory is leaked when function ends
}
```

### 2. Double Delete

```cpp
void doubleDelete() {
    int* ptr = new int(42);
    delete ptr;        // ✅ First delete - OK
    delete ptr;        // ❌ Second delete - CRASH!
}
```

### 3. Dangling Pointer

```cpp
void danglingPointer() {
    int* ptr = new int(42);
    delete ptr;
    std::cout << *ptr;  // ❌ Accessing freed memory - UNDEFINED BEHAVIOR!
}
```

### 4. Wrong Delete for Arrays

```cpp
void wrongArrayDelete() {
    int* arr = new int[5];
    delete arr;        // ❌ Wrong! Should be delete[]
    // delete[] arr;   // ✅ Correct
}
```

### 5. Wrong Delete for Single Object

```cpp
void wrongSingleDelete() {
    int* ptr = new int(42);
    delete[] ptr;      // ❌ Wrong! Should be delete
    // delete ptr;     // ✅ Correct
}
```

### 6. Using Null Pointer

```cpp
void useNullPointer() {
    int* ptr = nullptr;
    delete ptr;        // ✅ Safe - deleting nullptr is OK
    std::cout << *ptr;  // ❌ Dereferencing nullptr - CRASH!
}
```

---

## 🛡️ Safe Practices

### RAII Pattern

```cpp
#include <memory>

void raiiPattern() {
    std::cout << "=== RAII Pattern Example ===" << std::endl;
    
    // Using unique_ptr for automatic cleanup
    auto ptr = std::make_unique<int>(42);
    std::cout << "Value: " << *ptr << std::endl;
    
    // No need to delete - automatic cleanup when ptr goes out of scope
}
```

### Always Set to Nullptr After Delete

```cpp
void safeDelete() {
    int* ptr = new int(42);
    delete ptr;
    ptr = nullptr;  // ✅ Prevents accidental use
    
    if (ptr) {
        std::cout << *ptr;  // This won't execute
    }
}
```

### Check for Allocation Failure

```cpp
void safeAllocation() {
    int* ptr = new(std::nothrow) int[1000000000];
    
    if (ptr) {
        std::cout << "Allocation successful" << std::endl;
        delete[] ptr;
    } else {
        std::cout << "Allocation failed" << std::endl;
    }
}
```

### Use Smart Pointers

```cpp
void smartPointerExample() {
    std::cout << "=== Smart Pointer Example ===" << std::endl;
    
    // unique_ptr - exclusive ownership
    auto unique = std::make_unique<int>(42);
    std::cout << "Unique ptr: " << *unique << std::endl;
    
    // shared_ptr - shared ownership
    auto shared = std::make_shared<int>(99);
    std::cout << "Shared ptr: " << *shared << std::endl;
    
    // No manual delete needed!
}
```

---

## 🔍 Memory Management Debugging

### Tracking Allocations

```cpp
class MemoryTracker {
private:
    static int totalAllocations;
    static int currentAllocations;
    
public:
    static void trackAllocation() {
        totalAllocations++;
        currentAllocations++;
        std::cout << "Allocation #" << totalAllocations 
                 << " (current: " << currentAllocations << ")" << std::endl;
    }
    
    static void trackDeallocation() {
        currentAllocations--;
        std::cout << "Deallocation (current: " << currentAllocations << ")" << std::endl;
    }
    
    static int getCurrentAllocations() { return currentAllocations; }
};

int MemoryTracker::totalAllocations = 0;
int MemoryTracker::currentAllocations = 0;

// Override global new and delete
void* operator new(size_t size) {
    MemoryTracker::trackAllocation();
    return malloc(size);
}

void operator delete(void* ptr) {
    MemoryTracker::trackDeallocation();
    free(ptr);
}

void memoryTrackingExample() {
    std::cout << "=== Memory Tracking Example ===" << std::endl;
    
    int* ptr1 = new int(42);
    int* ptr2 = new int(99);
    
    delete ptr1;
    delete ptr2;
    
    std::cout << "Final allocations: " << MemoryTracker::getCurrentAllocations() << std::endl;
}
```

### Memory Leak Detection

```cpp
void leakDetection() {
    std::cout << "=== Memory Leak Detection ===" << std::endl;
    
    // Simulate a memory leak
    int* leakyPtr = new int(42);
    std::cout << "Created pointer but didn't delete it" << std::endl;
    
    // In real programs, use tools like Valgrind, AddressSanitizer
    // or built-in memory tracking to detect leaks
}
```

---

## 📊 Performance Considerations

### Allocation Speed

```cpp
#include <chrono>

void allocationSpeedTest() {
    const int ITERATIONS = 100000;
    
    // Stack allocation
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        int x = i;  // Stack allocation
    }
    auto stackTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    // Heap allocation
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        int* x = new int(i);  // Heap allocation
        delete x;
    }
    auto heapTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    std::cout << "Stack allocation: " << stackTime << " microseconds" << std::endl;
    std::cout << "Heap allocation: " << heapTime << " microseconds" << std::endl;
    std::cout << "Heap is " << (double)heapTime / stackTime << "x slower" << std::endl;
}
```

### Memory Fragmentation

```cpp
void fragmentationExample() {
    std::cout << "=== Memory Fragmentation Example ===" << std::endl;
    
    // Allocate and deallocate in a pattern that causes fragmentation
    int* ptrs[10];
    
    // Allocate
    for (int i = 0; i < 10; i++) {
        ptrs[i] = new int[100];
    }
    
    // Deallocate in a fragmented pattern
    delete[] ptrs[0];
    delete[] ptrs[2];
    delete[] ptrs[4];
    delete[] ptrs[6];
    delete[] ptrs[8];
    
    // Remaining allocations are fragmented
    std::cout << "Memory is now fragmented" << std::endl;
    
    // Clean up remaining
    for (int i = 1; i < 10; i += 2) {
        delete[] ptrs[i];
    }
}
```

---

## 🎯 Key Takeaways

1. **`new` allocates** memory on the heap
2. **`delete` frees** memory on the heap
3. **Always match** `new` with `delete`, `new[]` with `delete[]`
4. **Memory leaks** happen when you forget to `delete`
5. **Double delete** crashes your program
6. **Dangling pointers** access freed memory
7. **Use smart pointers** for automatic memory management
8. **Set to nullptr** after deleting for safety

---

## 🔄 Complete Memory Management Guide

| Operation | Syntax | Purpose | Example |
|-----------|---------|---------|---------|
| Allocate single | `new Type` | Create single object | `new int(42)` |
| Allocate array | `new Type[size]` | Create array | `new int[10]` |
| Free single | `delete ptr` | Free single object | `delete ptr` |
| Free array | `delete[] ptr` | Free array | `delete[] arr` |

---

## 🔄 Next Steps

Now that you understand how to allocate and deallocate memory, let's explore common memory management problems:

*Continue reading: [Memory Leaks and Dangling Pointers](MemoryLeaksDanglingPointers.md)*
