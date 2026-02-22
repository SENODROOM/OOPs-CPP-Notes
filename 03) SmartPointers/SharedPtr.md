# 🤝 shared_ptr - Shared Ownership
### "Many hands make light work - multiple owners, one resource"

---

## 🎯 Core Concept

`shared_ptr` is a smart pointer that allows **multiple owners** to share the same resource. It uses **reference counting** to track how many `shared_ptr` objects are pointing to the same memory.

### Key Characteristics

- ✅ **Multiple owners** - many pointers can own the same resource
- ✅ **Reference counting** - tracks number of owners
- ✅ **Copyable** - can be copied freely
- ✅ **Thread-safe** - reference counting is atomic
- ⚠️ **Overhead** - small performance and memory cost
- ✅ **Automatic cleanup** - deleted when last owner is gone

---

## 🏗️ Internal Implementation

### Reference Counting Mechanism

```cpp
template<typename T>
class shared_ptr {
private:
    T* ptr;           // Pointer to the managed object
    ControlBlock* control;  // Reference counting metadata
    
    struct ControlBlock {
        long shared_count;    // Number of shared_ptr owners
        long weak_count;      // Number of weak_ptr observers
        // ... other metadata
        
        void increment_shared() noexcept {
            shared_count.fetch_add(1, std::memory_order_relaxed);
        }
        
        long decrement_shared() noexcept {
            auto result = shared_count.fetch_sub(1, std::memory_order_acq_rel);
            if (result == 1) {  // Was 1, now 0
                delete ptr;  // Delete the managed object
                if (weak_count == 0) {
                    delete this;  // Delete control block
                }
            }
            return result - 1;
        }
    };
    
public:
    // Constructor
    shared_ptr(T* p) : ptr(p), control(new ControlBlock{1, 0}) {
        std::cout << "shared_ptr created with count=1" << std::endl;
    }
    
    // Copy constructor
    shared_ptr(const shared_ptr& other) noexcept 
        : ptr(other.ptr), control(other.control) {
        if (control) {
            control->increment_shared();
            std::cout << "shared_ptr copied, count=" << control->shared_count << std::endl;
        }
    }
    
    // Destructor
    ~shared_ptr() {
        if (control) {
            auto count = control->decrement_shared();
            std::cout << "shared_ptr destroyed, count=" << count << std::endl;
        }
    }
    
    // Assignment operator
    shared_ptr& operator=(const shared_ptr& other) noexcept {
        if (this != &other) {
            // Decrement old reference
            if (control) {
                control->decrement_shared();
            }
            
            // Copy new reference
            ptr = other.ptr;
            control = other.control;
            if (control) {
                control->increment_shared();
            }
        }
        return *this;
    }
    
    // Pointer operations
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }
    T* get() const noexcept { return ptr; }
    
    long use_count() const noexcept {
        return control ? control->shared_count : 0;
    }
};
```

### Memory Layout

```
Heap Memory:
┌─────────────────────────────────┐
│ Control Block                    │
│ ┌─────────────────────────────┐ │
│ │ shared_count: 3              │ │ ← Atomic counter
│ │ weak_count: 1               │ │
│ │ custom_deleter (optional)    │ │
│ │ allocator (optional)         │ │
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
            ▲
            │
┌─────────────────────────────────┐
│ Managed Object                  │
│ ┌─────────────────────────────┐ │
│ │ T object (actual data)      │ │ ← Your data
│ └─────────────────────────────┘ │
└─────────────────────────────────┘

Stack Memory (for each shared_ptr):
┌─────────────────────────────────┐
│ shared_ptr object              │
│ ┌─────────────────────────────┐ │
│ │ T* ptr (8 bytes)           │ │ ← Points to managed object
│ │ ControlBlock* (8 bytes)    │ │ ← Points to control block
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
```

---

## 📝 Creating and Using shared_ptr

### Basic Creation

