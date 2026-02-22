# 🛠️ Dynamic Memory Allocation - Practical Examples
### "Real-world applications and patterns for dynamic memory management"

---

## 🎯 Introduction

This section demonstrates practical applications of dynamic memory allocation that you'll encounter in real-world C++ programming. These examples show how dynamic memory concepts combine to solve complex problems.

---

## 🏗️ Data Structure Implementations

### Dynamic Array Class

```cpp
#include <iostream>
#include <stdexcept>

class DynamicArray {
private:
    int* data;
    int capacity;
    int size;
    
    void resize(int newCapacity) {
        int* newData = new int[newCapacity];
        
        // Copy existing data
        for (int i = 0; i < size; i++) {
            newData[i] = data[i];
        }
        
        // Clean up old data
        delete[] data;
        
        // Update pointers
        data = newData;
        capacity = newCapacity;
        
        std::cout << "Resized to capacity: " << newCapacity << std::endl;
    }
    
public:
    DynamicArray(int initialCapacity = 10) 
        : capacity(initialCapacity), size(0) {
        data = new int[capacity];
        std::cout << "Created dynamic array with capacity " << capacity << std::endl;
    }
    
    ~DynamicArray() {
        delete[] data;
        std::cout << "Destroyed dynamic array" << std::endl;
    }
    
    void add(int value) {
        if (size >= capacity) {
            resize(capacity * 2);  // Double capacity when full
        }
        
        data[size] = value;
        size++;
    }
    
    int get(int index) const {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        return data[index];
    }
    
    void set(int index, int value) {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        data[index] = value;
    }
    
    void insert(int index, int value) {
        if (index < 0 || index > size) {
            throw std::out_of_range("Index out of bounds");
        }
        
        if (size >= capacity) {
            resize(capacity * 2);
        }
        
        // Shift elements to the right
        for (int i = size; i > index; i--) {
            data[i] = data[i - 1];
        }
        
        data[index] = value;
        size++;
    }
    
    void remove(int index) {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        
        // Shift elements to the left
        for (int i = index; i < size - 1; i++) {
            data[i] = data[i + 1];
        }
        
        size--;
    }
    
    int getSize() const { return size; }
    int getCapacity() const { return capacity; }
    
    void display() const {
        std::cout << "Array (size " << size << "/" << capacity << "): ";
        for (int i = 0; i < size; i++) {
            std::cout << data[i] << " ";
        }
        std::cout << std::endl;
    }
};

void demonstrateDynamicArray() {
    std::cout << "=== Dynamic Array Demonstration ===" << std::endl;
    
    DynamicArray arr(3);  // Start with capacity 3
    
    // Add elements
    arr.add(10);
    arr.add(20);
    arr.add(30);
    arr.display();
    
    // Add more elements (will trigger resize)
    arr.add(40);
    arr.add(50);
    arr.display();
    
    // Insert element
    arr.insert(2, 25);
    arr.display();
    
    // Remove element
    arr.remove(3);
    arr.display();
    
    std::cout << "Final size: " << arr.getSize() << std::endl;
    std::cout << "Final capacity: " << arr.getCapacity() << std::endl;
}
```

### Linked List with Dynamic Memory

