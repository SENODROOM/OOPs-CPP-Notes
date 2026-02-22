# 🚨 Memory Leaks and Dangling Pointers
### "The two biggest dangers of dynamic memory management"

---

## 🎯 Core Concept

**Memory leaks** and **dangling pointers** are the two most common and dangerous problems in dynamic memory management. Understanding them is crucial for writing safe C++ code.

### The Leaky Faucet Analogy

```
Memory Leak = Leaky faucet - water keeps flowing but never stops
Dangling Pointer = Broken pipe - water flows but goes nowhere

┌─────────────────┐                ┌─────────────────┐
│  Water Tank    │                │  Water Tank    │
│  (Heap Memory) │                │  (Heap Memory) │
│ ┌─────────────┐ │                │ ┌─────────────┐ │
│ │  Water     │ │                │ │  Water     │ │
│ │  Level:    │ │                │ │  Level:    │ │
│ │  80%       │ │                │ │  80%       │ │
│ └─────────────┘ │                │ └─────────────┘ │
└─────────────────┘                └─────────────────┘
      ▲                                   ▲
      │                                   │
      └───────────────────────────────────┘
           Leaky Faucet                    Broken Pipe
    (Memory keeps allocating)          (Pointer points to freed memory)
```

---

## 🚰 Memory Leaks

### What is a Memory Leak?

A **memory leak** occurs when you allocate memory on the heap but never deallocate it. The memory remains allocated even though your program can no longer access it.

### How Memory Leaks Happen

```cpp
void memoryLeakExample() {
    int* ptr = new int(42);  // Allocate memory
    // ❌ Forgot to delete ptr!
    // Memory is leaked when function ends
}
```

### Types of Memory Leaks

#### 1. Simple Leak

```cpp
void simpleLeak() {
    int* ptr = new int(42);
    // Function ends, ptr is destroyed, but memory remains allocated
}
```

#### 2. Conditional Leak

```cpp
void conditionalLeak(bool condition) {
    int* ptr = new int(42);
    
    if (condition) {
        delete ptr;  // Only delete if condition is true
        return;
    }
    
    // ❌ If condition is false, memory is leaked
}
```

#### 3. Exception Leak

```cpp
void exceptionLeak() {
    int* ptr = new int(42);
    
    riskyOperation();  // Might throw exception
    delete ptr;        // ❌ Never reached if exception thrown
}
```

#### 4. Object Leak

```cpp
class LeakyClass {
private:
    int* data;
    
public:
    LeakyClass() {
        data = new int[100];  // Allocate in constructor
    }
    
    // ❌ No destructor to delete data!
};

void objectLeak() {
    LeakyClass obj;  // data is allocated but never deleted
}
```

### Detecting Memory Leaks

#### Manual Detection

```cpp
class MemoryLeakDetector {
private:
    static int allocationCount;
    
public:
    static void allocate() {
        allocationCount++;
        std::cout << "Allocation #" << allocationCount << std::endl;
    }
    
    static void deallocate() {
        allocationCount--;
        std::cout << "Deallocation (remaining: " << allocationCount << ")" << std::endl;
    }
    
    static int getRemainingAllocations() {
        return allocationCount;
    }
};

int MemoryLeakDetector::allocationCount = 0;

// Override global new/delete
void* operator new(size_t size) {
    MemoryLeakDetector::allocate();
    return malloc(size);
}

void operator delete(void* ptr) {
    MemoryLeakDetector::deallocate();
    free(ptr);
}

void leakDetection() {
    std::cout << "=== Memory Leak Detection ===" << std::endl;
    
    int* ptr1 = new int(42);
    int* ptr2 = new int(99);
    
    delete ptr1;  // Only delete one
    
    std::cout << "Remaining allocations: " 
              << MemoryLeakDetector::getRemainingAllocations() << std::endl;
    // ❌ One allocation is leaked!
}
```

#### Tool-Based Detection

