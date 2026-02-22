# 🚪 Getters & Setters in C++
### "The controlled doors into your private data."

---

## 🤔 The Problem — Private Data with No Access

Remember encapsulation? We make data `private` to protect it.
But then how do we **read or update** that data from outside the class?

```cpp
class Person {
private:
    string name;   // hidden!
    int age;       // hidden!
};

int main() {
    Person p;
    // p.name = "Alice";  ← ❌ ERROR! name is private
    // cout << p.age;     ← ❌ ERROR! age is private

    // We can't access ANYTHING! Useless class...
}
```

**The solution: Getters and Setters!**

---

## 💡 What are Getters and Setters?

**Getter** = A public function that **reads** (gets) a private variable
**Setter** = A public function that **writes** (sets) a private variable

They act like **controlled doorways** into your private data:

```
OUTSIDE WORLD             CLASS INTERNALS
                         ┌─────────────────┐
                         │  private:       │
getName() ──GET──────────►│    name         │
setName() ──SET──────────►│                 │
getAge()  ──GET──────────►│  private:       │
setAge()  ──SET──────────►│    age          │
                         └─────────────────┘
     ↑                          ↑
  Anyone can                 Hidden —
  call these                 no direct
  functions                  access!
```

---

## 📝 Basic Getter and Setter Syntax

```cpp
class Person {
private:
    string name;   // private data
    int age;       // private data

public:
    // GETTER for name — returns the value (read-only access)
    string getName() {
        return name;
    }

    // SETTER for name — sets the value (write access)
    void setName(string n) {
        name = n;
    }

    // GETTER for age
    int getAge() {
        return age;
    }

    // SETTER for age
    void setAge(int a) {
        age = a;
    }
};

int main() {
    Person p;

    // Use setters to write
    p.setName("Alice");
    p.setAge(25);

    // Use getters to read
    cout << p.getName() << endl;   // Alice
    cout << p.getAge()  << endl;   // 25
}
```

---

## 🔑 Naming Convention

The standard C++ naming convention for getters and setters:

| Variable  | Getter           | Setter              |
|-----------|------------------|---------------------|
| `name`    | `getName()`      | `setName(string n)` |
| `age`     | `getAge()`       | `setAge(int a)`     |
| `salary`  | `getSalary()`    | `setSalary(double s)` |
| `active`  | `isActive()` ← (for booleans, use `is`) | `setActive(bool a)` |

---

## 🛡️ The REAL Power — Validation in Setters

The most important reason to use setters is **data validation** — rejecting invalid values.

```cpp
class Student {
private:
    string name;
    int age;
    double gpa;

public:
    // Setter with validation — protects the data!
    void setAge(int a) {
        if (a < 5 || a > 100) {
            cout << "Error: Invalid age '" << a << "'! Not set." << endl;
            return;   // reject — don't change the value
        }
        age = a;   // only set if valid
    }

    void setGpa(double g) {
        if (g < 0.0 || g > 4.0) {
            cout << "Error: GPA must be 0.0–4.0! Not set." << endl;
            return;
        }
        gpa = g;
    }

    void setName(string n) {
        if (n.empty()) {
            cout << "Error: Name cannot be empty! Not set." << endl;
            return;
        }
        name = n;
    }

    int    getAge()  { return age; }
    double getGpa()  { return gpa; }
    string getName() { return name; }
};

int main() {
    Student s;

    s.setAge(20);       // ✅ Valid
    s.setAge(-5);       // ❌ Rejected: "Invalid age '-5'! Not set."
    s.setAge(200);      // ❌ Rejected: "Invalid age '200'! Not set."

    s.setGpa(3.5);      // ✅ Valid
    s.setGpa(5.0);      // ❌ Rejected: "GPA must be 0.0–4.0!"
    s.setGpa(-1.0);     // ❌ Rejected

    s.setName("Alice"); // ✅ Valid
    s.setName("");      // ❌ Rejected: "Name cannot be empty!"

    cout << "Age: " << s.getAge() << endl;    // 20  (invalid ones were rejected)
    cout << "GPA: " << s.getGpa() << endl;    // 3.5
    cout << "Name: " << s.getName() << endl;  // Alice
}
```

