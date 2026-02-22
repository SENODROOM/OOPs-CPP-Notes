# 🛠️ Pointers - Practical Examples
### "Real-world scenarios and applications of pointer concepts"

---

## 🎯 Introduction

This section demonstrates practical applications of pointers that you'll encounter in real-world C++ programming. These examples show how pointer concepts combine to solve complex problems.

---

## 🏗️ Data Structure Implementations

### Linked List Implementation

```cpp
#include <iostream>

struct Node {
    int data;
    Node* next;
    
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedList {
private:
    Node* head;
    Node* tail;
    int size;
    
public:
    LinkedList() : head(nullptr), tail(nullptr), size(0) {}
    
    ~LinkedList() {
        clear();
    }
    
    void add(int value) {
        Node* newNode = new Node(value);
        
        if (head == nullptr) {
            head = tail = newNode;
        } else {
            tail->next = newNode;
            tail = newNode;
        }
        size++;
    }
    
    void insert(int index, int value) {
        if (index < 0 || index > size) return;
        
        Node* newNode = new Node(value);
        
        if (index == 0) {
            newNode->next = head;
            head = newNode;
            if (tail == nullptr) tail = newNode;
        } else if (index == size) {
            tail->next = newNode;
            tail = newNode;
        } else {
            Node* current = head;
            for (int i = 0; i < index - 1; i++) {
                current = current->next;
            }
            newNode->next = current->next;
            current->next = newNode;
        }
        size++;
    }
    
    void remove(int index) {
        if (index < 0 || index >= size) return;
        
        Node* toDelete;
        
        if (index == 0) {
            toDelete = head;
            head = head->next;
            if (head == nullptr) tail = nullptr;
        } else {
            Node* current = head;
            for (int i = 0; i < index - 1; i++) {
                current = current->next;
            }
            toDelete = current->next;
            current->next = toDelete->next;
            
            if (toDelete == tail) {
                tail = current;
            }
        }
        
        delete toDelete;
        size--;
    }
    
    int get(int index) const {
        if (index < 0 || index >= size) return -1;
        
        Node* current = head;
        for (int i = 0; i < index; i++) {
            current = current->next;
        }
        return current->data;
    }
    
    void display() const {
        Node* current = head;
        while (current != nullptr) {
            std::cout << current->data << " -> ";
            current = current->next;
        }
        std::cout << "nullptr" << std::endl;
    }
    
    void clear() {
        Node* current = head;
        while (current != nullptr) {
            Node* next = current->next;
            delete current;
            current = next;
        }
        head = tail = nullptr;
        size = 0;
    }
    
    int getSize() const { return size; }
};

void demonstrateLinkedList() {
    LinkedList list;
    
    std::cout << "=== Linked List Demonstration ===" << std::endl;
    
    // Add elements
    list.add(10);
    list.add(20);
    list.add(30);
    list.add(40);
    list.add(50);
    
    std::cout << "After adding elements: ";
    list.display();
    
    // Insert at beginning
    list.insert(0, 5);
    std::cout << "After inserting 5 at beginning: ";
    list.display();
    
    // Insert in middle
    list.insert(3, 25);
    std::cout << "After inserting 25 at index 3: ";
    list.display();
    
    // Remove from middle
    list.remove(2);
    std::cout << "After removing element at index 2: ";
    list.display();
    
    // Get element
    std::cout << "Element at index 3: " << list.get(3) << std::endl;
    
    std::cout << "Final list size: " << list.getSize() << std::endl;
}
```

### Binary Search Tree

