# 🏗️ Classes & Objects in C++
### "Create your own custom data types — model anything from the real world."

---

## 🌍 The Real-World Problem

Imagine you're making a program to manage a library. You need to store info about books.

**Without classes:**
```cpp
string book1Title  = "Harry Potter";
string book1Author = "J.K. Rowling";
int    book1Pages  = 450;
double book1Price  = 25.99;

string book2Title  = "C++ Programming";
string book2Author = "Bjarne Stroustrup";
int    book2Pages  = 900;
double book2Price  = 59.99;
```

This is a mess! Imagine 1000 books. And you can't easily add functions that operate on books.

**With classes:**
```cpp
Book book1("Harry Potter", "J.K. Rowling", 450, 25.99);
Book book2("C++ Programming", "Bjarne Stroustrup", 900, 59.99);
book1.display();   // everything neatly packaged!
```

---

## 🤔 What is a Class?

A **class** is a **blueprint** (a template) that defines:
1. **Data** (what properties does it have?)
2. **Functions** (what can it do?)

Think of a class like a **cookie cutter** and objects like the **actual cookies**.

```
CLASS (Cookie Cutter):
┌──────────────────────────────┐
│          Book                │
│  ─────────────────────────   │
│  DATA:                       │
│    title, author, pages,     │
│    price                     │
│  ─────────────────────────   │
│  FUNCTIONS:                  │
│    display(), getPrice(),    │
│    applyDiscount()           │
└──────────────────────────────┘
         ↓ creates
OBJECTS (Actual Cookies):
 book1: "Harry Potter", Rowling, 450, 25.99
 book2: "C++ Primer", Lippman, 900, 59.99
 book3: "Clean Code", Martin, 431, 39.99
```

---

## 📝 Defining a Class

```cpp
class ClassName {
    // data and functions go here
};   // ← Don't forget the semicolon!
```

A simple example:

```cpp
class Dog {
public:
    // Data (also called: attributes, fields, member variables)
    string name;
    string breed;
    int age;

    // Functions (also called: methods, member functions)
    void bark() {
        cout << name << " says: Woof!" << endl;
    }

    void displayInfo() {
        cout << "Name: " << name << endl;
        cout << "Breed: " << breed << endl;
        cout << "Age: " << age << " years" << endl;
    }
};
```

---

## 🏠 What is an Object?

An **object** is a specific **instance** (a real example) created FROM a class.

If a class is the blueprint for a house, an object is an actual house built from that blueprint.

```cpp
// Creating objects
Dog dog1;   // dog1 is an object of type Dog
Dog dog2;   // dog2 is another, separate object

// Setting data
dog1.name  = "Max";
dog1.breed = "Labrador";
dog1.age   = 3;

dog2.name  = "Bella";
dog2.breed = "Poodle";
dog2.age   = 5;

// Calling functions
dog1.bark();         // Max says: Woof!
dog2.bark();         // Bella says: Woof!

dog1.displayInfo();  // shows Max's info
dog2.displayInfo();  // shows Bella's info
```

**Each object has its OWN copy of the data**, but shares the same functions:

```
dog1:                    dog2:
┌──────────────┐         ┌──────────────┐
│ name: "Max"  │         │ name: "Bella"│
│ breed: "Lab" │         │ breed: "Poo" │
│ age: 3       │         │ age: 5       │
└──────────────┘         └──────────────┘
      ↕                        ↕
      Both use the same bark() and displayInfo() functions
```

---

## 🔑 The Dot Operator `.`

To access an object's data or call its functions, use the **dot operator**:

```cpp
objectName.dataMember     // access data
objectName.functionName() // call function
```

```cpp
Dog dog1;
dog1.name = "Max";         // access data member
dog1.bark();               // call member function
cout << dog1.age;          // read data member
```

---

## 🎯 Complete Example — Bank Account

