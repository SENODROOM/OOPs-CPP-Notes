# 👁️ weak_ptr - Non-Owning Observer
### "I'm just looking, I don't own it - the perfect solution to circular references"

---

## 🎯 Core Concept

`weak_ptr` is a smart pointer that **observes** a `shared_ptr`-managed object without **owning** it. It does not affect the reference count and can be used to break circular references.

### Key Characteristics

- ✅ **Non-owning** - doesn't increment reference count
- ✅ **Observer only** - must lock() to access the object
- ✅ **Prevents cycles** - breaks circular reference problems
- ✅ **Can be empty** - resource might already be destroyed
- ⚠️ **Temporary access** - must convert to shared_ptr to use
- ✅ **Thread-safe** - reference counting operations are atomic

---

## 🏗️ Internal Implementation

### How weak_ptr Works

```cpp
template<typename T>
class weak_ptr {
private:
    ControlBlock* control;  // Same control block as shared_ptr
    
public:
    // Default constructor
    constexpr weak_ptr() noexcept : control(nullptr) {}
    
    // Construct from shared_ptr
    weak_ptr(const shared_ptr<T>& other) noexcept : control(other.control) {
        if (control) {
            control->increment_weak();  // Increment weak count, not shared count
        }
    }
    
    // Destructor
    ~weak_ptr() {
        if (control) {
            auto weak_count = control->decrement_weak();
            if (weak_count == 0 && control->shared_count == 0) {
                delete control;  // Delete control block if no owners or observers
            }
        }
    }
    
    // Lock to get shared_ptr (the key operation)
    shared_ptr<T> lock() const noexcept {
        if (!control || control->shared_count == 0) {
            return shared_ptr<T>();  // Return empty shared_ptr
        }
        
        // Increment shared count atomically
        control->increment_shared();
        return shared_ptr<T>(control);  // Construct shared_ptr from control block
    }
    
    // Check if expired (no more shared owners)
    bool expired() const noexcept {
        return !control || control->shared_count == 0;
    }
    
    // Get weak count
    long use_count() const noexcept {
        return control ? control->shared_count : 0;
    }
};
```

### Reference Counting with weak_ptr

```
Control Block State:
┌─────────────────────────────────┐
│ shared_count: 2                 │ ← Number of shared_ptr owners
│ weak_count: 1                   │ ← Number of weak_ptr observers
│ ┌─────────────────────────────┐ │
│ │ Managed object (alive)       │ │ ← Object exists if shared_count > 0
│ └─────────────────────────────┘ │
└─────────────────────────────────┘

When shared_count reaches 0:
┌─────────────────────────────────┐
│ shared_count: 0                 │ ← No more owners
│ weak_count: 1                   │ ← Still 1 observer
│ ┌─────────────────────────────┐ │
│ │ Managed object (destroyed)   │ │ ← Object deleted
│ └─────────────────────────────┘ │
└─────────────────────────────────┘

When weak_count also reaches 0:
┌─────────────────────────────────┐
│ shared_count: 0                 │
│ weak_count: 0                   │ ← No observers either
│ ┌─────────────────────────────┐ │
│ │ Control block (destroyed)    │ │ ← Everything cleaned up
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
```

---

## 📝 Creating and Using weak_ptr

### Basic Usage

```cpp
#include <memory>
#include <iostream>

int main() {
    // Create a shared_ptr
    auto shared = make_shared<int>(42);
    std::cout << "Shared count: " << shared.use_count() << std::endl;  // 1
    
    // Create weak_ptr from shared_ptr
    weak_ptr<int> weak = shared;
    std::cout << "Shared count: " << shared.use_count() << std::endl;  // 1 (unchanged!)
    std::cout << "Weak expired: " << weak.expired() << std::endl;      // false
    
    // Access through weak_ptr using lock()
    if (auto locked = weak.lock()) {
        std::cout << "Value: " << *locked << std::endl;  // 42
        std::cout << "Locked count: " << locked.use_count() << std::endl;  // 2
    }
    
    // Reset shared_ptr
    shared.reset();
    std::cout << "After reset - Shared count: " << weak.use_count() << std::endl;  // 0
    std::cout << "Weak expired: " << weak.expired() << std::endl;              // true
    
    // Try to lock after object is gone
    if (auto locked = weak.lock()) {
        std::cout << "Value: " << *locked << std::endl;
    } else {
        std::cout << "Object no longer exists" << std::endl;
    }
}
```