```cpp
struct TreeNode {
    int data;
    TreeNode* left;
    TreeNode* right;
    
    TreeNode(int val) : data(val), left(nullptr), right(nullptr) {}
};

class BinarySearchTree {
private:
    TreeNode* root;
    
    TreeNode* insert(TreeNode* node, int value) {
        if (node == nullptr) {
            return new TreeNode(value);
        }
        
        if (value < node->data) {
            node->left = insert(node->left, value);
        } else if (value > node->data) {
            node->right = insert(node->right, value);
        }
        
        return node;
    }
    
    TreeNode* find(TreeNode* node, int value) {
        if (node == nullptr || node->data == value) {
            return node;
        }
        
        if (value < node->data) {
            return find(node->left, value);
        } else {
            return find(node->right, value);
        }
    }
    
    void inorder(TreeNode* node) {
        if (node == nullptr) return;
        
        inorder(node->left);
        std::cout << node->data << " ";
        inorder(node->right);
    }
    
    void destroyTree(TreeNode* node) {
        if (node == nullptr) return;
        
        destroyTree(node->left);
        destroyTree(node->right);
        delete node;
    }
    
public:
    BinarySearchTree() : root(nullptr) {}
    
    ~BinarySearchTree() {
        destroyTree(root);
    }
    
    void insert(int value) {
        root = insert(root, value);
    }
    
    bool contains(int value) {
        return find(root, value) != nullptr;
    }
    
    void displayInorder() {
        inorder(root);
        std::cout << std::endl;
    }
};

void demonstrateBST() {
    BinarySearchTree bst;
    
    std::cout << "=== Binary Search Tree Demonstration ===" << std::endl;
    
    // Insert elements
    int values[] = {50, 30, 70, 20, 40, 60, 80};
    for (int val : values) {
        bst.insert(val);
    }
    
    std::cout << "In-order traversal: ";
    bst.displayInorder();
    
    // Search for elements
    std::cout << "Contains 40: " << bst.contains(40) << std::endl;
    std::cout << "Contains 99: " << bst.contains(99) << std::endl;
}
```

---

## 🗂️ Memory Management Systems

### Custom Memory Pool

```cpp
class MemoryPool {
private:
    char* pool;
    char* current;
    size_t poolSize;
    size_t used;
    
public:
    MemoryPool(size_t size) : poolSize(size), used(0) {
        pool = new char[size];
        current = pool;
    }
    
    ~MemoryPool() {
        delete[] pool;
    }
    
    void* allocate(size_t size) {
        if (used + size > poolSize) {
            return nullptr;  // Out of memory
        }
        
        void* ptr = current;
        current += size;
        used += size;
        return ptr;
    }
    
    void reset() {
        current = pool;
        used = 0;
    }
    
    size_t getUsed() const { return used; }
    size_t getAvailable() const { return poolSize - used; }
};

void demonstrateMemoryPool() {
    MemoryPool pool(1024);  // 1KB pool
    
    std::cout << "=== Memory Pool Demonstration ===" << std::endl;
    std::cout << "Pool size: " << 1024 << " bytes" << std::endl;
    
    // Allocate some memory
    int* intPtr = (int*)pool.allocate(sizeof(int));
    *intPtr = 42;
    
    char* charPtr = (char*)pool.allocate(100);
    strcpy(charPtr, "Hello from memory pool!");
    
    double* doublePtr = (double*)pool.allocate(sizeof(double));
    *doublePtr = 3.14159;
    
    std::cout << "Allocated data:" << std::endl;
    std::cout << "int: " << *intPtr << std::endl;
    std::cout << "string: " << charPtr << std::endl;
    std::cout << "double: " << *doublePtr << std::endl;
    
    std::cout << "Used: " << pool.getUsed() << " bytes" << std::endl;
    std::cout << "Available: " << pool.getAvailable() << " bytes" << std::endl;
    
    // Reset pool
    pool.reset();
    std::cout << "After reset - Used: " << pool.getUsed() << " bytes" << std::endl;
}
```

### Smart Pointer Implementation

```cpp
template<typename T>
class SimpleUniquePtr {
private:
    T* ptr;
    
public:
    // Constructor
    explicit SimpleUniquePtr(T* p = nullptr) : ptr(p) {}
    
    // Destructor
    ~SimpleUniquePtr() {
        delete ptr;
    }
    
    // Copy constructor (deleted)
    SimpleUniquePtr(const SimpleUniquePtr&) = delete;
    
    // Copy assignment (deleted)
    SimpleUniquePtr& operator=(const SimpleUniquePtr&) = delete;
    
    // Move constructor
    SimpleUniquePtr(SimpleUniquePtr&& other) noexcept : ptr(other.ptr) {
        other.ptr = nullptr;
    }
    
    // Move assignment
    SimpleUniquePtr& operator=(SimpleUniquePtr&& other) noexcept {
        if (this != &other) {
            delete ptr;
            ptr = other.ptr;
            other.ptr = nullptr;
        }
        return *this;
    }
    
    // Dereference operators
    T& operator*() const { return *ptr; }
    T* operator->() const { return ptr; }
    
    // Get raw pointer
    T* get() const { return ptr; }
    
    // Release ownership
    T* release() {
        T* temp = ptr;
        ptr = nullptr;
        return temp;
    }
    
    // Reset pointer
    void reset(T* p = nullptr) {
        delete ptr;
        ptr = p;
    }
};

void demonstrateSmartPointer() {
    std::cout << "=== Smart Pointer Demonstration ===" << std::endl;
    
    {
        SimpleUniquePtr<int> ptr1(new int(42));
        std::cout << "ptr1 value: " << *ptr1 << std::endl;
        
        // Move ownership
        SimpleUniquePtr<int> ptr2 = std::move(ptr1);
        std::cout << "ptr2 value: " << *ptr2 << std::endl;
        std::cout << "ptr1 is empty: " << (ptr1.get() == nullptr) << std::endl;
        
        // Reset with new value
        ptr2.reset(new int(99));
        std::cout << "ptr2 new value: " << *ptr2 << std::endl;
        
    }  // Automatic cleanup here
    
    std::cout << "Smart pointer destroyed automatically" << std::endl;
}
```