```cpp
#include <memory>
#include <iostream>

int main() {
    // Method 1: make_shared (preferred)
    auto ptr1 = make_shared<int>(42);
    std::cout << "Count: " << ptr1.use_count() << std::endl;  // 1
    
    // Method 2: Direct constructor
    shared_ptr<int> ptr2(new int(99));
    std::cout << "Count: " << ptr2.use_count() << std::endl;  // 1
    
    // Use like a regular pointer
    std::cout << *ptr1 << std::endl;  // 42
    std::cout << *ptr2 << std::endl;  // 99
}
```

### Copying and Reference Counting

```cpp
void demonstrateSharing() {
    auto original = make_shared<std::string>("Hello");
    std::cout << "Created: count=" << original.use_count() << std::endl;  // 1
    
    {
        auto copy1 = original;  // Copy constructor
        std::cout << "Copy 1: count=" << original.use_count() << std::endl;  // 2
        
        auto copy2 = original;  // Another copy
        std::cout << "Copy 2: count=" << original.use_count() << std::endl;  // 3
        
        copy2.reset();  // Reset one copy
        std::cout << "After reset: count=" << original.use_count() << std::endl;  // 2
        
    }  // copy1 goes out of scope here
    
    std::cout << "After scope: count=" << original.use_count() << std::endl;  // 1
}  // original goes out of scope, count=0, object deleted

std::cout << "Function ended" << std::endl;
```

**Output:**
```
Created: count=1
Copy 1: count=2
Copy 2: count=3
After reset: count=2
After scope: count=1
Function ended
```

---

## 🏢 Real-World Examples

### Shared Resource Management

```cpp
class DatabaseConnection {
public:
    DatabaseConnection(const std::string& db) : db_name_(db) {
        std::cout << "Connected to " << db_name_ << std::endl;
    }
    
    ~DatabaseConnection() {
        std::cout << "Disconnected from " << db_name_ << std::endl;
    }
    
    void query(const std::string& sql) {
        std::cout << "Query on " << db_name_ << ": " << sql << std::endl;
    }
    
private:
    std::string db_name_;
};

class UserService {
private:
    shared_ptr<DatabaseConnection> db_;
    
public:
    UserService(shared_ptr<DatabaseConnection> db) : db_(db) {}
    
    void addUser(const std::string& name) {
        db_->query("INSERT INTO users VALUES ('" + name + "')");
    }
};

class OrderService {
private:
    shared_ptr<DatabaseConnection> db_;
    
public:
    OrderService(shared_ptr<DatabaseConnection> db) : db_(db) {}
    
    void createOrder(int userId) {
        db_->query("INSERT INTO orders (user_id) VALUES (" + std::to_string(userId) + ")");
    }
};

int main() {
    // Create shared database connection
    auto db = make_shared<DatabaseConnection>("production_db");
    std::cout << "DB count: " << db.use_count() << std::endl;  // 1
    
    // Share between services
    UserService userService(db);
    OrderService orderService(db);
    std::cout << "DB count: " << db.use_count() << std::endl;  // 3
    
    userService.addUser("Alice");
    orderService.createOrder(1);
    
}  // All services destroyed, connection automatically closed

std::cout << "Program ended" << std::endl;
```

**Output:**
```
Connected to production_db
DB count: 1
DB count: 3
Query on production_db: INSERT INTO users VALUES ('Alice')
Query on production_db: INSERT INTO orders (user_id) VALUES (1)
Disconnected from production_db
Program ended
```

### Caching System

