# C++ Core Concepts — Complete Reference Guide

> Covers: Pointers · DMA · Smart Pointers · 1D/2D Arrays · Char Arrays · Classes · Objects · Encapsulation · Constructors · Destructors · Getters · Setters

---

## Table of Contents
1. [Pointers](#1-pointers)
2. [Dynamic Memory Allocation (DMA)](#2-dynamic-memory-allocation-dma)
3. [Smart Pointers](#3-smart-pointers)
4. [1D Arrays](#4-1d-arrays)
5. [2D Arrays](#5-2d-arrays)
6. [Char Arrays (C-Strings)](#6-char-arrays-c-strings)
7. [Classes & Objects](#7-classes--objects)
8. [Encapsulation](#8-encapsulation)
9. [Constructor](#9-constructor)
10. [Destructor](#10-destructor)
11. [Getters & Setters](#11-getters--setters)
12. [Complete Class Example](#12-complete-class-example)

---

## 1. Pointers

A **pointer** is a variable that stores the **memory address** of another variable.

### Syntax
```cpp
int x = 10;
int* ptr = &x;   // ptr stores the address of x
```

### Key Operators
| Operator | Name          | Meaning                        |
|----------|---------------|--------------------------------|
| `&`      | Address-of    | Gets the address of a variable |
| `*`      | Dereference   | Gets the value at an address   |

### Example
```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 42;
    int* ptr = &x;

    cout << "Value of x:       " << x    << endl;  // 42
    cout << "Address of x:     " << &x   << endl;  // 0x61ff08 (example)
    cout << "Pointer value:    " << ptr  << endl;  // 0x61ff08
    cout << "Dereferenced ptr: " << *ptr << endl;  // 42

    *ptr = 99;  // Modify x through pointer
    cout << "x is now: " << x << endl;  // 99

    return 0;
}
```

### Pointer Arithmetic
```cpp
int arr[] = {10, 20, 30};
int* p = arr;           // points to arr[0]

cout << *p;             // 10
cout << *(p + 1);       // 20
cout << *(p + 2);       // 30
p++;                    // move to next element
cout << *p;             // 20
```

### Pointer to Pointer
```cpp
int x = 5;
int* p = &x;
int** pp = &p;          // pointer to pointer

cout << **pp;           // 5
```

---

## 2. Dynamic Memory Allocation (DMA)

**DMA** lets you allocate memory at **runtime** on the **heap** instead of the stack. You control when it is created and destroyed.

| Location | Lifetime         | Keyword        |
|----------|------------------|----------------|
| Stack    | Auto (scope-end) | Normal vars    |
| Heap     | Until you delete | `new` / `delete` |

### Allocating a Single Variable
```cpp
int* ptr = new int;       // allocate
*ptr = 100;
cout << *ptr;             // 100
delete ptr;               // free memory
ptr = nullptr;            // good practice
```

### Allocating an Array
```cpp
int n = 5;
int* arr = new int[n];    // allocate array of 5

for (int i = 0; i < n; i++)
    arr[i] = i * 10;

for (int i = 0; i < n; i++)
    cout << arr[i] << " ";   // 0 10 20 30 40

delete[] arr;             // free array (use delete[])
arr = nullptr;
```

### Common Mistakes
```cpp
// ❌ Memory Leak — never deleted
int* p = new int(5);
// (forgot delete p)

// ❌ Dangling Pointer — deleted but still used
delete p;
cout << *p;   // undefined behavior!

// ✅ Correct
delete p;
p = nullptr;
```

---

## 3. Smart Pointers

**Smart pointers** automatically manage memory — no manual `delete` needed. Defined in `<memory>`.

### 3.1 `unique_ptr` — Single Ownership
Only ONE pointer can own the resource. Deleted automatically when it goes out of scope.

```cpp
#include <memory>
using namespace std;

int main() {
    unique_ptr<int> uptr = make_unique<int>(42);
    cout << *uptr << endl;   // 42

    // Transfer ownership
    unique_ptr<int> uptr2 = move(uptr);
    // uptr is now null, uptr2 owns the resource

}   // memory auto-freed here — no delete needed!
```

### 3.2 `shared_ptr` — Shared Ownership
Multiple pointers can share ownership. Memory freed when the **last** shared_ptr is destroyed.

```cpp
#include <memory>
using namespace std;

int main() {
    shared_ptr<int> sp1 = make_shared<int>(100);
    shared_ptr<int> sp2 = sp1;    // both own the resource

    cout << *sp1 << endl;         // 100
    cout << *sp2 << endl;         // 100
    cout << sp1.use_count();      // 2 (two owners)

}   // memory freed when both sp1 and sp2 go out of scope
```

### 3.3 `weak_ptr` — Non-Owning Observer
Observes a `shared_ptr` without affecting ownership count. Used to **break circular references**.

```cpp
#include <memory>
using namespace std;

int main() {
    shared_ptr<int> sp = make_shared<int>(55);
    weak_ptr<int> wp = sp;          // does NOT increase count

    cout << sp.use_count();         // 1 (weak_ptr doesn't count)

    if (auto locked = wp.lock()) {  // safely access if still alive
        cout << *locked;            // 55
    }
}
```

### Smart Pointer Summary
| Type         | Ownership    | Use Case                        |
|--------------|--------------|---------------------------------|
| `unique_ptr` | Single       | Default choice, no sharing      |
| `shared_ptr` | Shared/many  | Multiple owners needed          |
| `weak_ptr`   | None (observer) | Avoid circular references    |

---

## 4. 1D Arrays

### Static (Stack)
```cpp
int arr[5] = {10, 20, 30, 40, 50};

// Access
cout << arr[0];    // 10
cout << arr[4];    // 50

// Loop
for (int i = 0; i < 5; i++)
    cout << arr[i] << " ";
```

### Dynamic (Heap)
```cpp
int n;
cin >> n;
int* arr = new int[n];

for (int i = 0; i < n; i++)
    arr[i] = i + 1;

delete[] arr;
```

### Passing Array to Function
```cpp
void printArray(int* arr, int size) {
    for (int i = 0; i < size; i++)
        cout << arr[i] << " ";
}

int main() {
    int arr[] = {1, 2, 3, 4, 5};
    printArray(arr, 5);
}
```

---

## 5. 2D Arrays

### Static
```cpp
int matrix[3][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// Access
cout << matrix[1][2];   // 6

// Loop
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++)
        cout << matrix[i][j] << " ";
    cout << endl;
}
```

### Dynamic 2D Array
```cpp
int rows = 3, cols = 4;

// Allocate
int** matrix = new int*[rows];
for (int i = 0; i < rows; i++)
    matrix[i] = new int[cols];

// Use
matrix[1][2] = 99;
cout << matrix[1][2];   // 99

// Free
for (int i = 0; i < rows; i++)
    delete[] matrix[i];
delete[] matrix;
```

---

## 6. Char Arrays (C-Strings)

A **char array** stores a string as an array of characters ending with a **null terminator** `'\0'`.

```cpp
#include <cstring>   // for string functions
using namespace std;

char name[] = "Alice";
// Stored as: ['A','l','i','c','e','\0']

cout << name;           // Alice
cout << strlen(name);   // 5 (not counting '\0')
```

### Common C-String Functions
```cpp
char s1[20] = "Hello";
char s2[] = "World";

strlen(s1);           // 5 — length
strcpy(s1, s2);       // copy s2 into s1
strcat(s1, s2);       // concatenate: s1 = s1 + s2
strcmp(s1, s2);       // compare: 0 = equal
```

### Char Array vs std::string
```cpp
// C-style char array
char name1[50] = "Alice";

// C++ string (preferred)
string name2 = "Alice";
name2 += " Smith";      // easy concatenation
cout << name2.length(); // easy length
```

### Dynamic Char Array
```cpp
char* str = new char[100];
strcpy(str, "Dynamic string");
cout << str;
delete[] str;
```

---

## 7. Classes & Objects

A **class** is a blueprint. An **object** is an instance of that class.

```cpp
class Car {
public:
    string brand;
    int speed;

    void drive() {
        cout << brand << " is driving at " << speed << " km/h" << endl;
    }
};

int main() {
    Car myCar;          // Object created
    myCar.brand = "Toyota";
    myCar.speed = 120;
    myCar.drive();      // Toyota is driving at 120 km/h
}
```

### Class vs Object
| Term   | Description                              |
|--------|------------------------------------------|
| Class  | Blueprint/template (like a cookie cutter)|
| Object | A real instance (like the actual cookie) |

---

## 8. Encapsulation

**Encapsulation** means **hiding internal data** and only exposing it through controlled methods. It protects data from unintended modification.

### Access Specifiers
| Specifier   | Accessible From                      |
|-------------|--------------------------------------|
| `public`    | Anywhere                             |
| `private`   | Only inside the class                |
| `protected` | Inside class and derived classes     |

```cpp
class BankAccount {
private:
    double balance;     // hidden from outside

public:
    void deposit(double amount) {
        if (amount > 0)
            balance += amount;   // controlled access
    }

    double getBalance() {
        return balance;          // read-only access
    }
};

int main() {
    BankAccount acc;
    // acc.balance = -9999;  ❌ ERROR — private!
    acc.deposit(500);          // ✅ controlled
    cout << acc.getBalance();  // 500
}
```

---

## 9. Constructor

A **constructor** is a special method that **runs automatically when an object is created**. It initializes the object's data.

### Rules
- Same name as the class
- No return type (not even `void`)
- Can be overloaded

### Types of Constructors

#### Default Constructor
```cpp
class Student {
public:
    string name;
    int age;

    Student() {                    // default constructor
        name = "Unknown";
        age = 0;
        cout << "Object created!" << endl;
    }
};

Student s1;   // automatically calls constructor
```

#### Parameterized Constructor
```cpp
class Student {
public:
    string name;
    int age;

    Student(string n, int a) {   // parameterized
        name = n;
        age = a;
    }
};

Student s1("Alice", 20);   // passes values at creation
```

#### Copy Constructor
```cpp
class Student {
public:
    string name;

    Student(const Student& other) {   // copy constructor
        name = other.name;
    }
};

Student s1("Alice");
Student s2 = s1;    // copy constructor called
```

#### Constructor Initializer List (Best Practice)
```cpp
class Student {
private:
    string name;
    int age;

public:
    Student(string n, int a) : name(n), age(a) {}  // initializer list
};
```

---

## 10. Destructor

A **destructor** runs automatically when an object is **destroyed** (goes out of scope or `delete` is called). Used to **free resources**.

### Rules
- Same name as class with `~` prefix
- No return type, no parameters
- Only ONE destructor per class

```cpp
class MyClass {
public:
    int* data;

    MyClass(int size) {
        data = new int[size];     // allocate in constructor
        cout << "Constructor called" << endl;
    }

    ~MyClass() {
        delete[] data;            // free in destructor
        cout << "Destructor called" << endl;
    }
};

int main() {
    MyClass obj(10);   // Constructor called
}                      // Destructor called automatically here
```

### With Dynamic Objects
```cpp
MyClass* ptr = new MyClass(10);   // Constructor called
delete ptr;                        // Destructor called
```

---

## 11. Getters & Setters

**Getters** and **Setters** are public methods that read and write private data safely.

```cpp
class Person {
private:
    string name;
    int age;

public:
    // Getter — returns the value
    string getName() { return name; }
    int getAge()     { return age; }

    // Setter — validates then sets the value
    void setName(string n) {
        if (!n.empty())
            name = n;
    }

    void setAge(int a) {
        if (a >= 0 && a <= 150)   // validation!
            age = a;
        else
            cout << "Invalid age!" << endl;
    }
};

int main() {
    Person p;
    p.setName("Alice");
    p.setAge(25);
    p.setAge(-5);          // Invalid age! (rejected)

    cout << p.getName();   // Alice
    cout << p.getAge();    // 25
}
```

---

## 12. Complete Class Example

Putting it all together — a `Student` class using all concepts:

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
private:
    // Encapsulated data
    string name;
    int age;
    int* grades;    // dynamic array
    int numGrades;

public:
    // Parameterized Constructor
    Student(string n, int a, int numG) : name(n), age(a), numGrades(numG) {
        grades = new int[numGrades];   // DMA
        for (int i = 0; i < numGrades; i++)
            grades[i] = 0;
        cout << "Student " << name << " created." << endl;
    }

    // Copy Constructor
    Student(const Student& other) : name(other.name), age(other.age), numGrades(other.numGrades) {
        grades = new int[numGrades];
        for (int i = 0; i < numGrades; i++)
            grades[i] = other.grades[i];
    }

    // Destructor
    ~Student() {
        delete[] grades;
        cout << "Student " << name << " destroyed." << endl;
    }

    // Getters
    string getName()  const { return name; }
    int    getAge()   const { return age; }
    int    getGrade(int i) const {
        if (i >= 0 && i < numGrades) return grades[i];
        return -1;
    }

    // Setters
    void setName(string n) {
        if (!n.empty()) name = n;
    }

    void setAge(int a) {
        if (a > 0 && a < 150) age = a;
    }

    void setGrade(int index, int grade) {
        if (index >= 0 && index < numGrades && grade >= 0 && grade <= 100)
            grades[index] = grade;
    }

    // Method
    void display() const {
        cout << "Name: " << name << ", Age: " << age << endl;
        cout << "Grades: ";
        for (int i = 0; i < numGrades; i++)
            cout << grades[i] << " ";
        cout << endl;
    }
};

int main() {
    // Create object with constructor
    Student s1("Alice", 20, 3);
    s1.setGrade(0, 95);
    s1.setGrade(1, 88);
    s1.setGrade(2, 76);
    s1.display();

    // Smart pointer version
    auto s2 = make_unique<Student>("Bob", 22, 2);
    s2->setGrade(0, 90);
    s2->setGrade(1, 85);
    s2->display();

    // s2 auto-destroyed when unique_ptr goes out of scope

    return 0;
}   // s1 destructor called here
```

### Expected Output
```
Student Alice created.
Name: Alice, Age: 20
Grades: 95 88 76
Student Bob created.
Name: Bob, Age: 22
Grades: 90 85
Student Bob destroyed.
Student Alice destroyed.
```

---

## Quick Reference Summary

| Concept        | Purpose                                      | Key Syntax                    |
|----------------|----------------------------------------------|-------------------------------|
| Pointer        | Store memory address                         | `int* p = &x;`                |
| DMA            | Runtime heap allocation                      | `new` / `delete`              |
| unique_ptr     | Single-owner smart pointer                   | `make_unique<T>()`            |
| shared_ptr     | Multi-owner smart pointer                    | `make_shared<T>()`            |
| weak_ptr       | Non-owning observer                          | `wp.lock()`                   |
| 1D Array       | Linear collection                            | `int arr[5]`                  |
| 2D Array       | Matrix/grid                                  | `int mat[3][3]`               |
| Char Array     | C-style string                               | `char str[] = "hello"`        |
| Class          | Blueprint for objects                        | `class Name { };`             |
| Object         | Instance of a class                          | `ClassName obj;`              |
| Encapsulation  | Hide data, expose via methods                | `private:` + public methods   |
| Constructor    | Auto-runs on object creation                 | `ClassName() { }`             |
| Destructor     | Auto-runs on object destruction              | `~ClassName() { }`            |
| Getter         | Read private data                            | `getX() { return x; }`        |
| Setter         | Write private data with validation           | `setX(val) { x = val; }`      |

---

*Happy Coding in C++!* 🚀