```cpp
// Use tools like:
// - Valgrind (Linux)
// - AddressSanitizer (Clang/GCC)
// - Visual Studio Memory Diagnostics
// - Dr. Memory (Windows)

void demonstrateToolDetection() {
    std::cout << "=== Tool-Based Detection ===" << std::endl;
    std::cout << "Run with: valgrind --leak-check=full ./program" << std::endl;
    std::cout << "Or compile with: -fsanitize=address" << std::endl;
    
    int* leaky = new int(42);
    // Tools will detect this leak
}
```

---

## 🔗 Dangling Pointers

### What is a Dangling Pointer?

A **dangling pointer** is a pointer that points to memory that has been deallocated. Accessing a dangling pointer leads to undefined behavior.

### How Dangling Pointers Happen

```cpp
void danglingPointerExample() {
    int* ptr = new int(42);
    delete ptr;        // Memory is freed
    std::cout << *ptr;  // ❌ ptr is dangling - undefined behavior!
}
```

### Types of Dangling Pointers

#### 1. Stack Dangling Pointer

```cpp
int* createStackDangling() {
    int local = 42;
    return &local;  // ❌ local destroyed when function ends
}

void stackDangling() {
    int* ptr = createStackDangling();
    std::cout << *ptr;  // ❌ Undefined behavior!
}
```

#### 2. Heap Dangling Pointer

```cpp
void heapDangling() {
    int* ptr = new int(42);
    delete ptr;
    std::cout << *ptr;  // ❌ Undefined behavior!
}
```

#### 3. Multiple Dangling Pointers

```cpp
void multipleDangling() {
    int* ptr1 = new int(42);
    int* ptr2 = ptr1;  // Both point to same memory
    
    delete ptr1;  // Memory freed
    std::cout << *ptr1;  // ❌ Dangling
    std::cout << *ptr2;  // ❌ Also dangling
}
```

#### 4. Object Dangling Pointer

```cpp
class DanglingExample {
public:
    int value;
    
    DanglingExample(int v) : value(v) {}
    
    int* getValue() { return &value; }
};

void objectDangling() {
    int* ptr;
    
    {
        DanglingExample obj(42);
        ptr = obj.getValue();  // Points to obj's member
    }  // obj is destroyed here
    
    std::cout << *ptr;  // ❌ ptr is dangling!
}
```

### Detecting Dangling Pointers

#### Manual Detection

```cpp
class DanglingPointerDetector {
private:
    void* allocated;
    bool isValid;
    
public:
    DanglingPointerDetector(void* ptr) : allocated(ptr), isValid(true) {}
    
    void invalidate() { isValid = false; }
    
    bool checkValid() const { return isValid; }
    
    void* get() const { 
        if (!isValid) {
            std::cout << "ERROR: Dangling pointer detected!" << std::endl;
        }
        return allocated;
    }
};

void danglingDetection() {
    std::cout << "=== Dangling Pointer Detection ===" << std::endl;
    
    int* ptr = new int(42);
    DanglingPointerDetector detector(ptr);
    
    std::cout << "Value: " << *(int*)detector.get() << std::endl;
    
    delete ptr;
    detector.invalidate();
    
    detector.get();  // Will detect dangling pointer
}
```

#### Tool-Based Detection

```cpp
// Use tools like:
// - AddressSanitizer (detects use-after-free)
// - Valgrind (detects invalid reads/writes)
// - Clang Static Analyzer

void demonstrateToolDanglingDetection() {
    std::cout << "=== Tool-Based Dangling Detection ===" << std::endl;
    std::cout << "Run with: -fsanitize=address" << std::endl;
    
    int* ptr = new int(42);
    delete ptr;
    *ptr = 99;  // Tools will detect this
}
```

---

## 🛡️ Prevention Strategies

### RAII Pattern

```cpp
#include <memory>

void raiiPrevention() {
    std::cout << "=== RAII Prevention ===" << std::endl;
    
    // unique_ptr automatically deletes when out of scope
    auto safePtr = std::make_unique<int>(42);
    std::cout << "Value: " << *safePtr << std::endl;
    
    // No manual delete needed - no leaks possible!
}
```