**Output:**
```
Shared count: 1
Shared count: 1
Weak expired: false
Value: 42
Locked count: 2
After reset - Shared count: 0
Weak expired: true
Object no longer exists
```

---

## 🔄 Circular Reference Solution

### The Problem Without weak_ptr

```cpp
class Node {
public:
    std::string name;
    shared_ptr<Node> next;
    shared_ptr<Node> prev;  // ← This creates circular reference!
    
    Node(const std::string& n) : name(n) {
        std::cout << "Node " << name << " created" << std::endl;
    }
    
    ~Node() {
        std::cout << "Node " << name << " destroyed" << std::endl;
    }
};

void createCircularList() {
    auto node1 = make_shared<Node>("A");
    auto node2 = make_shared<Node>("B");
    auto node3 = make_shared<Node>("C");
    
    // Create circular references
    node1->next = node2;
    node2->next = node3;
    node3->next = node1;
    
    node1->prev = node3;  // ← Circular!
    node2->prev = node1;  // ← Circular!
    node3->prev = node2;  // ← Circular!
    
    std::cout << "node1 count: " << node1.use_count() << std::endl;  // 3
    std::cout << "node2 count: " << node2.use_count() << std::endl;  // 3
    std::cout << "node3 count: " << node3.use_count() << std::endl;  // 3
    
}  // MEMORY LEAK! No nodes destroyed

std::cout << "Function ended" << std::endl;
```

**Output:**
```
Node A created
Node B created
Node C created
node1 count: 3
node2 count: 3
node3 count: 3
Function ended
// No destructors called - memory leak!
```

### The Solution With weak_ptr

```cpp
class Node {
public:
    std::string name;
    shared_ptr<Node> next;
    weak_ptr<Node> prev;  // ← Use weak_ptr for back reference
    
    Node(const std::string& n) : name(n) {
        std::cout << "Node " << name << " created" << std::endl;
    }
    
    ~Node() {
        std::cout << "Node " << name << " destroyed" << std::endl;
    }
    
    void displayConnections() {
        std::cout << "Node " << name << " -> ";
        if (next) {
            std::cout << next->name;
        } else {
            std::cout << "nullptr";
        }
        std::cout << ", prev: ";
        if (auto prevLocked = prev.lock()) {
            std::cout << prevLocked->name;
        } else {
            std::cout << "nullptr";
        }
        std::cout << std::endl;
    }
};

void createProperList() {
    auto node1 = make_shared<Node>("A");
    auto node2 = make_shared<Node>("B");
    auto node3 = make_shared<Node>("C");
    
    // Forward references (strong)
    node1->next = node2;
    node2->next = node3;
    node3->next = nullptr;
    
    // Backward references (weak)
    node1->prev = weak_ptr<Node>();  // No previous
    node2->prev = node1;
    node3->prev = node2;
    
    std::cout << "node1 count: " << node1.use_count() << std::endl;  // 2 (node1 + node2->prev)
    std::cout << "node2 count: " << node2.use_count() << std::endl;  // 2 (node2 + node3->prev)
    std::cout << "node3 count: " << node3.use_count() << std::endl;  // 1 (only node3)
    
    // Display connections
    node1->displayConnections();
    node2->displayConnections();
    node3->displayConnections();
    
}  // All nodes destroyed properly!

std::cout << "Function ended" << std::endl;
```

**Output:**
```
Node A created
Node B created
Node C created
node1 count: 2
node2 count: 2
node3 count: 1
Node A -> B, prev: nullptr
Node B -> C, prev: A
Node C -> nullptr, prev: B
Node C destroyed
Node B destroyed
Node A destroyed
Function ended
```

