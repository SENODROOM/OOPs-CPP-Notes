# 🎯 unique_ptr - The Sole Owner
### "One pointer to rule them all, and in the darkness bind them"

---

## 🎯 Core Concept

`unique_ptr` is a smart pointer that **exclusively owns** the managed object. Only one `unique_ptr` can point to a given resource at any time.

### Key Characteristics

- ✅ **Exclusive ownership** - only one owner allowed
- ✅ **Zero overhead** - same size and speed as raw pointer
- ✅ **Move-only** - cannot be copied, only moved
- ✅ **Automatic cleanup** - destructor frees memory
- ✅ **Exception safe** - cleanup happens even with exceptions

---

## 🏗️ Internal Implementation

### What Makes `unique_ptr` Special?

```cpp
template<typename T>
class unique_ptr {
private:
    T* ptr;  // Just a raw pointer - no overhead!
    
public:
    // Constructor
    constexpr unique_ptr() noexcept : ptr(nullptr) {}
    explicit unique_ptr(T* p) noexcept : ptr(p) {}
    
    // Destructor - automatic cleanup
    ~unique_ptr() {
        if (ptr) {
            delete ptr;  // Free the memory
        }
    }
    
    // Delete copy operations - prevents multiple owners!
    unique_ptr(const unique_ptr&) = delete;
    unique_ptr& operator=(const unique_ptr&) = delete;
    
    // Allow move operations - transfer ownership
    unique_ptr(unique_ptr&& other) noexcept : ptr(other.ptr) {
        other.ptr = nullptr;  // Source gives up ownership
    }
    
    unique_ptr& operator=(unique_ptr&& other) noexcept {
        if (this != &other) {
            delete ptr;  // Clean up current resource
            ptr = other.ptr;
            other.ptr = nullptr;  // Source gives up ownership
        }
        return *this;
    }
    
    // Pointer operations
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }
    T* get() const noexcept { return ptr; }
    
    // Release ownership
    T* release() noexcept {
        T* temp = ptr;
        ptr = nullptr;
        return temp;
    }
};
```

### Memory Layout

```
unique_ptr object (8 bytes):
┌─────────────────┐
│ T* ptr          │ ← Points to managed object
└─────────────────┘

Same size as raw pointer! No overhead.
```

---

## 📝 Creating and Using unique_ptr

### Basic Creation

```cpp
#include <memory>
#include <iostream>

int main() {
    // Method 1: make_unique (preferred)
    auto ptr1 = make_unique<int>(42);
    
    // Method 2: Direct constructor
    unique_ptr<int> ptr2(new int(99));
    
    // Use like a regular pointer
    std::cout << *ptr1 << std::endl;  // 42
    std::cout << *ptr2 << std::endl;  // 99
    
    // No delete needed - automatic!
}
```

### With Custom Types

```cpp
class MyClass {
public:
    MyClass(int value) : data(value) {
        std::cout << "MyClass constructed with " << data << std::endl;
    }
    
    ~MyClass() {
        std::cout << "MyClass destroyed" << std::endl;
    }
    
    void doSomething() {
        std::cout << "Doing something with " << data << std::endl;
    }
    
private:
    int data;
};

int main() {
    auto obj = make_unique<MyClass>(123);
    obj->doSomething();  // Use arrow operator like raw pointer
    
}  // Destructor called automatically here
```

**Output:**
```
MyClass constructed with 123
Doing something with 123
MyClass destroyed
```

---

## 🔄 Move Semantics - The Magic

### Why Can't We Copy?

```cpp
unique_ptr<int> ptr1 = make_unique<int>(42);
unique_ptr<int> ptr2 = ptr1;  // ❌ COMPILE ERROR!

// Error: copy constructor is deleted
// Reason: Would create two owners for same resource!
```

### Moving Ownership

