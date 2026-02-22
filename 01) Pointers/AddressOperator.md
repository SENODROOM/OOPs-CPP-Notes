# 📍 Address Operator (&)
### "The GPS of memory - finding where variables live"

---

## 🎯 Core Concept

The **address operator** (`&`) is a unary operator that returns the **memory address** of its operand. It answers the question: "Where in memory is this variable stored?"

### The House Analogy

```
Variable = House with address "123 Main Street"
&variable = GPS coordinates to "123 Main Street"

┌─────────────────┐
│   House       │
│ Address: 123   │ ← &house = "123 Main Street"
│ Value: Gold    │
└─────────────────┘
```

---

## 🏗️ Syntax and Usage

### Basic Syntax

```cpp
&variable_name
```

### Examples with Different Types

```cpp
#include <iostream>

int main() {
    // Basic types
    int    i = 42;
    double d = 3.14;
    char   c = 'A';
    bool   b = true;
    
    // Get addresses
    int*    iPtr = &i;     // Address of integer
    double* dPtr = &d;     // Address of double
    char*   cPtr = &c;     // Address of character
    bool*   bPtr = &b;     // Address of boolean
    
    std::cout << "Addresses:" << std::endl;
    std::cout << "&i = " << iPtr << std::endl;  // e.g., 0x7ffd1234
    std::cout << "&d = " << dPtr << std::endl;  // e.g., 0x7ffd1238
    std::cout << "&c = " << cPtr << std::endl;  // e.g., 0x7ffd123C
    std::cout << "&b = " << bPtr << std::endl;  // e.g., 0x7ffd123D
    
    return 0;
}
```

### With Objects

```cpp
#include <string>
#include <vector>

int main() {
    std::string str = "Hello";
    std::vector<int> vec = {1, 2, 3};
    
    std::string* strPtr = &str;      // Address of string object
    std::vector<int>* vecPtr = &vec;  // Address of vector object
    
    std::cout << "String address: " << strPtr << std::endl;
    std::cout << "Vector address: " << vecPtr << std::endl;
    
    return 0;
}
```

---

## 🖼️ Visual Representation

### Memory Layout with Addresses

```
Stack Memory:
┌─────────────────────────────────────────────────────────────────┐
│ Address: 0x7ffd1234 │ Address: 0x7ffd1238 │ Address: 0x7ffd123C │ Address: 0x7ffd123D │
├─────────────────┬─────────────────┬─────────────────┬─────────────────┤
│ Variable: i      │ Variable: d      │ Variable: c      │ Variable: b      │
│ Value: 42        │ Value: 3.14      │ Value: 'A'       │ Value: true      │
└─────────────────┴─────────────────┴─────────────────┴─────────────────┘
      ▲                ▲                ▲                ▲
      │                │                │                │
      │                │                │                │
      └────────────────┴────────────────┴────────────────┴────────────────┘
              │                │                │                │
              ▼                ▼                ▼                ▼
         ┌─────────────────────────────────────────────────────┐
         │                 Pointer Variables                 │
         │ ┌─────────────────────────────────────────────────┐ │
         │ │ int* iPtr = &i;    │ double* dPtr = &d;    │ ... │
         │ └─────────────────────────────────────────────────┘ │
         └─────────────────────────────────────────────────────┘
```

---

## 🔄 Address Operator in Action

### Step-by-Step Example

```cpp
#include <iostream>

void demonstrateAddressOperator() {
    std::cout << "=== Address Operator Demonstration ===" << std::endl;
    
    // Step 1: Create variables
    int x = 42;
    int y = 99;
    
    std::cout << "Created variables:" << std::endl;
    std::cout << "x = " << x << std::endl;
    std::cout << "y = " << y << std::endl;
    
    // Step 2: Get addresses
    int* xPtr = &x;
    int* yPtr = &y;
    
    std::cout << "\nGot addresses:" << std::endl;
    std::cout << "&x = " << xPtr << std::endl;
    std::cout << "&y = " << yPtr << std::endl;
    
    // Step 3: Show relationship
    std::cout << "\nRelationships:" << std::endl;
    std::cout << "xPtr points to x: " << (xPtr == &x) << std::endl;  // true
    std::cout << "yPtr points to y: " << (yPtr == &y) << std::endl;  // true
    std::cout << "x and y have different addresses: " << (&x != &y) << std::endl;  // true
    
    // Step 4: Use addresses
    std::cout << "\nUsing addresses:" << std::endl;
    std::cout << "Value at xPtr: " << *xPtr << std::endl;  // 42
    std::cout << "Value at yPtr: " << *yPtr << std::endl;  // 99
    
    // Step 5: Modify through addresses
    *xPtr = 100;
    std::cout << "After *xPtr = 100:" << std::endl;
    std::cout << "x = " << x << std::endl;  // 100
}

int main() {
    demonstrateAddressOperator();
    return 0;
}
```