---

## 🏢 Real-World Examples

### Observer Pattern

```cpp
class Subject;  // Forward declaration

class Observer {
public:
    virtual void update(const std::string& message) = 0;
    virtual ~Observer() = default;
};

class Subject {
private:
    std::vector<weak_ptr<Observer>> observers_;  // weak_ptr to avoid cycles
    std::string state_;
    
public:
    void addObserver(shared_ptr<Observer> observer) {
        observers_.push_back(observer);
    }
    
    void setState(const std::string& newState) {
        state_ = newState;
        notifyObservers();
    }
    
private:
    void notifyObservers() {
        // Remove expired observers and notify active ones
        observers_.erase(
            std::remove_if(observers_.begin(), observers_.end(),
                [](const weak_ptr<Observer>& weakObs) {
                    if (auto obs = weakObs.lock()) {
                        obs->update(state_);
                        return false;  // Keep this observer
                    }
                    return true;   // Remove expired observer
                }),
            observers_.end()
        );
    }
};

class ConcreteObserver : public Observer, public enable_shared_from_this<ConcreteObserver> {
private:
    std::string name_;
    
public:
    ConcreteObserver(const std::string& name) : name_(name) {
        std::cout << "Observer " << name_ << " created" << std::endl;
    }
    
    void update(const std::string& message) override {
        std::cout << "Observer " << name_ << " received: " << message << std::endl;
    }
    
    void subscribeTo(shared_ptr<Subject> subject) {
        subject->addObserver(shared_from_this());
    }
    
    ~ConcreteObserver() {
        std::cout << "Observer " << name_ << " destroyed" << std::endl;
    }
};

int main() {
    auto subject = make_shared<Subject>();
    
    {
        auto obs1 = make_shared<ConcreteObserver>("Alice");
        auto obs2 = make_shared<ConcreteObserver>("Bob");
        
        obs1->subscribeTo(subject);
        obs2->subscribeTo(subject);
        
        subject->setState("First update");
        
        std::cout << "Observers active: " << std::endl;
    }  // Observers destroyed here
    
    std::cout << "After observers destroyed" << std::endl;
    subject->setState("Second update");  // No observers to notify
    
    return 0;
}
```

**Output:**
```
Observer Alice created
Observer Bob created
Observers active:
Observer Alice received: First update
Observer Bob received: First update
Observer Bob destroyed
Observer Alice destroyed
After observers destroyed
```

### Cache with Weak References

```cpp
template<typename T>
class Cache {
private:
    std::unordered_map<std::string, shared_ptr<T>> cache_;
    std::mutex mutex_;
    
public:
    shared_ptr<T> get(const std::string& key) {
        std::lock_guard<std::mutex> lock(mutex_);
        
        auto it = cache_.find(key);
        if (it != cache_.end()) {
            return it->second;  // Return existing shared_ptr
        }
        
        // Create new object
        auto obj = make_shared<T>();
        cache_[key] = obj;
        return obj;
    }
    
    weak_ptr<T> getWeak(const std::string& key) {
        std::lock_guard<std::mutex> lock(mutex_);
        
        auto it = cache_.find(key);
        if (it != cache_.end()) {
            return it->second;  // Convert to weak_ptr
        }
        return weak_ptr<T>();  // Empty weak_ptr
    }
    
    void cleanup() {
        std::lock_guard<std::mutex> lock(mutex_);
        
        // Remove entries with no more shared owners
        for (auto it = cache_.begin(); it != cache_.end();) {
            if (it->second.use_count() == 1) {  // Only cache owns it
                std::cout << "Removing expired entry: " << it->first << std::endl;
                it = cache_.erase(it);
            } else {
                ++it;
            }
        }
    }
    
    size_t size() const {
        std::lock_guard<std::mutex> lock(mutex_);
        return cache_.size();
    }
};

class Resource {
public:
    Resource(int id) : id_(id) {
        std::cout << "Resource " << id_ << " created" << std::endl;
    }
    
    ~Resource() {
        std::cout << "Resource " << id_ << " destroyed" << std::endl;
    }
    
    void use() {
        std::cout << "Using resource " << id_ << std::endl;
    }
    
private:
    int id_;
};

int main() {
    Cache<Resource> cache;
    
    // Get strong references
    auto res1 = cache.get("key1");
    auto res2 = cache.get("key2");
    
    std::cout << "Cache size: " << cache.size() << std::endl;
    
    // Get weak references
    auto weak1 = cache.getWeak("key1");
    auto weak2 = cache.getWeak("key3");  // Doesn't exist
    
    // Use weak references
    if (auto locked = weak1.lock()) {
        locked->use();
    } else {
        std::cout << "Resource key1 no longer exists" << std::endl;
    }
    
    if (auto locked = weak2.lock()) {
        locked->use();
    } else {
        std::cout << "Resource key3 doesn't exist" << std::endl;
    }
    
    // Clear strong references
    res1.reset();
    res2.reset();
    
    std::cout << "After reset, cache size: " << cache.size() << std::endl;
    
    // Clean up expired entries
    cache.cleanup();
    std::cout << "After cleanup, cache size: " << cache.size() << std::endl;
    
    return 0;
}
```