```cpp
struct Node {
    int data;
    Node* next;
    
    Node(int val) : data(val), next(nullptr) {
        std::cout << "Created node with data " << val << std::endl;
    }
    
    ~Node() {
        std::cout << "Destroyed node with data " << data << std::endl;
    }
};

class LinkedList {
private:
    Node* head;
    Node* tail;
    int size;
    
public:
    LinkedList() : head(nullptr), tail(nullptr), size(0) {
        std::cout << "Created linked list" << std::endl;
    }
    
    ~LinkedList() {
        clear();
        std::cout << "Destroyed linked list" << std::endl;
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
        if (index < 0 || index > size) {
            throw std::out_of_range("Index out of bounds");
        }
        
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
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        
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
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        
        Node* current = head;
        for (int i = 0; i < index; i++) {
            current = current->next;
        }
        
        return current->data;
    }
    
    void display() const {
        std::cout << "Linked list (size " << size << "): ";
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
    std::cout << "=== Linked List Demonstration ===" << std::endl;
    
    LinkedList list;
    
    // Add elements
    list.add(10);
    list.add(20);
    list.add(30);
    list.display();
    
    // Insert elements
    list.insert(0, 5);
    list.insert(2, 25);
    list.display();
    
    // Remove elements
    list.remove(1);
    list.remove(3);
    list.display();
    
    std::cout << "Final size: " << list.getSize() << std::endl;
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
        std::cout << "Created memory pool of " << size << " bytes" << std::endl;
    }
    
    ~MemoryPool() {
        delete[] pool;
        std::cout << "Destroyed memory pool" << std::endl;
    }
    
    void* allocate(size_t size) {
        if (used + size > poolSize) {
            std::cout << "Pool allocation failed: not enough memory" << std::endl;
            return nullptr;
        }
        
        void* ptr = current;
        current += size;
        used += size;
        
        std::cout << "Allocated " << size << " bytes from pool" << std::endl;
        return ptr;
    }
    
    void reset() {
        current = pool;
        used = 0;
        std::cout << "Reset memory pool" << std::endl;
    }
    
    size_t getUsed() const { return used; }
    size_t getAvailable() const { return poolSize - used; }
    
    void displayStatus() const {
        std::cout << "Pool status: " << used << "/" << poolSize 
                 << " bytes used (" << getAvailable() << " available)" << std::endl;
    }
};

void demonstrateMemoryPool() {
    std::cout << "=== Memory Pool Demonstration ===" << std::endl;
    
    MemoryPool pool(1024);  // 1KB pool
    
    pool.displayStatus();
    
    // Allocate some memory
    int* intPtr = (int*)pool.allocate(sizeof(int));
    *intPtr = 42;
    
    char* charPtr = (char*)pool.allocate(100);
    strcpy(charPtr, "Hello from memory pool!");
    
    double* doublePtr = (double*)pool.allocate(sizeof(double));
    *doublePtr = 3.14159;
    
    pool.displayStatus();
    
    std::cout << "Allocated data:" << std::endl;
    std::cout << "int: " << *intPtr << std::endl;
    std::cout << "string: " << charPtr << std::endl;
    std::cout << "double: " << *doublePtr << std::endl;
    
    // Reset pool
    pool.reset();
    pool.displayStatus();
}
```

### Smart Pointer Implementation