**Output:**
```
=== Address Operator Demonstration ===
Created variables:
x = 42
y = 99

Got addresses:
&x = 0x7ffd1234
&y = 0x7ffd1238

Relationships:
xPtr points to x: 1
yPtr points to y: 1
x and y have different addresses: 1

Using addresses:
Value at xPtr: 42
Value at yPtr: 99

After *xPtr = 100:
x = 100
```

---

## 🎭 Address vs Value

### Key Difference

```cpp
int value = 42;

std::cout << value;    // 42  ← The VALUE stored in the variable
std::cout << &value;   // 0x7ffd1234  ← The ADDRESS where it's stored
```

### Visual Representation

```
Variable 'value':
┌─────────────────┐
│   Memory Cell   │
│ Address: 0x1000 │ ← &value gives you this
│ Value: 42       │ ← value gives you this
└─────────────────┘
```

---

## 🔍 Address Properties

### Address Uniqueness

Each variable has a unique address (at any given moment):

```cpp
int a = 10;
int b = 20;
int c = 30;

std::cout << (&a != &b) << std::endl;  // true
std::cout << (&b != &c) << std::endl;  // true
std::cout << (&a != &c) << std::endl;  // true
```

### Address Permanence

A variable's address stays the same throughout its lifetime:

```cpp
void showAddressStability() {
    int x = 42;
    int* ptr = &x;
    
    std::cout << "Initial address: " << ptr << std::endl;
    
    x = 99;  // Change value, not address
    std::cout << "After change: " << ptr << std::endl;  // Same address!
    
    {
        int y = 100;
        ptr = &y;  // Point to different variable
        std::cout << "New address: " << ptr << std::endl;
    }
    
    // ptr is now invalid (points to destroyed variable)
}
```

---

## 🏛️ Address Operator with Different Storage Types

### Stack Variables

```cpp
void stackAddresses() {
    int local = 42;
    static int staticVar = 99;
    
    std::cout << "Local variable address: " << &local << std::endl;
    std::cout << "Static variable address: " << &staticVar << std::endl;
}
```

### Heap Variables

```cpp
void heapAddresses() {
    int* dynamic = new int(42);
    
    std::cout << "Heap variable address: " << dynamic << std::endl;
    std::cout << "Heap variable value: " << *dynamic << std::endl;
    
    delete dynamic;  // Don't forget to free!
}
```

### Global Variables

```cpp
int globalVar = 100;

void globalAddresses() {
    std::cout << "Global variable address: " << &globalVar << std::endl;
}
```

---

## ⚠️ Common Mistakes

### 1. Taking Address of Temporary

```cpp
int* badPointer() {
    int temp = 42;
    return &temp;  // ❌ DANGER! temp destroyed when function ends
}

void useBadPointer() {
    int* ptr = badPointer();
    std::cout << *ptr;  // ❌ UNDEFINED BEHAVIOR!
}
```

### 2. Taking Address of Literals

```cpp
// int* ptr = &42;  // ❌ COMPILE ERROR! Can't take address of literal
// int& ref = 99;   // ❌ COMPILE ERROR! Can't reference literal
```

### 3. Taking Address of Register Variables

```cpp
// register int reg = 42;  // Hint to store in register
// int* ptr = &reg;     // ❌ May not work if stored in register
```

### 4. Modifying Const Variables

```cpp
const int constant = 42;
const int* ptr = &constant;  // ✅ OK: pointer to const

// *ptr = 99;  // ❌ COMPILE ERROR! Can't modify through const pointer
```

