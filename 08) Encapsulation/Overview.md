# 🔒 Encapsulation in C++
### "Lock your data away — only let controlled access in."

---

## 🤔 The Problem — Data Without Protection

Imagine you have a BankAccount class where the balance is publicly accessible:

```cpp
class BankAccount {
public:
    string owner;
    double balance;   // ← Anyone can touch this!
};

int main() {
    BankAccount acc;
    acc.owner = "Alice";
    acc.balance = 1000.0;

    // Anyone can do this — no rules!
    acc.balance = -999999;   // ← Set to negative? That makes no sense!
    acc.balance = 1000000;   // ← Just give yourself a million?
}
```

There's no protection! Someone could set the balance to anything — even nonsense values.

**Encapsulation solves this by hiding data and controlling access through functions.**

---

## 💡 What is Encapsulation?

**Encapsulation** means:
1. **Hiding** the internal data of a class (making it `private`)
2. **Exposing** controlled access through `public` functions

It's like a **medicine capsule**:
- The capsule's shell (the class interface) is what you interact with
- The medicine inside (the data) is hidden and protected
- You can't just reach inside — you interact through the defined interface

```
Without Encapsulation:         With Encapsulation:
┌────────────────┐             ┌────────────────┐
│  BankAccount   │             │  BankAccount   │
│                │             │                │
│  balance ◄────────────      │ ┌────────────┐ │
│  (public, anyone    │ ANY   │ │  balance   │ │ ← hidden!
│   can change it)    │ CODE  │ │  (private) │ │
│                │    │       │ └────────────┘ │
└────────────────┘    │       │  deposit()  ◄──────── controlled
                       │       │  withdraw() ◄──────── access
                               │  getBalance()◄──────── only
                               └────────────────┘
```

---

## 🔑 Access Specifiers

C++ has 3 access specifiers that control who can access what:

### `public` — Open to Everyone
```cpp
class Dog {
public:
    string name;   // anyone can read or change this
    void bark() { cout << "Woof!"; }   // anyone can call this
};

Dog d;
d.name = "Max";   // ✅ Fine — it's public
d.bark();          // ✅ Fine — it's public
```

### `private` — Only the Class Itself
```cpp
class Dog {
private:
    int age;   // ONLY functions inside the Dog class can touch this

public:
    void setAge(int a) { age = a; }   // ✅ This is inside Dog — can access
    int getAge() { return age; }       // ✅ Also inside Dog — can access
};

Dog d;
// d.age = 5;   ← ❌ ERROR! age is private!
d.setAge(5);    // ✅ Use the public function instead
```

### `protected` — Class and Its Children
Used with inheritance (advanced topic). Like `private` but also accessible by derived classes.

---

## 🏛️ The Standard Pattern — Private Data + Public Functions

The most common encapsulation pattern:

```cpp
class Person {
private:
    // DATA is hidden (private)
    string name;
    int age;
    double salary;

public:
    // FUNCTIONS are the interface (public)
    void setName(string n)   { name = n; }
    void setAge(int a)       { if (a > 0) age = a; }   // validation!
    void setSalary(double s) { if (s >= 0) salary = s; }

    string getName()   { return name; }
    int    getAge()    { return age; }
    double getSalary() { return salary; }

    void display() {
        cout << name << ", Age: " << age << ", Salary: $" << salary << endl;
    }
};
```

---

## ✅ Why is Encapsulation Useful?

### 1. Data Validation — Reject Bad Values

```cpp
class Temperature {
private:
    double celsius;

public:
    void setTemperature(double temp) {
        if (temp < -273.15) {   // Can't go below absolute zero!
            cout << "Error: Temperature below absolute zero!" << endl;
        } else {
            celsius = temp;   // only set if valid
        }
    }

    double getTemperature() { return celsius; }
};

Temperature t;
t.setTemperature(25.0);     // ✅ Set to 25°C
t.setTemperature(-500.0);   // ❌ Rejected! Invalid value
```

### 2. Read-Only Data — Allow Reading but Not Writing

```cpp
class Circle {
private:
    double radius;

public:
    Circle(double r) : radius(r) {}

    // Can READ radius
    double getRadius() { return radius; }

    // Computed property — user can't mess with it
    double getArea() { return 3.14159 * radius * radius; }

    // NO setArea() function — you can't directly set area
    // (it's determined by radius)
};

Circle c(5.0);
cout << c.getRadius();  // 5
cout << c.getArea();    // 78.5398
// c.radius = 10;       // ❌ Can't! Private.
// c.area = 100;        // ❌ There's no such data member
```

### 3. Internal Implementation Can Change Without Breaking Code