```cpp
template<typename T>
class UniquePtr {
private:
    T* ptr;
    
public:
    // Constructor
    explicit UniquePtr(T* p = nullptr) : ptr(p) {
        std::cout << "Created unique pointer" << std::endl;
    }
    
    // Destructor
    ~UniquePtr() {
        if (ptr) {
            std::cout << "Deleted managed object" << std::endl;
            delete ptr;
        }
    }
    
    // Copy constructor (deleted)
    UniquePtr(const UniquePtr&) = delete;
    
    // Copy assignment (deleted)
    UniquePtr& operator=(const UniquePtr&) = delete;
    
    // Move constructor
    UniquePtr(UniquePtr&& other) noexcept : ptr(other.ptr) {
        other.ptr = nullptr;
        std::cout << "Moved unique pointer" << std::endl;
    }
    
    // Move assignment
    UniquePtr& operator=(UniquePtr&& other) noexcept {
        if (this != &other) {
            if (ptr) {
                delete ptr;
            }
            ptr = other.ptr;
            other.ptr = nullptr;
            std::cout << "Move assigned unique pointer" << std::endl;
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
        std::cout << "Released ownership" << std::endl;
        return temp;
    }
    
    // Reset pointer
    void reset(T* p = nullptr) {
        if (ptr) {
            delete ptr;
        }
        ptr = p;
        std::cout << "Reset pointer" << std::endl;
    }
    
    // Check if pointer is valid
    explicit operator bool() const { return ptr != nullptr; }
};

void demonstrateSmartPointer() {
    std::cout << "=== Smart Pointer Demonstration ===" << std::endl;
    
    {
        UniquePtr<int> ptr1(new int(42));
        std::cout << "Value: " << *ptr1 << std::endl;
        
        // Move ownership
        UniquePtr<int> ptr2 = std::move(ptr1);
        std::cout << "Moved value: " << *ptr2 << std::endl;
        std::cout << "ptr1 is valid: " << (ptr1 ? "true" : "false") << std::endl;
        
        // Reset with new value
        ptr2.reset(new int(99));
        std::cout << "Reset value: " << *ptr2 << std::endl;
        
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
        x += 10.0f * deltaTime;
        rotation += 45.0f * deltaTime;
    }
    
    void setPosition(float newX, float newY) {
        x = newX;
        y = newY;
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
    Component** components;
    int maxComponents;
    int componentCount;
    bool active;
    
public:
    Entity(int maxComps = 10) 
        : maxComponents(maxComps), componentCount(0), active(true) {
        components = new Component*[maxComponents];
        for (int i = 0; i < maxComponents; i++) {
            components[i] = nullptr;
        }
        std::cout << "Created entity" << std::endl;
    }
    
    ~Entity() {
        for (int i = 0; i < componentCount; i++) {
            delete components[i];
        }
        delete[] components;
        std::cout << "Destroyed entity" << std::endl;
    }
    
    template<typename T, typename... Args>
    T* addComponent(Args&&... args) {
        if (componentCount >= maxComponents) {
            std::cout << "Cannot add more components" << std::endl;
            return nullptr;
        }
        
        T* component = new T(std::forward<Args>(args)...);
        components[componentCount] = component;
        componentCount++;
        
        std::cout << "Added component" << std::endl;
        return component;
    }
    
    template<typename T>
    T* getComponent() {
        for (int i = 0; i < componentCount; i++) {
            T* casted = dynamic_cast<T*>(components[i]);
            if (casted) {
                return casted;
            }
        }
        return nullptr;
    }
    
    void update(float deltaTime) {
        if (!active) return;
        
        for (int i = 0; i < componentCount; i++) {
            components[i]->update(deltaTime);
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

### Message Queue System

```cpp
struct Message {
    int id;
    std::string content;
    time_t timestamp;
    
    Message(int msgId, const std::string& msg) 
        : id(msgId), content(msg), timestamp(time(nullptr)) {
        std::cout << "Created message " << id << ": " << content << std::endl;
    }
    
    ~Message() {
        std::cout << "Destroyed message " << id << std::endl;
    }
};

class MessageQueue {
private:
    Message** messages;
    int capacity;
    int count;
    int nextId;
    
    void resize() {
        int newCapacity = capacity * 2;
        Message** newMessages = new Message*[newCapacity];
        
        for (int i = 0; i < count; i++) {
            newMessages[i] = messages[i];
        }
        
        delete[] messages;
        messages = newMessages;
        capacity = newCapacity;
        
        std::cout << "Resized message queue to " << newCapacity << std::endl;
    }
    
public:
    MessageQueue(int initialCapacity = 10) 
        : capacity(initialCapacity), count(0), nextId(1) {
        messages = new Message*[capacity];
        for (int i = 0; i < capacity; i++) {
            messages[i] = nullptr;
        }
        std::cout << "Created message queue" << std::endl;
    }
    
    ~MessageQueue() {
        clear();
        delete[] messages;
        std::cout << "Destroyed message queue" << std::endl;
    }
    
    int sendMessage(const std::string& content) {
        if (count >= capacity) {
            resize();
        }
        
        Message* msg = new Message(nextId++, content);
        messages[count] = msg;
        count++;
        
        return msg->id;
    }
    
    Message* getMessage(int id) {
        for (int i = 0; i < count; i++) {
            if (messages[i]->id == id) {
                return messages[i];
            }
        }
        return nullptr;
    }
    
    void processMessages() {
        std::cout << "Processing " << count << " messages:" << std::endl;
        
        for (int i = 0; i < count; i++) {
            Message* msg = messages[i];
            std::cout << "Message " << msg->id << ": " << msg->content 
                     << " (timestamp: " << msg->timestamp << ")" << std::endl;
        }
    }
    
    void clear() {
        for (int i = 0; i < count; i++) {
            delete messages[i];
        }
        count = 0;
        std::cout << "Cleared message queue" << std::endl;
    }
    