```cpp
unique_ptr<int> ptr1 = make_unique<int>(42);
unique_ptr<int> ptr2 = move(ptr1);  // ✅ Transfer ownership

// After move:
// ptr1 is empty (nullptr)
// ptr2 owns the resource

std::cout << "ptr2: " << *ptr2 << std::endl;  // 42
// std::cout << *ptr1 << std::endl;  // ❌ CRASH! ptr1 is empty
```

### Visual Representation

```
Before move:
ptr1 ──────────────► [ 42 ]     ptr2 ──► nullptr

After move:
ptr1 ──► nullptr     ptr2 ──────────────► [ 42 ]
```

---

## 🏢 Real-World Examples

### Function Parameters

```cpp
void processData(unique_ptr<MyClass> data) {
    data->doSomething();
}  // data destroyed here

int main() {
    auto myData = make_unique<MyClass>(123);
    processData(move(myData));  // Transfer ownership
    
    // myData is now empty
    return 0;
}
```

### Return Values

```cpp
unique_ptr<MyClass> createData(int value) {
    return make_unique<MyClass>(value);  // NRVO - no copy
}

int main() {
    auto data = createData(456);  // Move constructor
    data->doSomething();
}
```

### Class Members

```cpp
class ResourceManager {
private:
    unique_ptr<Resource> resource_;
    
public:
    ResourceManager() : resource_(make_unique<Resource>()) {
        std::cout << "Resource Manager created" << std::endl;
    }
    
    void useResource() {
        if (resource_) {
            resource_->doWork();
        }
    }
    
    // Move constructor
    ResourceManager(ResourceManager&& other) noexcept 
        : resource_(move(other.resource_)) {
    }
};

int main() {
    ResourceManager manager1;
    manager1.useResource();
    
    ResourceManager manager2 = move(manager1);  // Transfer ownership
    manager2.useResource();
    // manager1.resource_ is now empty
}
```

---

## 📊 Arrays with unique_ptr

### Creating Arrays

```cpp
// Create array of 5 integers
auto arr = make_unique<int[]>(5);

// Initialize elements
for (int i = 0; i < 5; i++) {
    arr[i] = i * 10;
}

// Access like regular array
std::cout << arr[2] << std::endl;  // 20

// Automatically deletes[] when out of scope
```

### Custom Type Arrays

```cpp
class Widget {
public:
    Widget(int id) : id_(id) {
        std::cout << "Widget " << id_ << " created" << std::endl;
    }
    
    ~Widget() {
        std::cout << "Widget " << id_ << " destroyed" << std::endl;
    }
    
    void activate() {
        std::cout << "Widget " << id_ << " activated" << std::endl;
    }
    
private:
    int id_;
};

int main() {
    auto widgets = make_unique<Widget[]>(3);
    
    // Note: make_unique<T[]>() doesn't pass constructor arguments
    // For custom constructors with arguments, use different approach:
    
    // Method 1: Vector of unique_ptr
    std::vector<unique_ptr<Widget>> widgetVec;
    widgetVec.push_back(make_unique<Widget>(1));
    widgetVec.push_back(make_unique<Widget>(2));
    widgetVec.push_back(make_unique<Widget>(3));
    
    for (auto& widget : widgetVec) {
        widget->activate();
    }
}
```

---

## 🛠️ Advanced Operations

### Releasing Ownership

```cpp
auto ptr = make_unique<int>(42);

// Release ownership - returns raw pointer
int* raw = ptr.release();  // ptr becomes empty

// Now you're responsible for deletion
delete raw;  // Must delete manually!
```

### Resetting

```cpp
auto ptr = make_unique<int>(42);
std::cout << *ptr << std::endl;  // 42

// Reset with new value
ptr.reset(new int(99));
std::cout << *ptr << std::endl;  // 99

// Reset to empty
ptr.reset();  // Deletes current object, becomes empty
```

### Swapping

