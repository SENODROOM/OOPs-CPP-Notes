# 📋 1D Arrays in C++
### "A row of boxes — store many values under one name."

---

## 🤔 Why Do We Need Arrays?

Imagine you want to store the scores of 5 students.

**Without an array:**
```cpp
int score1 = 85;
int score2 = 92;
int score3 = 78;
int score4 = 95;
int score5 = 88;
```

This is messy. What if you had 1000 students? You'd need 1000 variables!

**With an array:**
```cpp
int scores[5] = {85, 92, 78, 95, 88};
```

One name, five values. Clean and simple!

---

## 🗳️ What is a 1D Array?

A **1D array** is a collection of variables of the **same type**, stored in **consecutive memory locations**, accessed using a single name and an **index number**.

Think of it like a **row of numbered boxes**:

```
scores array:
┌─────┬─────┬─────┬─────┬─────┐
│ 85  │ 92  │ 78  │ 95  │ 88  │
└─────┴─────┴─────┴─────┴─────┘
  [0]   [1]   [2]   [3]   [4]
   ↑
 Index starts at ZERO (not 1)!
```

---

## 📝 Declaring and Initializing Arrays

### Method 1: Declare with a specific size
```cpp
int scores[5];   // 5 empty boxes — garbage values inside
```

### Method 2: Declare and initialize
```cpp
int scores[5] = {85, 92, 78, 95, 88};   // all values set
```

### Method 3: Let C++ count the size
```cpp
int scores[] = {85, 92, 78, 95, 88};   // size automatically = 5
```

### Method 4: Partial initialization (rest become 0)
```cpp
int scores[5] = {85, 92};   // {85, 92, 0, 0, 0}
```

### Method 5: All zeros
```cpp
int scores[5] = {0};    // {0, 0, 0, 0, 0}
int scores[5] = {};     // {0, 0, 0, 0, 0}
```

---

## 🔢 Accessing Array Elements

You access elements using **square brackets `[]`** with an **index**.

> ⚠️ **CRITICAL RULE:** Array indices start at **0**, not 1!

```cpp
int scores[5] = {85, 92, 78, 95, 88};

cout << scores[0];   // 85  (FIRST element — index 0)
cout << scores[1];   // 92  (second element)
cout << scores[2];   // 78  (third element)
cout << scores[3];   // 95  (fourth element)
cout << scores[4];   // 88  (LAST element — index 4, NOT 5!)
```

```
Index:    0     1     2     3     4
        ┌─────┬─────┬─────┬─────┬─────┐
Value:  │ 85  │ 92  │ 78  │ 95  │ 88  │
        └─────┴─────┴─────┴─────┴─────┘
         ↑                         ↑
      scores[0]               scores[4]
     (first box)              (last box)
```

**For an array of size N:**
- First element: `arr[0]`
- Last element:  `arr[N-1]`

---

## ✏️ Modifying Array Elements

```cpp
int scores[5] = {85, 92, 78, 95, 88};

scores[2] = 100;   // Change the third element

cout << scores[2];   // Now prints 100
```

---

## 🔁 Looping Through an Array

The real power of arrays is combining them with loops!

```cpp
int scores[5] = {85, 92, 78, 95, 88};

// Print all elements
for (int i = 0; i < 5; i++) {
    cout << "scores[" << i << "] = " << scores[i] << endl;
}
```

**Output:**
```
scores[0] = 85
scores[1] = 92
scores[2] = 78
scores[3] = 95
scores[4] = 88
```

### Modern Range-Based For Loop (C++11)

```cpp
int scores[] = {85, 92, 78, 95, 88};

for (int score : scores) {
    cout << score << " ";   // 85 92 78 95 88
}
```

This is like saying "for each score in scores, print it."

---

## 🧮 Common Array Operations

### Find the Sum
```cpp
int nums[] = {10, 20, 30, 40, 50};
int sum = 0;

for (int i = 0; i < 5; i++) {
    sum += nums[i];
}

cout << "Sum: " << sum;   // 150
```

### Find the Average
```cpp
int nums[] = {10, 20, 30, 40, 50};
int sum = 0;

for (int i = 0; i < 5; i++)
    sum += nums[i];

double avg = (double)sum / 5;
cout << "Average: " << avg;   // 30
```

### Find Maximum
```cpp
int nums[] = {34, 78, 12, 95, 56};
int max = nums[0];   // assume first is largest

for (int i = 1; i < 5; i++) {
    if (nums[i] > max)
        max = nums[i];   // update if found bigger
}

cout << "Max: " << max;   // 95
```

### Find Minimum
```cpp
int nums[] = {34, 78, 12, 95, 56};
int min = nums[0];

for (int i = 1; i < 5; i++) {
    if (nums[i] < min)
        min = nums[i];
}

cout << "Min: " << min;   // 12
```

### Reverse an Array
```cpp
int arr[] = {1, 2, 3, 4, 5};
int size = 5;

for (int i = 0; i < size / 2; i++) {
    int temp = arr[i];
    arr[i] = arr[size - 1 - i];
    arr[size - 1 - i] = temp;
}

// arr is now {5, 4, 3, 2, 1}
```

---