```cpp
template<typename T>
class Cache {
private:
    std::unordered_map<std::string, shared_ptr<T>> cache_;
    
public:
    shared_ptr<T> get(const std::string& key) {
        auto it = cache_.find(key);
        if (it != cache_.end()) {
            std::cout << "Cache hit for " << key << std::endl;
            return it->second;  // Return existing shared_ptr
        }
        
        std::cout << "Cache miss for " << key << ", creating new" << std::endl;
        auto item = make_shared<T>();
        cache_[key] = item;
        return item;
    }
    
    void clear() {
        cache_.clear();
    }
    
    size_t size() const {
        return cache_.size();
    }
};

class ExpensiveObject {
public:
    ExpensiveObject() {
        std::cout << "ExpensiveObject created (slow operation)" << std::endl;
    }
    
    ~ExpensiveObject() {
        std::cout << "ExpensiveObject destroyed" << std::endl;
    }
    
    void doWork() {
        std::cout << "Doing expensive work" << std::endl;
    }
};

int main() {
    Cache<ExpensiveObject> cache;
    
    // First request - creates object
    auto obj1 = cache.get("key1");
    obj1->doWork();
    
    // Second request - returns same object
    auto obj2 = cache.get("key1");
    obj2->doWork();
    
    std::cout << "Cache size: " << cache.size() << std::endl;
    
    // Different key - creates new object
    auto obj3 = cache.get("key2");
    obj3->doWork();
    
    std::cout << "Cache size: " << cache.size() << std::endl;
    
}  // All objects destroyed when cache is cleared

std::cout << "Program ended" << std::endl;
```

---

## 🔄 Advanced Operations

### Custom Deleters

```cpp
// Custom deleter for arrays
auto arrayDeleter = [](int* p) {
    std::cout << "Custom array deleter called" << std::endl;
    delete[] p;
};

shared_ptr<int> arr(new int[5], arrayDeleter);

// Custom deleter for FILE*
auto fileDeleter = [](FILE* f) {
    if (f) {
        std::cout << "Closing file" << std::endl;
        fclose(f);
    }
};

shared_ptr<FILE> file(fopen("data.txt", "r"), fileDeleter);
```

### Aliasing Constructor

```cpp
struct Node {
    int value;
    shared_ptr<Node> next;
    
    Node(int v) : value(v) {}
};

// Create linked list
auto node1 = make_shared<Node>(1);
auto node2 = make_shared<Node>(2);
auto node3 = make_shared<Node>(3);

node1->next = node2;
node2->next = node3;

// shared_ptr that owns node1 but points to node3's value
shared_ptr<int> valuePtr(node1, &node3->value);

std::cout << "Value: " << *valuePtr << std::endl;  // 3
std::cout << "Use count: " << node1.use_count() << std::endl;  // 2 (node1 + valuePtr)
```

### Atomic Operations

```cpp
shared_ptr<MyClass> globalPtr;

void threadSafeUpdate() {
    // Atomic replacement
    auto newPtr = make_shared<MyClass>();
    globalPtr = newPtr;  // Thread-safe assignment
    
    // Atomic access
    if (auto local = globalPtr) {  // Thread-safe copy
        local->doSomething();
    }
}
```

---

## ⚡ Performance Analysis

### Memory Overhead

```cpp
// Raw pointer
int* raw = new int(42);
// Size: 8 bytes (pointer) + 4 bytes (int) = 12 bytes

// unique_ptr
unique_ptr<int> unique = make_unique<int>(42);
// Size: 8 bytes (pointer) + 4 bytes (int) = 12 bytes

// shared_ptr
shared_ptr<int> shared = make_shared<int>(42);
// Size: 16 bytes (2 pointers) + 8 bytes (control block) + 4 bytes (int) = 28 bytes
```

**Overhead:** ~16 bytes per shared_ptr + 8 bytes control block

### Speed Comparison

```cpp
void benchmark() {
    const int ITERATIONS = 1000000;
    
    // Raw pointer
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        int* ptr = new int(i);
        *ptr = *ptr * 2;
        delete ptr;
    }
    auto end = std::chrono::high_resolution_clock::now();
    
    // shared_ptr
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        auto ptr = make_shared<int>(i);
        *ptr = *ptr * 2;
    }
    end = std::chrono::high_resolution_clock::now();
    
    // shared_ptr copying
    auto base = make_shared<int>(42);
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        auto copy = base;  // Atomic increment
    }
    end = std::chrono::high_resolution_clock::now();
}
```