---

## 🎮 Game Development Examples

### Entity Component System

```cpp
struct Component {
    virtual ~Component() = default;
    virtual void update(float deltaTime) = 0;
};

struct Transform : Component {
    float x, y, rotation;
    
    Transform(float x = 0, float y = 0, float rot = 0) 
        : x(x), y(y), rotation(rot) {}
    
    void update(float deltaTime) override {
        // Update position based on velocity
        x += 10.0f * deltaTime;
        rotation += 45.0f * deltaTime;
    }
    
    void setPosition(float newX, float newY) {
        x = newX;
        y = newY;
    }
    
    void setRotation(float rot) {
        rotation = rot;
    }
};

struct Renderer : Component {
    std::string texture;
    bool visible;
    
    Renderer(const std::string& tex) : texture(tex), visible(true) {}
    
    void update(float deltaTime) override {
        // Animation updates
    }
    
    void setVisible(bool vis) {
        visible = vis;
    }
};

class Entity {
private:
    std::vector<Component*> components;
    bool active;
    
public:
    Entity() : active(true) {}
    
    ~Entity() {
        for (auto component : components) {
            delete component;
        }
    }
    
    template<typename T, typename... Args>
    T* addComponent(Args&&... args) {
        T* component = new T(std::forward<Args>(args)...);
        components.push_back(component);
        return component;
    }
    
    template<typename T>
    T* getComponent() {
        for (auto component : components) {
            if (auto casted = dynamic_cast<T*>(component)) {
                return casted;
            }
        }
        return nullptr;
    }
    
    void update(float deltaTime) {
        if (!active) return;
        
        for (auto component : components) {
            component->update(deltaTime);
        }
    }
    
    void setActive(bool act) { active = act; }
    bool isActive() const { return active; }
};

void demonstrateECS() {
    std::cout << "=== Entity Component System Demonstration ===" << std::endl;
    
    Entity player;
    auto transform = player.addComponent<Transform>(100.0f, 50.0f, 0.0f);
    auto renderer = player.addComponent<Renderer>("player.png");
    
    std::cout << "Player created with Transform and Renderer components" << std::endl;
    
    // Simulate game loop
    for (int frame = 0; frame < 5; frame++) {
        std::cout << "\nFrame " << frame << ":" << std::endl;
        player.update(0.016f);  // 60 FPS
        
        auto playerTransform = player.getComponent<Transform>();
        if (playerTransform) {
            std::cout << "Position: (" << playerTransform->x 
                     << ", " << playerTransform->y << ")" << std::endl;
            std::cout << "Rotation: " << playerTransform->rotation << std::endl;
        }
    }
}
```

---

## 🌐 Network Programming

### Simple Message Queue

```cpp
struct Message {
    int id;
    std::string content;
    time_t timestamp;
    
    Message(int msgId, const std::string& msg) 
        : id(msgId), content(msg), timestamp(time(nullptr)) {}
};

class MessageQueue {
private:
    std::vector<Message*> messages;
    int nextId;
    
public:
    MessageQueue() : nextId(1) {}
    
    ~MessageQueue() {
        clear();
    }
    
    int sendMessage(const std::string& content) {
        Message* msg = new Message(nextId++, content);
        messages.push_back(msg);
        return msg->id;
    }
    
    Message* getMessage(int id) {
        for (auto msg : messages) {
            if (msg->id == id) {
                return msg;
            }
        }
        return nullptr;
    }
    
    void processMessages() {
        std::cout << "Processing " << messages.size() << " messages:" << std::endl;
        
        for (auto msg : messages) {
            std::cout << "Message " << msg->id << ": " << msg->content 
                     << " (timestamp: " << msg->timestamp << ")" << std::endl;
        }
    }
    
    void clear() {
        for (auto msg : messages) {
            delete msg;
        }
        messages.clear();
    }
    
    size_t size() const { return messages.size(); }
};

void demonstrateMessageQueue() {
    std::cout << "=== Message Queue Demonstration ===" << std::endl;
    
    MessageQueue queue;
    
    // Send some messages
    int id1 = queue.sendMessage("Hello from client");
    int id2 = queue.sendMessage("Request data");
    int id3 = queue.sendMessage("Disconnect");
    
    std::cout << "Sent " << queue.size() << " messages" << std::endl;
    
    // Process messages
    queue.processMessages();
    
    // Find specific message
    Message* msg = queue.getMessage(id2);
    if (msg) {
        std::cout << "Found message " << id2 << ": " << msg->content << std::endl;
    }
}
```