### Exception Safety

```cpp
void exceptionSafeAllocation() {
    std::cout << "=== Exception-Safe Allocation ===" << std::endl;
    
    try {
        auto ptr = std::make_unique<int>(42);
        riskyOperation();  // Might throw exception
        // ptr automatically deleted even if exception thrown
    } catch (const std::exception& e) {
        std::cout << "Exception caught: " << e.what() << std::endl;
    }
}
```

### Smart Pointers for Shared Ownership

```cpp
void sharedOwnershipPrevention() {
    std::cout << "=== Shared Ownership Prevention ===" << std::endl;
    
    std::shared_ptr<int> ptr1 = std::make_shared<int>(42);
    std::shared_ptr<int> ptr2 = ptr1;  // Shared ownership
    
    std::cout << "Use count: " << ptr1.use_count() << std::endl;
    
    // Memory automatically deleted when last shared_ptr is destroyed
}
```

### Custom RAII Wrappers

```cpp
template<typename T>
class SafePointer {
private:
    T* ptr;
    
public:
    SafePointer(T* p = nullptr) : ptr(p) {}
    
    ~SafePointer() {
        delete ptr;
    }
    
    // Prevent copying
    SafePointer(const SafePointer&) = delete;
    SafePointer& operator=(const SafePointer&) = delete;
    
    // Allow moving
    SafePointer(SafePointer&& other) noexcept : ptr(other.ptr) {
        other.ptr = nullptr;
    }
    
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }
    
    T* get() const { return ptr; }
};

void customWrapperPrevention() {
    std::cout << "=== Custom Wrapper Prevention ===" << std::endl;
    
    SafePointer<int> safePtr(new int(42));
    std::cout << "Value: " << *safePtr << std::endl;
    
    // Automatically deleted when safePtr goes out of scope
}
```

---

## 🔧 Debugging Techniques

### Memory Logging

```cpp
class MemoryLogger {
private:
    static std::unordered_map<void*, std::string> allocations;
    
public:
    static void logAllocation(void* ptr, const std::string& context) {
        allocations[ptr] = context;
        std::cout << "Allocated " << ptr << " (" << context << ")" << std::endl;
    }
    
    static void logDeallocation(void* ptr) {
        auto it = allocations.find(ptr);
        if (it != allocations.end()) {
            std::cout << "Deallocated " << ptr << " (" << it->second << ")" << std::endl;
            allocations.erase(it);
        } else {
            std::cout << "ERROR: Deallocating unknown pointer " << ptr << std::endl;
        }
    }
    
    static void checkLeaks() {
        if (!allocations.empty()) {
            std::cout << "Memory leaks detected:" << std::endl;
            for (const auto& [ptr, context] : allocations) {
                std::cout << "  " << ptr << " (" << context << ")" << std::endl;
            }
        } else {
            std::cout << "No memory leaks detected" << std::endl;
        }
    }
};

std::unordered_map<void*, std::string> MemoryLogger::allocations;

void memoryLoggingExample() {
    std::cout << "=== Memory Logging Example ===" << std::endl;
    
    int* ptr1 = new int(42);
    MemoryLogger::logAllocation(ptr1, "ptr1");
    
    int* ptr2 = new int(99);
    MemoryLogger::logAllocation(ptr2, "ptr2");
    
    delete ptr1;
    MemoryLogger::logDeallocation(ptr1);
    
    MemoryLogger::checkLeaks();  // Will show ptr2 as leak
}
```

### Guard Objects