**Typical Results:**
- Raw pointer: ~50ms
- shared_ptr (creation): ~80ms (60% slower)
- shared_ptr (copying): ~120ms (140% slower)

---

## 🔄 Circular Reference Problem

### The Problem

```cpp
class Node {
public:
    std::string name;
    shared_ptr<Node> next;
    
    Node(const std::string& n) : name(n) {
        std::cout << "Node " << name << " created" << std::endl;
    }
    
    ~Node() {
        std::cout << "Node " << name << " destroyed" << std::endl;
    }
};

void createCircularReference() {
    auto node1 = make_shared<Node>("A");
    auto node2 = make_shared<Node>("B");
    
    node1->next = node2;  // node1 owns node2
    node2->next = node1;  // node2 owns node1 ← CIRCULAR REFERENCE!
    
    std::cout << "node1 count: " << node1.use_count() << std::endl;  // 2
    std::cout << "node2 count: " << node2.use_count() << std::endl;  // 2
    
}  // Neither node is destroyed - memory leak!

std::cout << "Function ended" << std::endl;
```

**Output:**
```
Node A created
Node B created
node1 count: 2
node2 count: 2
Function ended
// No destructors called - memory leak!
```

### The Solution: weak_ptr

```cpp
class Node {
public:
    std::string name;
    shared_ptr<Node> next;
    weak_ptr<Node> prev;  // Use weak_ptr for back reference
    
    Node(const std::string& n) : name(n) {
        std::cout << "Node " << name << " created" << std::endl;
    }
    
    ~Node() {
        std::cout << "Node " << name << " destroyed" << std::endl;
    }
};

void breakCircularReference() {
    auto node1 = make_shared<Node>("A");
    auto node2 = make_shared<Node>("B");
    
    node1->next = node2;  // Strong reference
    node2->prev = node1;  // Weak reference - doesn't increase count
    
    std::cout << "node1 count: " << node1.use_count() << std::endl;  // 1
    std::cout << "node2 count: " << node2.use_count() << std::endl;  // 1
    
}  // Both nodes destroyed properly!

std::cout << "Function ended" << std::endl;
```

**Output:**
```
Node A created
Node B created
node1 count: 1
node2 count: 1
Node B destroyed
Node A destroyed
Function ended
```

---

## 🎯 Best Practices

### DO ✅

```cpp
// Use make_shared when possible
auto ptr = make_shared<MyClass>(arg1, arg2);

// Use for shared resources
shared_ptr<Database> db = make_shared<Database>();

// Use weak_ptr to break cycles
weak_ptr<Node> prev = current;

// Check weak_ptr before use
if (auto strong = weak.lock()) {
    strong->doSomething();
}

// Use enable_shared_from_this for member functions
class MyClass : public enable_shared_from_this<MyClass> {
    shared_ptr<MyClass> getShared() {
        return shared_from_this();
    }
};
```

### DON'T ❌

```cpp
// Don't use new directly (less efficient)
shared_ptr<MyClass> ptr(new MyClass());

// Don't create circular references with shared_ptr
// Use weak_ptr instead

// Don't use weak_ptr without checking
weak_ptr<int> weak = shared;
*weak.lock();  // ❌ Might be nullptr!

// Don't assume thread safety for the pointed-to object
// Only reference counting is atomic
```

---

## 🎓 Key Takeaways

1. **shared_ptr = shared ownership** - multiple owners allowed
2. **Reference counting** - tracks number of owners automatically
3. **Copyable** - can be copied freely, count increases
4. **Thread-safe reference counting** - but not the object itself
5. **Memory overhead** - extra control block for counting
6. **Performance cost** - slower than unique_ptr but still fast
7. **Circular references** - use weak_ptr to break cycles
8. **Use when sharing is needed** - otherwise prefer unique_ptr

---

## 🔄 Next Steps

Now that you understand shared ownership, let's explore how to observe shared resources without affecting their lifetime:

*Continue reading: [weak_ptr - Non-Owning Observer](WeakPtr.md)*
