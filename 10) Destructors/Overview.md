# 💥 Destructors in C++
### "The farewell ceremony — automatically runs when an object is destroyed."

---

## 🤔 What Problem Does the Destructor Solve?

Imagine your object allocates memory on the heap during its lifetime:

```cpp
class FileHandler {
public:
    char* buffer;

    FileHandler() {
        buffer = new char[1024];   // allocate memory
        cout << "File opened!" << endl;
    }
};

int main() {
    FileHandler fh;
    // ... use fh ...
}   // fh goes out of scope — object is DESTROYED
    // BUT buffer is still allocated on heap! Memory leak!
```

Without a destructor, the heap memory never gets freed. **Destructors fix this.**

---

## 💡 What is a Destructor?

A **destructor** is a special function that:
1. Has the **same name** as the class, but with a **`~` (tilde)** prefix
2. Has **no return type** and **no parameters**
3. Runs **automatically** when an object is destroyed (goes out of scope or `delete` is called)
4. Used to **clean up resources** (free memory, close files, release connections)

```cpp
class MyClass {
public:
    MyClass() {
        cout << "Object created!" << endl;    // constructor
    }

    ~MyClass() {
        cout << "Object destroyed!" << endl;  // destructor
    }
};

int main() {
    cout << "Start of main" << endl;
    MyClass obj;    // Constructor runs
    cout << "End of main" << endl;
}                   // Destructor runs automatically here!
```

**Output:**
```
Start of main
Object created!
End of main
Object destroyed!
```

---

## ⏰ When Does the Destructor Run?

### 1. When a local object goes out of scope (`}`)

```cpp
int main() {
    cout << "Before block" << endl;
    {
        MyClass obj;    // Constructor called
        cout << "Inside block" << endl;
    }                   // ← Destructor called HERE (scope ends)
    cout << "After block" << endl;
}
```

**Output:**
```
Before block
Object created!
Inside block
Object destroyed!    ← happens when } is hit
After block
```

### 2. When `delete` is called on a heap object

```cpp
MyClass* ptr = new MyClass();   // Constructor called
cout << "Using object..." << endl;
delete ptr;                      // ← Destructor called HERE
ptr = nullptr;
```

**Output:**
```
Object created!
Using object...
Object destroyed!
```

### 3. Order of Destruction (LIFO — Last In, First Out)

Objects are destroyed in **reverse order** of creation — like a stack of plates.

```cpp
int main() {
    MyClass a;   // created first
    MyClass b;   // created second
    MyClass c;   // created third
}
// Destruction order: c → b → a  (reverse of creation)
```

**Output:**
```
A created
B created
C created
C destroyed    ← last created, first destroyed
B destroyed
A destroyed
```

---

## 🏗️ Destructor with Dynamic Memory

This is the most important use of destructors — freeing heap memory.

```cpp
#include <iostream>
using namespace std;

class DynamicArray {
private:
    int* data;
    int size;

public:
    // Constructor — allocates memory
    DynamicArray(int n) : size(n) {
        data = new int[n];   // allocate on heap
        for (int i = 0; i < n; i++)
            data[i] = 0;
        cout << "Array of " << n << " created." << endl;
    }

    // Destructor — frees memory
    ~DynamicArray() {
        delete[] data;   // free the heap memory
        data = nullptr;
        cout << "Array destroyed — memory freed!" << endl;
    }

    void set(int index, int value) { data[index] = value; }
    int  get(int index) { return data[index]; }

    void display() {
        for (int i = 0; i < size; i++)
            cout << data[i] << " ";
        cout << endl;
    }
};

int main() {
    DynamicArray arr(5);    // Constructor: allocates 5 ints
    arr.set(0, 10);
    arr.set(1, 20);
    arr.set(2, 30);
    arr.display();          // 10 20 0 0 0
}                           // Destructor: frees memory automatically!
```

**Output:**
```
Array of 5 created.
10 20 0 0 0
Array destroyed — memory freed!
```

---

## 🏠 Real-Life Analogy

> Think of a hotel room:
>
> **Constructor** = When you check in:
> - Turn on lights
> - Set up your belongings
> - Order room service
>
> **Destructor** = When you check out:
> - Pack your bags
> - Return the room key
> - Cancel any pending orders
> - Leave the room clean
>
> The cleanup happens AUTOMATICALLY when you "check out" (object goes out of scope).

---

## 📋 Destructor Rules

| Rule               | Detail                                                          |
|--------------------|------------------------------------------------------------------|
| Name               | `~ClassName()` — tilde + class name                             |
| Return type        | None (not even `void`)                                          |
| Parameters         | None — cannot take any arguments                                |
| How many?          | Only ONE destructor per class                                   |
| Called when?       | Goes out of scope OR `delete` is called                         |
| Called manually?   | You CAN call it manually, but you almost never should           |
| Default destructor | If you don't write one, C++ provides an empty default one       |

---

## 🔗 Constructor + Destructor Together — Full Lifecycle