```cpp
#include <iostream>
#include <string>
using namespace std;

class BankAccount {
public:
    string owner;
    string accountNumber;
    double balance;

    void deposit(double amount) {
        balance += amount;
        cout << "Deposited: $" << amount << endl;
        cout << "New Balance: $" << balance << endl;
    }

    void withdraw(double amount) {
        if (amount > balance) {
            cout << "Insufficient funds!" << endl;
        } else {
            balance -= amount;
            cout << "Withdrawn: $" << amount << endl;
            cout << "New Balance: $" << balance << endl;
        }
    }

    void displayInfo() {
        cout << "================================" << endl;
        cout << "Owner: " << owner << endl;
        cout << "Account: " << accountNumber << endl;
        cout << "Balance: $" << balance << endl;
        cout << "================================" << endl;
    }
};

int main() {
    // Create two bank accounts (two objects)
    BankAccount acc1;
    acc1.owner = "Alice";
    acc1.accountNumber = "ACC-001";
    acc1.balance = 1000.0;

    BankAccount acc2;
    acc2.owner = "Bob";
    acc2.accountNumber = "ACC-002";
    acc2.balance = 500.0;

    // Use them
    acc1.displayInfo();
    acc1.deposit(250.0);
    acc1.withdraw(100.0);
    acc1.withdraw(2000.0);   // Should fail!

    cout << endl;

    acc2.displayInfo();
    acc2.deposit(300.0);

    return 0;
}
```

**Output:**
```
================================
Owner: Alice
Account: ACC-001
Balance: $1000
================================
Deposited: $250
New Balance: $1250
Withdrawn: $100
New Balance: $1150
Insufficient funds!

================================
Owner: Bob
Account: ACC-002
Balance: $500
================================
Deposited: $300
New Balance: $800
```

---

## 🏛️ Structure of a Class

```cpp
class ClassName {
// Access specifiers (public/private/protected) control who can access what

public:         // ← accessible from ANYWHERE
    // public data and functions

private:        // ← accessible ONLY from inside this class
    // private data and functions

protected:      // ← accessible from inside class AND derived classes
    // protected data and functions
};
```

We'll learn more about `private` and `public` in the **Encapsulation** chapter.

---

## 🔃 Object Arrays — Multiple Objects at Once

```cpp
Dog dogs[3];   // array of 3 Dog objects

dogs[0].name = "Max";    dogs[0].age = 3;
dogs[1].name = "Bella";  dogs[1].age = 5;
dogs[2].name = "Rocky";  dogs[2].age = 2;

for (int i = 0; i < 3; i++) {
    cout << dogs[i].name << " is " << dogs[i].age << " years old." << endl;
}
```

---

## 📌 Pointer to Object

```cpp
Dog dog1;
dog1.name = "Max";

Dog* ptr = &dog1;       // pointer to an object

// Two ways to access via pointer:
cout << (*ptr).name;   // dereference then dot
cout << ptr->name;     // arrow operator (shorthand — much more common!)

ptr->bark();           // call function via pointer
```

> The **arrow operator** `->` is used with pointers to objects.
> The **dot operator** `.` is used with regular objects.

---

## 🗑️ Dynamic Object Creation

```cpp
Dog* dog = new Dog();    // create object on heap
dog->name = "Max";
dog->bark();

delete dog;              // free memory when done
dog = nullptr;
```

---

## 📊 Class vs Object — Summary

| Concept  | Analogy                  | C++ Example                 |
|----------|--------------------------|------------------------------|
| Class    | Recipe / Blueprint       | `class Dog { ... };`        |
| Object   | Actual dish / Building   | `Dog myDog;`                |
| Data     | Ingredients / Properties | `name`, `age`, `breed`      |
| Function | Steps / Actions          | `bark()`, `displayInfo()`   |

---

## 🎯 Key Takeaways

1. A **class** is a blueprint — it defines what data and functions exist
2. An **object** is a real instance created from a class
3. Each object has its **own copy of data** but shares functions
4. Use the **dot operator** `.` to access members of an object
5. Use the **arrow operator** `->` to access members via a pointer
6. You can create **many objects** from one class
7. Classes let you **model real-world things** in code

---
*Next up: Encapsulation — hiding your data and protecting it from misuse!* →