    int getCount() const { return count; }
};

void demonstrateMessageQueue() {
    std::cout << "=== Message Queue Demonstration ===" << std::endl;
    
    MessageQueue queue;
    
    // Send some messages
    int id1 = queue.sendMessage("Hello from client");
    int id2 = queue.sendMessage("Request data");
    int id3 = queue.sendMessage("Disconnect");
    
    std::cout << "Sent " << queue.getCount() << " messages" << std::endl;
    
    // Process messages
    queue.processMessages();
    
    // Find specific message
    Message* msg = queue.getMessage(id2);
    if (msg) {
        std::cout << "Found message " << id2 << ": " << msg->content << std::endl;
    }
    
    queue.clear();
}
```

---

## 📊 Data Processing Examples

### Dynamic Data Pipeline

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
        std::cout << "Created data processor with " << size << " elements" << std::endl;
    }
    
    ~DataProcessor() {
        delete[] data;
        std::cout << "Destroyed data processor" << std::endl;
    }
    
    DataProcessor& filter(int threshold) {
        std::cout << "Filtering values below " << threshold << std::endl;
        
        int* filtered = new int[size];
        int filteredCount = 0;
        
        for (int i = 0; i < size; i++) {
            if (data[i] >= threshold) {
                filtered[filteredCount] = data[i];
                filteredCount++;
            }
        }
        
        delete[] data;
        data = filtered;
        size = filteredCount;
        
        return *this;
    }
    
    DataProcessor& transform(int multiplier) {
        std::cout << "Multiplying values by " << multiplier << std::endl;
        
        for (int i = 0; i < size; i++) {
            data[i] *= multiplier;
        }
        
        return *this;
    }
    
    DataProcessor& normalize() {
        std::cout << "Normalizing values to 0-100 range" << std::endl;
        
        if (size == 0) return *this;
        
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
        std::cout << "Data (" << size << " elements): ";
        for (int i = 0; i < size; i++) {
            std::cout << data[i] << " ";
        }
        std::cout << std::endl;
    }
    
    int* getData() const { return data; }
    int getSize() const { return size; }
    
    DataProcessor& sort() {
        std::cout << "Sorting data" << std::endl;
        
        // Simple bubble sort
        for (int i = 0; i < size - 1; i++) {
            for (int j = 0; j < size - i - 1; j++) {
                if (data[j] > data[j + 1]) {
                    int temp = data[j];
                    data[j] = data[j + 1];
                    data[j + 1] = temp;
                }
            }
        }
        
        return *this;
    }
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
              .sort()          // Sort ascending
              .normalize();    // Normalize to 0-100
    
    std::cout << "Processed data: ";
    processor.display();
    
    // Calculate statistics
    int* processed = processor.getData();
    int sum = 0;
    for (int i = 0; i < processor.getSize(); i++) {
        sum += processed[i];
    }
    
    std::cout << "Sum: " << sum << std::endl;
    std::cout << "Average: " << (double)sum / processor.getSize() << std::endl;
}
```

### Dynamic Matrix Operations