```cpp
auto ptr1 = make_unique<int>(42);
auto ptr2 = make_unique<int>(99);

std::cout << "Before: " << *ptr1 << ", " << *ptr2 << std::endl;  // 42, 99

ptr1.swap(ptr2);

std::cout << "After: " << *ptr1 << ", " << *ptr2 << std::endl;   // 99, 42
```

---

## ⚡ Performance Analysis

### Memory Usage

```cpp
// Raw pointer
int* raw = new int(42);
// Size: 8 bytes (pointer) + 4 bytes (int) = 12 bytes

// unique_ptr
unique_ptr<int> smart = make_unique<int>(42);
// Size: 8 bytes (pointer) + 4 bytes (int) = 12 bytes
```

**Result:** Zero memory overhead!

### Speed Comparison

```cpp
#include <chrono>

void benchmark() {
    const int ITERATIONS = 10000000;
    
    // Raw pointer benchmark
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        int* raw = new int(i);
        *raw = *raw * 2;
        delete raw;
    }
    auto end = std::chrono::high_resolution_clock::now();
    
    // unique_ptr benchmark  
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        auto smart = make_unique<int>(i);
        *smart = *smart * 2;
    }
    end = std::chrono::high_resolution_clock::now();
}
```

**Typical Results:**
- Raw pointer: ~100ms
- unique_ptr: ~105ms (5% overhead due to make_unique)

**Conclusion:** Performance is virtually identical!

---

## 🎯 Best Practices

### DO ✅

```cpp
// Use make_unique
auto ptr = make_unique<MyClass>(arg1, arg2);

// Pass by move when transferring ownership
void takeOwnership(unique_ptr<MyClass> obj);

// Return by value (NRVO optimization)
unique_ptr<MyClass> createObject() {
    return make_unique<MyClass>();
}

// Use with standard containers
std::vector<unique_ptr<MyClass>> objects;
objects.push_back(make_unique<MyClass>());
```

### DON'T ❌

```cpp
// Don't use new directly (less safe)
unique_ptr<MyClass> ptr(new MyClass());

// Don't copy (won't compile anyway)
unique_ptr<MyClass> copy = original;

// Don't use after move
auto moved = move(original);
moved->doSomething();  // ✅ OK
original->doSomething();  // ❌ CRASH!

// Don't forget to check after release()
int* raw = ptr.release();
if (raw) {
    delete raw;  // Must delete manually
}
```

---

## 🔄 Common Patterns

### Factory Pattern

```cpp
class ObjectFactory {
public:
    static unique_ptr<MyObject> create(Type type) {
        switch (type) {
            case Type::A:
                return make_unique<ObjectA>();
            case Type::B:
                return make_unique<ObjectB>();
            default:
                return nullptr;
        }
    }
};

auto obj = ObjectFactory::create(Type::A);
```

### PIMPL (Pointer to Implementation)

```cpp
// Header file
class MyClass {
public:
    MyClass();
    ~MyClass();
    void doSomething();
    
private:
    class Impl;
    unique_ptr<Impl> pimpl_;
};

// Implementation file
class MyClass::Impl {
public:
    void doSomething() {
        std::cout << "Implementation details hidden" << std::endl;
    }
};

MyClass::MyClass() : pimpl_(make_unique<Impl>()) {}
MyClass::~MyClass() = default;

void MyClass::doSomething() {
    pimpl_->doSomething();
}
```

---

## 🎓 Key Takeaways

1. **unique_ptr = exclusive ownership** - only one owner at a time
2. **Zero overhead** - same size and speed as raw pointer
3. **Move-only semantics** - transfer ownership with `move()`
4. **Automatic cleanup** - destructor frees memory automatically
5. **Exception safe** - cleanup happens even with exceptions
6. **Use `make_unique`** - safer and often faster than `new`
7. **Perfect for most use cases** - should be your default choice

---

## 🔄 Next Steps

Now that you understand `unique_ptr`, let's explore how to share ownership between multiple parts of your code:

*Continue reading: [shared_ptr - Shared Ownership](SharedPtr.md)*