```cpp
template<typename T>
class MemoryGuard {
private:
    T* ptr;
    
public:
    MemoryGuard(T* p) : ptr(p) {}
    
    ~MemoryGuard() {
        if (ptr) {
            delete ptr;
        }
    }
    
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }
    
    T* release() {
        T* temp = ptr;
        ptr = nullptr;
        return temp;
    }
    
    void reset(T* p = nullptr) {
        if (ptr) {
            delete ptr;
        }
        ptr = p;
    }
};

void guardExample() {
    std::cout << "=== Memory Guard Example ===" << std::endl;
    
    {
        MemoryGuard<int> guard(new int(42));
        std::cout << "Value: " << *guard << std::endl;
        // Memory automatically deleted when guard goes out of scope
    }
    
    std::cout << "Guard out of scope - memory freed" << std::endl;
}
```

---

## 📊 Real-World Examples

### File Handle Management

```cpp
class FileHandler {
private:
    FILE* file;
    
public:
    FileHandler(const char* filename) {
        file = fopen(filename, "r");
        if (!file) {
            throw std::runtime_error("Failed to open file");
        }
    }
    
    ~FileHandler() {
        if (file) {
            fclose(file);
        }
    }
    
    void readLine(char* buffer, int size) {
        if (file && fgets(buffer, size, file)) {
            // Line read successfully
        }
    }
};

void fileHandlingExample() {
    std::cout << "=== File Handling Example ===" << std::endl;
    
    try {
        FileHandler file("example.txt");
        char buffer[256];
        file.readLine(buffer, 256);
        std::cout << "Read: " << buffer << std::endl;
        // File automatically closed when file goes out of scope
    } catch (const std::exception& e) {
        std::cout << "Error: " << e.what() << std::endl;
    }
}
```

### Database Connection Pool

```cpp
class DatabaseConnection {
private:
    std::string connectionString;
    bool connected;
    
public:
    DatabaseConnection(const std::string& connStr) 
        : connectionString(connStr), connected(false) {
        // Simulate connection
        connected = true;
        std::cout << "Connected to database" << std::endl;
    }
    
    ~DatabaseConnection() {
        if (connected) {
            std::cout << "Disconnected from database" << std::endl;
        }
    }
    
    void query(const std::string& sql) {
        if (connected) {
            std::cout << "Query: " << sql << std::endl;
        }
    }
};

class ConnectionPool {
private:
    std::vector<std::unique_ptr<DatabaseConnection>> connections;
    
public:
    ConnectionPool(int poolSize) {
        for (int i = 0; i < poolSize; i++) {
            connections.push_back(
                std::make_unique<DatabaseConnection>("connection_string")
            );
        }
    }
    
    DatabaseConnection* getConnection() {
        // Simple implementation - in real code, use proper pooling
        if (!connections.empty()) {
            return connections.back().get();
        }
        return nullptr;
    }
};

void connectionPoolExample() {
    std::cout << "=== Connection Pool Example ===" << std::endl;
    
    ConnectionPool pool(3);
    
    DatabaseConnection* conn = pool.getConnection();
    if (conn) {
        conn->query("SELECT * FROM users");
    }
    
    // Connections automatically closed when pool is destroyed
}
```

---

## 🎯 Key Takeaways

1. **Memory leaks** = allocated but never freed memory
2. **Dangling pointers** = pointers to freed memory
3. **Both cause undefined behavior** and program instability
4. **RAII pattern** prevents both automatically
5. **Smart pointers** are the modern solution
6. **Always delete** what you allocate
7. **Set pointers to nullptr** after deleting
8. **Use tools** to detect memory problems

---

## 🔄 Prevention Checklist

| Problem | Prevention Strategy | Example |
|---------|-------------------|---------|
| Memory Leak | RAII/Smart Pointers | `auto ptr = make_unique<int>(42)` |
| Dangling Pointer | Null pointer after delete | `delete ptr; ptr = nullptr;` |
| Exception Leak | RAII/Smart Pointers | `try { auto ptr = make_unique<int>(); }` |
| Stack Dangling | Don't return stack addresses | Use heap allocation instead |
| Multiple Dangling | Weak pointers for observation | `weak_ptr` for non-owning references |

---

## 🔄 Next Steps

Now that you understand memory safety issues, let's explore how to work with dynamic arrays:

*Continue reading: [Dynamic Arrays](DynamicArrays.md)*
