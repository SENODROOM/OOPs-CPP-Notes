# 🏗️ Constructors in C++
### "The birth ceremony — automatically runs when an object comes to life."

---

## 🤔 The Problem — Uninitialized Objects

When you create an object without a constructor, its data contains **garbage values**:

```cpp
class Student {
public:
    string name;
    int age;
    double gpa;
};

int main() {
    Student s;
    cout << s.age;   // Garbage! Could be -12345 or 0 or anything
    cout << s.gpa;   // Garbage!
}
```

You'd have to manually initialize every object:
```cpp
Student s;
s.name = "Unknown";
s.age = 0;
s.gpa = 0.0;
// Tedious and easy to forget!
```

**Constructors solve this by automatically initializing objects the moment they're created.**

---

## 💡 What is a Constructor?

A **constructor** is a special function that:
1. Has the **same name** as the class
2. Has **no return type** (not even `void`)
3. Runs **automatically** when an object is created
4. Can take parameters to initialize the object with specific values

```cpp
class Student {
public:
    string name;
    int age;

    // This is a constructor!
    Student() {
        name = "Unknown";
        age = 0;
        cout << "A Student object was just created!" << endl;
    }
};

int main() {
    Student s;   // Constructor runs AUTOMATICALLY here!
    // Output: "A Student object was just created!"
    cout << s.name;   // "Unknown" — already initialized!
}
```

---

## 🏠 Real-Life Analogy

> A constructor is like a **move-in checklist** for a new apartment.
> The moment you move in (create the object), the checklist automatically runs:
> - Set up electricity ✓
> - Connect water ✓
> - Set up internet ✓
>
> You don't have to remember to do these manually — they happen automatically when you "move in."

---

## 📚 Types of Constructors

---

### 1️⃣ Default Constructor — No Parameters

Takes no arguments. Used to give default values.

```cpp
class Car {
public:
    string brand;
    int speed;
    bool isRunning;

    // Default Constructor
    Car() {
        brand = "Unknown";
        speed = 0;
        isRunning = false;
        cout << "Car object created with defaults!" << endl;
    }
};

int main() {
    Car c1;   // Default constructor called automatically
    Car c2;   // Called again for c2

    cout << c1.brand;      // Unknown
    cout << c2.isRunning;  // 0 (false)
}
```

**Output:**
```
Car object created with defaults!
Car object created with defaults!
Unknown
0
```

---

### 2️⃣ Parameterized Constructor — Takes Arguments

Lets you initialize the object with specific values at creation time.

```cpp
class Car {
public:
    string brand;
    int speed;

    // Parameterized Constructor
    Car(string b, int s) {
        brand = b;
        speed = s;
        cout << brand << " created with speed " << speed << endl;
    }
};

int main() {
    Car c1("Toyota", 120);   // passes values to constructor
    Car c2("BMW", 200);      // different values for c2
    Car c3("Honda", 150);

    cout << c1.brand;   // Toyota
    cout << c2.speed;   // 200
}
```

**Output:**
```
Toyota created with speed 120
BMW created with speed 200
Honda created with speed 150
Toyota
200
```

---

### 3️⃣ Constructor Overloading — Multiple Constructors

You can have MORE THAN ONE constructor — with different parameters!
C++ picks the right one based on what you pass.

```cpp
class Rectangle {
public:
    double width;
    double height;

    // Constructor 1: No arguments — square with side 1
    Rectangle() {
        width = 1.0;
        height = 1.0;
        cout << "Default 1x1 rectangle created" << endl;
    }

    // Constructor 2: One argument — a square
    Rectangle(double side) {
        width = side;
        height = side;
        cout << "Square " << side << "x" << side << " created" << endl;
    }

    // Constructor 3: Two arguments — a rectangle
    Rectangle(double w, double h) {
        width = w;
        height = h;
        cout << "Rectangle " << w << "x" << h << " created" << endl;
    }

    double area() { return width * height; }
};

int main() {
    Rectangle r1;           // Uses Constructor 1: 1x1
    Rectangle r2(5.0);      // Uses Constructor 2: 5x5 square
    Rectangle r3(4.0, 7.0); // Uses Constructor 3: 4x7 rectangle

    cout << "r1 area: " << r1.area() << endl;   // 1
    cout << "r2 area: " << r2.area() << endl;   // 25
    cout << "r3 area: " << r3.area() << endl;   // 28
}
```

---

### 4️⃣ Copy Constructor — Create from Another Object

Creates a new object as a **copy** of an existing one.

```cpp
class Student {
public:
    string name;
    int age;

    // Regular constructor
    Student(string n, int a) {
        name = n;
        age = a;
    }

    // Copy Constructor — takes a REFERENCE to another Student
    Student(const Student& other) {
        name = other.name;
        age = other.age;
        cout << "Copy of " << name << " created!" << endl;
    }
};

int main() {
    Student s1("Alice", 20);   // Regular constructor

    Student s2 = s1;           // Copy constructor called!
    Student s3(s1);            // Also copy constructor!

    cout << s2.name;   // Alice (copy of s1)
    cout << s3.age;    // 20   (copy of s1)

    // Changing s2 does NOT affect s1
    s2.name = "Bob";
    cout << s1.name;   // Still "Alice"
    cout << s2.name;   // "Bob"
}
```