---

## 📊 Data Processing Examples

### Data Pipeline

```cpp
class DataProcessor {
private:
    int* data;
    int size;
    
public:
    DataProcessor(int* input, int inputSize) : size(inputSize) {
        data = new int[size];
        for (int i = 0; i < size; i++) {
            data[i] = input[i];
        }
    }
    
    ~DataProcessor() {
        delete[] data;
    }
    
    DataProcessor& filter(int threshold) {
        for (int i = 0; i < size; i++) {
            if (data[i] < threshold) {
                data[i] = 0;
            }
        }
        return *this;
    }
    
    DataProcessor& transform(int multiplier) {
        for (int i = 0; i < size; i++) {
            data[i] *= multiplier;
        }
        return *this;
    }
    
    DataProcessor& normalize() {
        int max = data[0];
        for (int i = 1; i < size; i++) {
            if (data[i] > max) max = data[i];
        }
        
        if (max > 0) {
            for (int i = 0; i < size; i++) {
                data[i] = (data[i] * 100) / max;
            }
        }
        return *this;
    }
    
    void display() const {
        std::cout << "Data: ";
        for (int i = 0; i < size; i++) {
            std::cout << data[i] << " ";
        }
        std::cout << std::endl;
    }
    
    int* getData() const { return data; }
    int getSize() const { return size; }
};

void demonstrateDataPipeline() {
    std::cout << "=== Data Pipeline Demonstration ===" << std::endl;
    
    int rawData[] = {10, 25, 40, 15, 60, 35, 80, 5};
    int size = sizeof(rawData) / sizeof(rawData[0]);
    
    DataProcessor processor(rawData, size);
    
    std::cout << "Original data: ";
    processor.display();
    
    // Chain operations
    processor.filter(20)      // Remove values < 20
              .transform(2)    // Multiply by 2
              .normalize();   // Normalize to 0-100
    
    std::cout << "Processed data: ";
    processor.display();
    
    // Calculate statistics
    int* processed = processor.getData();
    int sum = 0;
    for (int i = 0; i < size; i++) {
        sum += processed[i];
    }
    
    std::cout << "Sum: " << sum << std::endl;
    std::cout << "Average: " << (double)sum / size << std::endl;
}
```

---

## 🎯 Key Takeaways

1. **Linked Lists** use pointers to chain nodes together
2. **Binary Trees** use pointers to create hierarchical structures
3. **Memory Pools** provide efficient memory allocation
4. **Smart Pointers** automate memory management
5. **Entity Systems** use pointers for component management
6. **Message Queues** use pointers for dynamic message handling
7. **Data Pipelines** use pointers for efficient data processing

---

## 🔄 Complete Pointer Applications Summary

| Application | Pointer Use | Key Concepts |
|-------------|-------------|--------------|
| Linked Lists | Node connections | Dynamic memory, traversal |
| Binary Trees | Hierarchical structure | Recursive algorithms |
| Memory Pools | Custom allocation | Memory management |
| Smart Pointers | RAII | Automatic cleanup |
| Entity Systems | Component management | Polymorphism |
| Message Queues | Dynamic storage | Queue operations |
| Data Pipelines | Efficient processing | Method chaining |

---

## 🎓 Final Thoughts

These practical examples demonstrate how pointer concepts combine to solve real-world problems. The key is understanding:

1. **Memory management** - when to allocate and deallocate
2. **Pointer relationships** - how pointers connect different data
3. **Performance considerations** - cache-friendly access patterns
4. **Safety practices** - null checks, bounds checking
5. **Modern alternatives** - smart pointers, containers

Master these patterns, and you'll be ready to tackle complex C++ projects with confidence! 🚀