---

## 🛡️ Safe Usage Practices

### Always Check Validity

```cpp
void safeAddressUse(int* ptr) {
    if (ptr != nullptr) {
        std::cout << "Value: " << *ptr << std::endl;
    } else {
        std::cout << "Null pointer!" << std::endl;
    }
}
```

### Understand Lifetime

```cpp
int* createLocalAddress() {
    int local = 42;
    return &local;  // ❌ Don't do this!
}

int* createHeapAddress() {
    int* heap = new int(42);
    return heap;   // ✅ This is safe (caller must delete)
}
```

### Use With Const Correctly

```cpp
void constCorrectness() {
    int value = 42;
    const int* constPtr = &value;  // Pointer to const int
    int* const ptrToConst = &value;  // Const pointer to int
    
    // *constPtr = 99;  // ❌ Can't modify through const pointer
    *ptrToConst = 99;  // ✅ Can modify value, but not reassign pointer
    // ptrToConst = nullptr;  // ❌ Can't reassign const pointer
}
```

---

## 🔍 Advanced Address Operations

### Address Arithmetic

```cpp
void addressArithmetic() {
    int arr[] = {10, 20, 30, 40, 50};
    
    int* first = &arr[0];    // Address of first element
    int* second = &arr[1];   // Address of second element
    
    std::cout << "First address: " << first << std::endl;
    std::cout << "Second address: " << second << std::endl;
    std::cout << "Difference: " << (second - first) << " bytes" << std::endl;  // Usually 4 (sizeof(int))
}
```

### Address of Array vs Address of First Element

```cpp
void arrayAddressDifference() {
    int arr[5] = {1, 2, 3, 4, 5};
    
    std::cout << "&arr: " << &arr << std::endl;      // Address of whole array
    std::cout << "&arr[0]: " << &arr[0] << std::endl;  // Address of first element
    
    // In most cases, these are the same!
    std::cout << "Same address: " << (&arr == &arr[0]) << std::endl;
}
```

### Member Addresses

```cpp
struct Point {
    int x;
    int y;
};

void memberAddresses() {
    Point pt = {10, 20};
    
    int* xPtr = &pt.x;  // Address of member x
    int* yPtr = &pt.y;  // Address of member y
    
    std::cout << "Point address: " << &pt << std::endl;
    std::cout << "Member x address: " << xPtr << std::endl;
    std::cout << "Member y address: " << yPtr << std::endl;
    std::cout << "Offset of x: " << (char*)xPtr - (char*)&pt << " bytes" << std::endl;
    std::cout << "Offset of y: " << (char*)yPtr - (char*)&pt << " bytes" << std::endl;
}
```

---

## 🎯 Real-World Applications

### Function Parameters - Pass by Address

```cpp
void swapByAddress(int* a, int* b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

void demonstrateSwap() {
    int x = 10;
    int y = 20;
    
    std::cout << "Before: x=" << x << ", y=" << y << std::endl;
    swapByAddress(&x, &y);
    std::cout << "After: x=" << x << ", y=" << y << std::endl;
}
```

### Data Structures - Linked Lists

```cpp
struct Node {
    int data;
    Node* next;
};

void linkedListExample() {
    Node node1 = {10, nullptr};
    Node node2 = {20, nullptr};
    Node node3 = {30, nullptr};
    
    // Link the nodes
    node1.next = &node2;
    node2.next = &node3;
    
    // Traverse using addresses
    Node* current = &node1;
    while (current != nullptr) {
        std::cout << "Data: " << current->data << std::endl;
        current = current->next;
    }
}
```

---

## 🎓 Key Takeaways

1. **& gets address** - returns where variable is stored in memory
2. **All variables have addresses** - even primitives and objects
3. **Addresses are unique** - each variable has its own location
4. **Addresses stay constant** - as long as variable exists
5. **Don't return addresses of locals** - they become invalid
6. **Use with const properly** - respect const-correctness
7. **Addresses are numbers** - usually in hexadecimal format

---

## 🔄 Next Steps

Now that you understand how to get addresses, let's explore how to use those addresses:

*Continue reading: [Dereference Operator (*)](DereferenceOperator.md)*