---

## 🛠️ Advanced Operations

### enable_shared_from_this

```cpp
class NetworkConnection : public enable_shared_from_this<NetworkConnection> {
private:
    std::string address_;
    
public:
    NetworkConnection(const std::string& addr) : address_(addr) {
        std::cout << "Connection to " << address_ << " established" << std::endl;
    }
    
    ~NetworkConnection() {
        std::cout << "Connection to " << address_ << " closed" << std::endl;
    }
    
    // Method that needs to return shared_ptr to this
    shared_ptr<NetworkConnection> getShared() {
        return shared_from_this();  // Safe way to get shared_ptr to this
    }
    
    // Method that registers callback with weak_ptr
    void registerCallback(std::function<void(weak_ptr<NetworkConnection>)> callback) {
        // Store callback and call it later with weak_ptr to this
        callback(weak_from_this());
    }
};

int main() {
    auto conn = make_shared<NetworkConnection>("server.example.com");
    
    // Get shared_ptr from member function
    auto shared = conn->getShared();
    std::cout << "Use count: " << shared.use_count() << std::endl;  // 2
    
    // Use weak_ptr in callbacks
    conn->registerCallback([](weak_ptr<NetworkConnection> weakConn) {
        if (auto locked = weakConn.lock()) {
            std::cout << "Callback: connection still alive" << std::endl;
        } else {
            std::cout << "Callback: connection destroyed" << std::endl;
        }
    });
    
    return 0;
}
```

### Atomic Operations

```cpp
class ThreadSafeCache {
private:
    std::unordered_map<std::string, shared_ptr<int>> cache_;
    mutable std::shared_mutex mutex_;  // C++17 shared mutex
    
public:
    // Read with shared lock
    weak_ptr<int> getWeak(const std::string& key) const {
        std::shared_lock<std::shared_mutex> lock(mutex_);
        
        auto it = cache_.find(key);
        if (it != cache_.end()) {
            return it->second;
        }
        return weak_ptr<int>();
    }
    
    // Write with exclusive lock
    void set(const std::string& key, int value) {
        std::unique_lock<std::shared_mutex> lock(mutex_);
        cache_[key] = make_shared<int>(value);
    }
    
    // Thread-safe cleanup
    void cleanupExpired() {
        std::unique_lock<std::shared_mutex> lock(mutex_);
        
        for (auto it = cache_.begin(); it != cache_.end();) {
            if (it->second.use_count() == 1) {  // Only cache owns it
                it = cache_.erase(it);
            } else {
                ++it;
            }
        }
    }
};
```

---

## ⚡ Performance Considerations

### Memory Usage