---

## 👁️ Read-Only Property — Getter Without Setter

Sometimes you want data that can be READ but NOT CHANGED from outside.

```cpp
class BankAccount {
private:
    string accountNumber;
    double balance;
    int transactionCount;

public:
    BankAccount(string accNum) : accountNumber(accNum), balance(0.0), transactionCount(0) {}

    // Read-only — no setter! Account number should never change.
    string getAccountNumber() { return accountNumber; }

    // Read-only — balance is changed only through deposit/withdraw
    double getBalance() { return balance; }

    // Read-only — count increases automatically
    int getTransactionCount() { return transactionCount; }

    // Only these functions can modify balance
    void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            transactionCount++;
        }
    }

    void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            transactionCount++;
        }
    }
};

int main() {
    BankAccount acc("ACC-001");
    acc.deposit(500.0);
    acc.deposit(250.0);
    acc.withdraw(100.0);

    cout << "Account: " << acc.getAccountNumber() << endl;  // ACC-001
    cout << "Balance: $" << acc.getBalance() << endl;       // 650
    cout << "Transactions: " << acc.getTransactionCount() << endl;  // 3

    // acc.balance = 99999;        ← ❌ Can't! Private.
    // acc.accountNumber = "HACK"; ← ❌ Can't! No setter exists!
}
```

---

## ✏️ Write-Only Property — Setter Without Getter

Rare, but sometimes you want to SET data that you never expose (like passwords).

```cpp
class User {
private:
    string username;
    string password;   // never returned — security!

public:
    void setUsername(string u) { username = u; }

    void setPassword(string p) {
        if (p.length() < 8) {
            cout << "Error: Password too short! Must be 8+ characters." << endl;
            return;
        }
        password = p;   // stored internally, but never returned!
    }

    string getUsername() { return username; }
    // NO getPassword()! That would be a security risk!

    bool checkPassword(string attempt) {
        return password == attempt;   // compare internally — never expose
    }
};

int main() {
    User u;
    u.setUsername("alice");
    u.setPassword("abc");           // Error: too short
    u.setPassword("mySecret123");   // ✅ Valid

    cout << u.getUsername() << endl;      // alice
    // cout << u.getPassword();           ← ❌ No getter for password!

    cout << u.checkPassword("wrong") << endl;        // 0 (false)
    cout << u.checkPassword("mySecret123") << endl;  // 1 (true)
}
```

---

## 🎯 `const` Getters — Best Practice

Mark getters as `const` — it means they promise NOT to modify the object.

```cpp
class Circle {
private:
    double radius;

public:
    Circle(double r) : radius(r) {}

    // const after the () means "I won't change the object"
    double getRadius() const { return radius; }
    double getArea()   const { return 3.14159 * radius * radius; }
    double getPerimeter() const { return 2 * 3.14159 * radius; }

    void setRadius(double r) {
        if (r > 0) radius = r;
    }
};

int main() {
    const Circle c(5.0);   // const object — can only call const functions
    cout << c.getRadius();   // ✅ Works (const function)
    cout << c.getArea();     // ✅ Works (const function)
    // c.setRadius(10);      ← ❌ Can't call non-const function on const object!
}
```

---

## 🧪 Complete Working Example — Employee Management