```cpp
class Matrix {
private:
    int** data;
    int rows;
    int cols;
    
public:
    Matrix(int r, int c) : rows(r), cols(c) {
        data = new int*[rows];
        for (int i = 0; i < rows; i++) {
            data[i] = new int[cols];
            for (int j = 0; j < cols; j++) {
                data[i][j] = 0;
            }
        }
        std::cout << "Created " << rows << "x" << cols << " matrix" << std::endl;
    }
    
    ~Matrix() {
        for (int i = 0; i < rows; i++) {
            delete[] data[i];
        }
        delete[] data;
        std::cout << "Destroyed matrix" << std::endl;
    }
    
    void set(int row, int col, int value) {
        if (row >= 0 && row < rows && col >= 0 && col < cols) {
            data[row][col] = value;
        }
    }
    
    int get(int row, int col) const {
        if (row >= 0 && row < rows && col >= 0 && col < cols) {
            return data[row][col];
        }
        return 0;
    }
    
    Matrix* add(const Matrix& other) const {
        if (rows != other.rows || cols != other.cols) {
            std::cout << "Cannot add matrices of different sizes" << std::endl;
            return nullptr;
        }
        
        Matrix* result = new Matrix(rows, cols);
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                result->data[i][j] = data[i][j] + other.data[i][j];
            }
        }
        
        return result;
    }
    
    Matrix* multiply(const Matrix& other) const {
        if (cols != other.rows) {
            std::cout << "Cannot multiply matrices" << std::endl;
            return nullptr;
        }
        
        Matrix* result = new Matrix(rows, other.cols);
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < other.cols; j++) {
                for (int k = 0; k < cols; k++) {
                    result->data[i][j] += data[i][k] * other.data[k][j];
                }
            }
        }
        
        return result;
    }
    
    Matrix* transpose() const {
        Matrix* result = new Matrix(cols, rows);
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                result->data[j][i] = data[i][j];
            }
        }
        
        return result;
    }
    
    void display() const {
        std::cout << "Matrix (" << rows << "x" << cols << "):" << std::endl;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                std::cout << data[i][j] << "\t";
            }
            std::cout << std::endl;
        }
    }
    
    int getRows() const { return rows; }
    int getCols() const { return cols; }
};

void demonstrateMatrixOperations() {
    std::cout << "=== Matrix Operations Demonstration ===" << std::endl;
    
    Matrix mat1(2, 3);
    Matrix mat2(2, 3);
    
    // Fill matrices
    mat1.set(0, 0, 1); mat1.set(0, 1, 2); mat1.set(0, 2, 3);
    mat1.set(1, 0, 4); mat1.set(1, 1, 5); mat1.set(1, 2, 6);
    
    mat2.set(0, 0, 7); mat2.set(0, 1, 8); mat2.set(0, 2, 9);
    mat2.set(1, 0, 10); mat2.set(1, 1, 11); mat2.set(1, 2, 12);
    
    std::cout << "Matrix 1:" << std::endl;
    mat1.display();
    
    std::cout << "Matrix 2:" << std::endl;
    mat2.display();
    
    // Matrix addition
    Matrix* sum = mat1.add(mat2);
    if (sum) {
        std::cout << "Matrix 1 + Matrix 2:" << std::endl;
        sum->display();
        delete sum;
    }
    
    // Matrix transpose
    Matrix* transpose = mat1.transpose();
    if (transpose) {
        std::cout << "Transpose of Matrix 1:" << std::endl;
        transpose->display();
        delete transpose;
    }
}
```

---

## 🎯 Key Takeaways

1. **Dynamic arrays** need manual resizing and memory management
2. **Linked lists** use dynamic memory for flexible node structures
3. **Memory pools** provide efficient allocation for many small objects
4. **Smart pointers** automate memory management with RAII
5. **Entity systems** use dynamic components for flexible game objects
6. **Message queues** handle dynamic message storage and processing
7. **Data pipelines** process dynamic datasets efficiently
8. **Matrix operations** require careful 2D memory management

---

## 🔄 Complete Dynamic Memory Applications Summary

| Application | Memory Pattern | Key Concepts |
|-------------|---------------|--------------|
| Dynamic Array | Resizable array | Automatic resizing, capacity management |
| Linked List | Node chain | Dynamic node allocation/deallocation |
| Memory Pool | Pre-allocated pool | Efficient bulk allocation |
| Smart Pointer | RAII wrapper | Automatic cleanup |
| Entity System | Component array | Dynamic component management |
| Message Queue | Dynamic buffer | Message lifecycle management |
| Data Pipeline | Transform chain | In-place data processing |
| Matrix Operations | 2D arrays | Nested memory management |

---

## 🎓 Final Thoughts

These practical examples demonstrate how dynamic memory allocation is used in real-world C++ applications. The key is understanding:

1. **Memory lifecycle** - when to allocate and deallocate
2. **Ownership semantics** - who is responsible for cleanup
3. **Performance considerations** - cache-friendly access patterns
4. **Safety practices** - null checks, bounds checking
5. **Modern alternatives** - smart pointers, containers

Master these patterns and you'll be ready to tackle complex C++ projects with confidence! 🚀