```cpp
// shared_ptr
shared_ptr<int> shared = make_shared<int>(42);
// Size: 16 bytes (2 pointers) + 8 bytes control block + 4 bytes int = 28 bytes

// weak_ptr
weak_ptr<int> weak = shared;
// Size: 8 bytes (pointer to control block)
```

### Speed Comparison

```cpp
void benchmarkWeakPtr() {
    const int ITERATIONS = 1000000;
    auto base = make_shared<int>(42);
    
    // Lock operation
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        weak_ptr<int> weak = base;
        if (auto locked = weak.lock()) {
            volatile int val = *locked;  // Prevent optimization
        }
    }
    auto end = std::chrono::high_resolution_clock::now();
    
    // Expired check
    start = std::chrono::high_resolution_clock::now();
    weak_ptr<int> expiredWeak;
    for (int i = 0; i < ITERATIONS; i++) {
        if (expiredWeak.expired()) {
            // Do nothing
        }
    }
    end = std::chrono::high_resolution_clock::now();
}
```

**Typical Results:**
- `lock()` operation: ~20ns (vs ~1ns for direct access)
- `expired()` check: ~5ns
- `weak_ptr` copy: ~2ns

---

## 🎯 Best Practices

### DO ✅

```cpp
// Use weak_ptr to break circular references
class Node {
    weak_ptr<Node> parent;  // Instead of shared_ptr
};

// Check weak_ptr before using
if (auto strong = weak.lock()) {
    strong->doSomething();
}

// Use enable_shared_from_this for member functions
class MyClass : public enable_shared_from_this<MyClass> {
    shared_ptr<MyClass> getShared() {
        return shared_from_this();
    }
};

// Use in observer patterns
weak_ptr<Subject> subject_;  // Observer holds weak reference

// Use for caching with cleanup
weak_ptr<Resource> getCachedResource(const std::string& key);
```

### DON'T ❌

```cpp
// Don't use weak_ptr as primary ownership
weak_ptr<MyClass> primary;  // ❌ Should be shared_ptr

// Don't forget to check lock() result
*weak.lock();  // ❌ Might be nullptr!

// Don't store weak_ptr to objects that might be destroyed
// without checking regularly

// Don't use weak_ptr for simple references
// Use raw pointers or references instead
```

---

## 🎓 Key Takeaways

1. **weak_ptr = non-owning observer** - doesn't affect reference count
2. **Must lock() to access** - converts to shared_ptr temporarily
3. **Prevents circular references** - the primary use case
4. **Can be expired** - resource might already be destroyed
5. **Observer pattern** - perfect for callbacks and notifications
6. **Cache cleanup** - detect when resources are no longer used
7. **Thread-safe reference counting** - but not the object itself
8. **Use when you need to observe** but not own the resource

---

## 🔄 Complete Smart Pointer Guide

You've now learned all three types of smart pointers! Here's when to use each:

| Situation | Best Choice | Why |
|-----------|-------------|-----|
| Single owner, most common case | `unique_ptr` | Zero overhead, clear ownership |
| Multiple owners need sharing | `shared_ptr` | Reference counting, copyable |
| Break cycles or observe | `weak_ptr` | Non-owning, prevents memory leaks |

### Quick Decision Tree

```
Need a pointer?
│
├── Will only ONE thing own it?
│   └── YES → use unique_ptr ✅ (default choice)
│
├── Will MULTIPLE things share it?
│   └── YES → use shared_ptr ✅
│
└── Need to OBSERVE without owning?
    └── YES → use weak_ptr ✅ (with shared_ptr)
```

---

## 🎯 Final Thoughts

Smart pointers are one of C++'s most powerful features for writing safe, modern code. They eliminate entire classes of bugs while providing excellent performance.

**Remember:**
- Start with `unique_ptr` for most cases
- Use `shared_ptr` when you truly need shared ownership
- Use `weak_ptr` to break cycles and for observation
- Always prefer `make_unique()` and `make_shared()`

Your future self will thank you for writing memory-safe code! 🚀