```cpp
#include <iostream>
#include <string>
using namespace std;

class DatabaseConnection {
private:
    string dbName;
    bool connected;

public:
    // Constructor — opens connection
    DatabaseConnection(string name) : dbName(name), connected(false) {
        cout << "Connecting to database: " << dbName << "..." << endl;
        connected = true;
        cout << "Connected!" << endl;
    }

    // Destructor — closes connection
    ~DatabaseConnection() {
        if (connected) {
            cout << "Closing connection to: " << dbName << endl;
            connected = false;
            cout << "Connection closed." << endl;
        }
    }

    void query(string sql) {
        if (connected)
            cout << "Running query: " << sql << endl;
        else
            cout << "Not connected!" << endl;
    }
};

void doWork() {
    DatabaseConnection db("MyDatabase");   // ← Constructor runs
    db.query("SELECT * FROM users");
    db.query("UPDATE products SET price = 10");
    cout << "Work done!" << endl;
}   // ← Destructor runs here (db goes out of scope)

int main() {
    cout << "Starting program..." << endl;
    doWork();
    cout << "Back in main." << endl;
    return 0;
}
```

**Output:**
```
Starting program...
Connecting to database: MyDatabase...
Connected!
Running query: SELECT * FROM users
Running query: UPDATE products SET price = 10
Work done!
Closing connection to: MyDatabase
Connection closed.
Back in main.
```

---

## 🔢 Multiple Objects — Watch the Order

```cpp
#include <iostream>
using namespace std;

class Numbered {
private:
    int id;

public:
    Numbered(int n) : id(n) {
        cout << "Object " << id << " created" << endl;
    }
    ~Numbered() {
        cout << "Object " << id << " destroyed" << endl;
    }
};

int main() {
    Numbered a(1);
    Numbered b(2);
    Numbered c(3);
    cout << "All created. Exiting..." << endl;
}
```

**Output:**
```
Object 1 created
Object 2 created
Object 3 created
All created. Exiting...
Object 3 destroyed
Object 2 destroyed
Object 1 destroyed
```

Note the **reverse order** — LIFO (Last In, First Out), like stacking and unstacking dishes.

---

## 🐛 What Happens Without a Destructor (Memory Leak)

```cpp
class Leaky {
public:
    int* data;

    Leaky() {
        data = new int[1000000];   // allocate 1 million ints!
    }
    // ← NO DESTRUCTOR!  Memory never freed!
};

int main() {
    for (int i = 0; i < 100; i++) {
        Leaky obj;   // allocates 1 million ints...
    }                // ...but NEVER frees them!

    // After this loop, 100 million integers are stuck in memory!
    // Your program will slow down or crash!
}
```

---

## ✅ With Destructor — No Leak

```cpp
class Safe {
public:
    int* data;

    Safe() {
        data = new int[1000000];
    }

    ~Safe() {
        delete[] data;   // freed every time!
    }
};

int main() {
    for (int i = 0; i < 100; i++) {
        Safe obj;   // allocates...
    }               // ...and IMMEDIATELY frees when scope ends!
    // No memory leak!
}
```

---

## 🤝 Rule of Three (Important!)

If your class needs a custom **destructor**, it probably also needs:
1. A custom **copy constructor**
2. A custom **copy assignment operator**

This is called the **Rule of Three** in C++.

```cpp
class MyArray {
private:
    int* data;
    int size;

public:
    // 1. Constructor
    MyArray(int n) : size(n) {
        data = new int[n];
    }

    // 2. Destructor (we need this — we have heap memory)
    ~MyArray() {
        delete[] data;
    }

    // 3. Copy Constructor (deep copy)
    MyArray(const MyArray& other) : size(other.size) {
        data = new int[size];
        for (int i = 0; i < size; i++)
            data[i] = other.data[i];   // copy values, not pointer!
    }

    // 4. Copy Assignment Operator
    MyArray& operator=(const MyArray& other) {
        if (this != &other) {
            delete[] data;
            size = other.size;
            data = new int[size];
            for (int i = 0; i < size; i++)
                data[i] = other.data[i];
        }
        return *this;
    }
};
```

---

## 📊 Constructor vs Destructor

| Feature      | Constructor            | Destructor             |
|--------------|------------------------|------------------------|
| Name         | `ClassName()`          | `~ClassName()`         |
| Return type  | None                   | None                   |
| Parameters   | Can have parameters    | No parameters          |
| How many?    | Multiple (overloading) | Only ONE               |
| Called when? | Object is created      | Object is destroyed    |
| Purpose      | Initialize the object  | Clean up resources     |
| Manual call? | Yes (with `new`)       | Rarely needed manually |

---

## 🎯 Key Takeaways

1. A destructor **automatically runs** when an object goes out of scope or is deleted
2. Syntax: `~ClassName()` — tilde prefix, no return type, no parameters
3. **Only ONE destructor** per class — cannot be overloaded
4. Most important use: **freeing dynamic memory** (`delete`, `delete[]`)
5. Also used for: closing files, releasing network connections, saving state
6. Objects are destroyed in **reverse order** of creation (LIFO)
7. Without a destructor for heap memory → **memory leak**
8. If you write a destructor, also consider the **Rule of Three**

---
*Next up: Getters & Setters — the doors into your private data!* →