```cpp
#include <iostream>
#include <string>
using namespace std;

class Employee {
private:
    string name;
    int employeeId;
    double salary;
    string department;
    int yearsOfService;

public:
    // Constructor
    Employee(string n, int id, double sal, string dept)
        : name(n), employeeId(id), salary(sal),
          department(dept), yearsOfService(0) {
        cout << "Employee " << name << " hired!" << endl;
    }

    // ── GETTERS ──────────────────────────────────────────
    string getName()       const { return name; }
    int    getEmployeeId() const { return employeeId; }
    double getSalary()     const { return salary; }
    string getDepartment() const { return department; }
    int    getYears()      const { return yearsOfService; }

    // Computed properties (no direct data, calculated)
    double getAnnualSalary() const { return salary * 12; }
    string getLevel() const {
        if (yearsOfService < 2)   return "Junior";
        if (yearsOfService < 5)   return "Mid-level";
        if (yearsOfService < 10)  return "Senior";
        return "Expert";
    }

    // ── SETTERS ──────────────────────────────────────────
    void setName(string n) {
        if (n.empty()) {
            cout << "Error: Name cannot be empty." << endl;
            return;
        }
        name = n;
    }

    void setSalary(double s) {
        if (s < 0) {
            cout << "Error: Salary cannot be negative." << endl;
            return;
        }
        if (s < salary)
            cout << "Warning: Salary decrease!" << endl;
        salary = s;
    }

    void setDepartment(string d) {
        if (d.empty()) {
            cout << "Error: Department cannot be empty." << endl;
            return;
        }
        department = d;
    }

    void incrementYear() {
        yearsOfService++;
    }

    // No setter for employeeId — it should never change!
    // getId() exists but setId() does NOT.

    // ── DISPLAY ──────────────────────────────────────────
    void display() const {
        cout << "─────────────────────────────" << endl;
        cout << "Name:       " << name << endl;
        cout << "ID:         " << employeeId << endl;
        cout << "Department: " << department << endl;
        cout << "Salary:     $" << salary << "/month" << endl;
        cout << "Annual:     $" << getAnnualSalary() << endl;
        cout << "Years:      " << yearsOfService << endl;
        cout << "Level:      " << getLevel() << endl;
        cout << "─────────────────────────────" << endl;
    }
};

int main() {
    Employee emp("Alice Ahmed", 1001, 3500.0, "Engineering");
    emp.display();

    // Give a raise
    cout << "\nGiving Alice a raise..." << endl;
    emp.setSalary(4200.0);

    // Transfer department
    emp.setDepartment("Product");

    // Years pass
    emp.incrementYear();
    emp.incrementYear();
    emp.incrementYear();

    emp.display();

    // Try invalid operations
    cout << "\nTrying invalid operations:" << endl;
    emp.setSalary(-1000);   // Error
    emp.setName("");         // Error
    emp.setDepartment("");   // Error

    cout << "\nSalary unchanged: $" << emp.getSalary() << endl;

    return 0;
}
```

**Output:**
```
Employee Alice Ahmed hired!
─────────────────────────────
Name:       Alice Ahmed
ID:         1001
Department: Engineering
Salary:     $3500/month
Annual:     $42000
Years:      0
Level:      Junior
─────────────────────────────

Giving Alice a raise...
─────────────────────────────
Name:       Alice Ahmed
ID:         1001
Department: Product
Salary:     $4200/month
Annual:     $50400
Years:      3
Level:      Mid-level
─────────────────────────────

Trying invalid operations:
Error: Salary cannot be negative.
Error: Name cannot be empty.
Error: Department cannot be empty.

Salary unchanged: $4200
```

---

## 📊 Getter & Setter Summary

| Pattern          | Getter | Setter | Example Use Case                   |
|------------------|--------|--------|------------------------------------|
| Read & Write     | ✅     | ✅     | Name, email, department            |
| Read-Only        | ✅     | ❌     | ID, creation date, calculated area |
| Write-Only       | ❌     | ✅     | Passwords (security)               |
| Computed (no var)| ✅     | ❌     | Annual salary, grade letter        |

---

## 🎯 Key Takeaways

1. **Getter** = public function to **read** private data — `getX()`
2. **Setter** = public function to **write** private data — `setX(val)`
3. Setters can **validate** data before storing it (reject invalid values)
4. **Read-only**: provide getter but NO setter
5. **Write-only**: provide setter but NO getter (for sensitive data like passwords)
6. **Computed properties**: getter that calculates and returns a value (no stored variable needed)
7. Mark getters as **`const`** — they promise not to modify the object
8. Together with encapsulation, getters/setters give you **full control over your data**

---
*Next up: The big picture — putting it ALL together in one complete example!* →