## 📬 Passing Arrays to Functions

When you pass an array to a function, you actually pass a **pointer to its first element**.
This means the function CAN modify the original array!

```cpp
#include <iostream>
using namespace std;

void printArray(int arr[], int size) {   // or: int* arr
    for (int i = 0; i < size; i++)
        cout << arr[i] << " ";
    cout << endl;
}

void doubleAll(int arr[], int size) {
    for (int i = 0; i < size; i++)
        arr[i] *= 2;   // modifies the ORIGINAL array!
}

int main() {
    int nums[] = {1, 2, 3, 4, 5};

    cout << "Before: ";
    printArray(nums, 5);   // 1 2 3 4 5

    doubleAll(nums, 5);

    cout << "After: ";
    printArray(nums, 5);   // 2 4 6 8 10

    return 0;
}
```

> Note: Arrays always need their size passed separately — the function can't know the size on its own.

---

## 🏗️ Dynamic 1D Arrays (using DMA)

When you don't know the size at compile time:

```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cout << "Enter size: ";
    cin >> n;

    int* arr = new int[n];   // create array on the heap

    // Fill
    for (int i = 0; i < n; i++) {
        cout << "Enter value " << (i+1) << ": ";
        cin >> arr[i];
    }

    // Display
    cout << "Array: ";
    for (int i = 0; i < n; i++)
        cout << arr[i] << " ";

    delete[] arr;   // ALWAYS free heap memory!
    arr = nullptr;

    return 0;
}
```

---

## ⚠️ Common Mistakes

### ❌ Array Out of Bounds — The #1 Danger!

```cpp
int arr[5] = {1, 2, 3, 4, 5};

cout << arr[5];    // ❌ UNDEFINED BEHAVIOR! Index 5 doesn't exist (0-4 only)
cout << arr[-1];   // ❌ UNDEFINED BEHAVIOR!
cout << arr[100];  // ❌ UNDEFINED BEHAVIOR! — could crash or return garbage
```

C++ does NOT check if your index is valid. This is your responsibility!

### ❌ Forgetting Array Size Starts at 0

```cpp
int arr[5] = {10, 20, 30, 40, 50};
cout << arr[5];   // ❌ Off by one error! Should be arr[4]
```

### ❌ Comparing Arrays with ==

```cpp
int a[] = {1, 2, 3};
int b[] = {1, 2, 3};

if (a == b)   // ❌ This compares ADDRESSES, not content!
    cout << "Same";

// ✅ Compare element by element:
bool same = true;
for (int i = 0; i < 3; i++)
    if (a[i] != b[i]) { same = false; break; }
```

---

## 🧪 Complete Working Example — Student Grade Manager

```cpp
#include <iostream>
using namespace std;

int main() {
    const int SIZE = 5;
    int grades[SIZE];
    string names[SIZE];

    // Input
    cout << "=== Enter Student Grades ===" << endl;
    for (int i = 0; i < SIZE; i++) {
        cout << "Student " << (i+1) << " name: ";
        cin >> names[i];
        cout << "Grade: ";
        cin >> grades[i];
    }

    // Calculate stats
    int sum = 0, max = grades[0], min = grades[0];
    int maxIdx = 0, minIdx = 0;

    for (int i = 0; i < SIZE; i++) {
        sum += grades[i];
        if (grades[i] > max) { max = grades[i]; maxIdx = i; }
        if (grades[i] < min) { min = grades[i]; minIdx = i; }
    }

    double avg = (double)sum / SIZE;

    // Display results
    cout << "\n=== Results ===" << endl;
    for (int i = 0; i < SIZE; i++)
        cout << names[i] << ": " << grades[i] << endl;

    cout << "\nAverage: " << avg << endl;
    cout << "Highest: " << names[maxIdx] << " with " << max << endl;
    cout << "Lowest: "  << names[minIdx] << " with " << min << endl;

    return 0;
}
```

---

## 📊 Array Cheat Sheet

| Operation              | Code Example                               |
|------------------------|--------------------------------------------|
| Declare                | `int arr[5];`                              |
| Declare + Init         | `int arr[5] = {1,2,3,4,5};`               |
| Access element         | `arr[2]`                                   |
| Modify element         | `arr[2] = 99;`                             |
| First element          | `arr[0]`                                   |
| Last element (size=5)  | `arr[4]`                                   |
| Loop through           | `for(int i=0; i<5; i++) arr[i]`            |
| Dynamic array          | `int* arr = new int[n];`                   |
| Free dynamic           | `delete[] arr;`                            |
| Pass to function       | `void f(int arr[], int size)`              |

---

## 🎯 Key Takeaways

1. Arrays store **multiple values of the same type** under one name
2. Array indices start at **0**, not 1
3. For array of size N, valid indices are **0 to N-1**
4. Use loops to process arrays efficiently
5. Arrays passed to functions can be **modified** (they pass by reference)
6. Always remember to pass the **size** separately to functions
7. Use **dynamic arrays** (`new`) when size is not known at compile time
8. **Out-of-bounds access** is undefined behavior — always guard against it!

---
*Next up: 2D Arrays — storing data in rows and columns like a spreadsheet!* →