---

### 5️⃣ Constructor Initializer List — The Best Practice ✅

Instead of assigning values inside `{}`, you can initialize them in a special list.
This is more efficient and is the **preferred modern style**.

```cpp
class Point {
private:
    double x;
    double y;

public:
    // Old way (assignment in body)
    Point(double xVal, double yVal) {
        x = xVal;
        y = yVal;
    }

    // ✅ Better way — Initializer List (after the : )
    Point(double xVal, double yVal) : x(xVal), y(yVal) {
        // body is now empty — initialization done above
    }

    void display() {
        cout << "(" << x << ", " << y << ")" << endl;
    }
};

int main() {
    Point p1(3.0, 4.0);
    p1.display();   // (3, 4)
}
```

**For multiple members:**
```cpp
class Student {
private:
    string name;
    int age;
    double gpa;

public:
    // Initialize all members in the list
    Student(string n, int a, double g) : name(n), age(a), gpa(g) {
        cout << "Student created: " << name << endl;
    }
};
```

> **Why use initializer list?**
> - More efficient (direct initialization, no extra assignment)
> - Required for `const` members and reference members
> - Considered best practice in modern C++

---

## 🧪 Complete Working Example

```cpp
#include <iostream>
#include <string>
using namespace std;

class BankAccount {
private:
    string owner;
    string accountNumber;
    double balance;
    bool active;

public:
    // Default constructor
    BankAccount() : owner("Anonymous"), accountNumber("000-000"), balance(0.0), active(true) {
        cout << "New empty account created." << endl;
    }

    // Parameterized constructor
    BankAccount(string name, string accNum, double initialBalance)
        : owner(name), accountNumber(accNum), active(true) {
        if (initialBalance >= 0)
            balance = initialBalance;
        else {
            balance = 0;
            cout << "Warning: Initial balance can't be negative. Set to 0." << endl;
        }
        cout << "Account created for " << owner << endl;
    }

    // Copy constructor
    BankAccount(const BankAccount& other)
        : owner(other.owner + " (copy)"),
          accountNumber(other.accountNumber + "-C"),
          balance(other.balance),
          active(other.active) {
        cout << "Account copied!" << endl;
    }

    void displayInfo() {
        cout << "Owner: "   << owner << endl;
        cout << "Account: " << accountNumber << endl;
        cout << "Balance: $" << balance << endl;
        cout << "Active: "  << (active ? "Yes" : "No") << endl;
        cout << "-------------------" << endl;
    }
};

int main() {
    cout << "=== Creating Accounts ===" << endl;

    BankAccount acc1;                              // default
    BankAccount acc2("Alice", "ACC-001", 1000.0); // parameterized
    BankAccount acc3("Bob",   "ACC-002", -500.0); // invalid balance
    BankAccount acc4 = acc2;                       // copy

    cout << "\n=== Account Details ===" << endl;
    acc1.displayInfo();
    acc2.displayInfo();
    acc3.displayInfo();
    acc4.displayInfo();

    return 0;
}
```

**Output:**
```
=== Creating Accounts ===
New empty account created.
Account created for Alice
Warning: Initial balance can't be negative. Set to 0.
Account created for Bob
Account copied!

=== Account Details ===
Owner: Anonymous
Account: 000-000
Balance: $0
Active: Yes
-------------------
Owner: Alice
Account: ACC-001
Balance: $1000
Active: Yes
-------------------
Owner: Bob
Account: ACC-002
Balance: $0
Active: Yes
-------------------
Owner: Alice (copy)
Account: ACC-001-C
Balance: $1000
Active: Yes
-------------------
```

---

## ⚠️ Important Rules

```cpp
// ❌ Constructor cannot have a return type
void Student() { }    // WRONG — this is not a constructor!
Student() { }         // ✅ Correct — no return type

// ❌ Constructor name must match class name exactly
class Dog {
    dog() { }    // WRONG — lowercase 'd'
    Dog() { }    // ✅ Correct
};

// If you define ANY constructor, C++ won't auto-generate a default one
class Cat {
public:
    Cat(string name) { }   // Only parameterized — default Cat() is GONE!
};
Cat c;           // ❌ ERROR — no default constructor!
Cat c("Whiskers"); // ✅ OK
```

---

## 📊 Constructor Types at a Glance

| Type            | Syntax                              | When to Use                    |
|-----------------|-------------------------------------|--------------------------------|
| Default         | `MyClass() { }`                     | Default initialization         |
| Parameterized   | `MyClass(int x) { }`               | Initialize with specific values|
| Copy            | `MyClass(const MyClass& o) { }`    | Create from existing object    |
| Initializer List| `MyClass(int x) : data(x) { }`    | Best practice, always prefer   |

---

## 🎯 Key Takeaways

1. A constructor **automatically runs** when an object is created
2. Same name as class, **no return type** (not even void)
3. **Default constructor** = no parameters, sets default values
4. **Parameterized constructor** = takes arguments to customize initialization
5. **Copy constructor** = creates a new object from an existing one
6. **Initializer list** (`: data(val)`) is the preferred modern style
7. You can have **multiple constructors** with different parameters (overloading)
8. If you define any constructor, the **default one disappears** unless you also write it

---
*Next up: Destructors — the farewell ceremony when objects are destroyed!* →