```cpp
// Old implementation
class DataStore {
private:
    int data[100];   // ← using array

public:
    void add(int val) { /* add to array */ }
    int get(int i) { return data[i]; }
};

// New implementation — changed internally to vector
class DataStore {
private:
    vector<int> data;   // ← changed to vector

public:
    void add(int val) { data.push_back(val); }   // works the same!
    int get(int i) { return data[i]; }            // works the same!
};

// Code that USES DataStore doesn't need to change!
DataStore ds;
ds.add(10);       // still works
ds.get(0);        // still works
```

---

## 🧪 Complete Example — Student Class

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
private:
    // All data is hidden
    string name;
    int studentId;
    double gpa;
    bool enrolled;

public:
    // Setters (with validation)
    void setName(string n) {
        if (n.empty()) {
            cout << "Error: Name cannot be empty!" << endl;
            return;
        }
        name = n;
    }

    void setStudentId(int id) {
        if (id <= 0) {
            cout << "Error: Invalid student ID!" << endl;
            return;
        }
        studentId = id;
    }

    void setGpa(double g) {
        if (g < 0.0 || g > 4.0) {
            cout << "Error: GPA must be between 0.0 and 4.0!" << endl;
            return;
        }
        gpa = g;
    }

    void enroll()   { enrolled = true; }
    void unenroll() { enrolled = false; }

    // Getters (read-only access)
    string getName()    { return name; }
    int    getId()      { return studentId; }
    double getGpa()     { return gpa; }
    bool   isEnrolled() { return enrolled; }

    // Computed property
    string getGrade() {
        if (gpa >= 3.7) return "A";
        if (gpa >= 3.3) return "A-";
        if (gpa >= 3.0) return "B+";
        if (gpa >= 2.7) return "B";
        return "C or below";
    }

    // Display info
    void display() {
        cout << "==============================" << endl;
        cout << "Name:     " << name << endl;
        cout << "ID:       " << studentId << endl;
        cout << "GPA:      " << gpa << endl;
        cout << "Grade:    " << getGrade() << endl;
        cout << "Status:   " << (enrolled ? "Enrolled" : "Not Enrolled") << endl;
        cout << "==============================" << endl;
    }
};

int main() {
    Student s;

    // Valid data
    s.setName("Alice Ahmed");
    s.setStudentId(12345);
    s.setGpa(3.8);
    s.enroll();
    s.display();

    // Try invalid data
    s.setGpa(5.0);     // Error: GPA must be between 0.0 and 4.0!
    s.setStudentId(-1); // Error: Invalid student ID!
    s.setName("");      // Error: Name cannot be empty!

    // Data unchanged after failed sets
    cout << "GPA is still: " << s.getGpa() << endl;   // 3.8

    return 0;
}
```

**Output:**
```
==============================
Name:     Alice Ahmed
ID:       12345
GPA:      3.8
Grade:    A
Status:   Enrolled
==============================
Error: GPA must be between 0.0 and 4.0!
Error: Invalid student ID!
Error: Name cannot be empty!
GPA is still: 3.8
```

---

## 🏥 Real-World Analogy: Hospital Patient System

```
PUBLIC (what staff can interact with):
✅ Record patient name
✅ Take vitals (blood pressure, temperature)
✅ View test results
✅ Prescribe medication

PRIVATE (hidden internal workings):
🔒 Internal ID numbers
🔒 Raw database records
🔒 Encryption keys
🔒 Billing algorithms

The INTERFACE (public functions) validates and controls access to the PRIVATE data.
A nurse can't just edit the raw database — they go through controlled forms.
```

---

## 📊 Access Specifier Summary

| Specifier   | Accessible From                      | Typical Use                        |
|-------------|--------------------------------------|------------------------------------|
| `public`    | Anywhere (inside & outside class)    | Functions that form the interface  |
| `private`   | Only inside the class                | Data members, helper functions     |
| `protected` | Inside class + derived classes       | Inheritance scenarios              |

---

## 📋 Encapsulation Golden Rules

1. **Make data `private`** — never expose raw data
2. **Make functions `public`** — these are the interface
3. **Validate in setters** — reject invalid values
4. **Use getters for read access** — return copies, not references to private data
5. **Keep the interface stable** — even if internal implementation changes

---

## 🎯 Key Takeaways

1. **Encapsulation = data hiding + controlled access**
2. Use `private` for data, `public` for functions
3. Access private data ONLY through public getter/setter functions
4. Setters can **validate** data before storing it
5. Users of the class interact with the **interface** (public functions), not the data directly
6. This protects data from corruption and makes code easier to maintain
7. Internal implementation can change without breaking the outside world

---
*Next up: Constructors — automatically initializing objects when they're created!* →
