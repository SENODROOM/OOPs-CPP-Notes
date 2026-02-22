# 🔍 Array Access and Modification
### "Getting and changing array elements"

---

## 🎯 Core Concept

**Array access** retrieves values from array elements using their index, while **modification** changes the values stored in those elements.

### The Mailbox Analogy

```
Array = Row of mailboxes
Index = Mailbox number
Access = Open mailbox to read mail
Modification = Put new mail in mailbox

Mailbox Row:
┌─────┬─────┬─────┬─────┬─────┐
│ 85  │ 92  │ 78  │ 95  │ 88  │
└─────┴─────┴─────┴─────┴─────┘
  [0]   [1]   [2]   [3]   [4]
   ▲     ▲     ▲     ▲     ▲
   │     │     │     │     │
  Read  Read  Read  Read  Read
  Write Write Write Write Write
```

---

## 🔍 Accessing Array Elements

### Basic Access Syntax

```cpp
array_name[index]
```

### Direct Access Examples

```cpp
#include <iostream>

void basicAccess() {
    int scores[5] = {85, 92, 78, 95, 88};
    
    // Access individual elements
    int firstScore = scores[0];    // 85
    int secondScore = scores[1];   // 92
    int lastScore = scores[4];     // 88
    
    std::cout << "First score: " << firstScore << std::endl;
    std::cout << "Second score: " << secondScore << std::endl;
    std::cout << "Last score: " << lastScore << std::endl;
}
```

### Variable Index Access

```cpp
void variableIndexAccess() {
    int numbers[5] = {10, 20, 30, 40, 50};
    
    // Using variables as indices
    int index = 2;
    int value = numbers[index];    // 30
    
    std::cout << "Element at index " << index << ": " << value << std::endl;
    
    // Using expressions as indices
    int sum = numbers[0] + numbers[4];  // 10 + 50 = 60
    std::cout << "Sum of first and last: " << sum << std::endl;
}
```

### Loop-Based Access

```cpp
void loopAccess() {
    int grades[5] = {85, 92, 78, 95, 88};
    
    // Forward traversal
    std::cout << "Forward traversal: ";
    for (int i = 0; i < 5; i++) {
        std::cout << grades[i] << " ";
    }
    std::cout << std::endl;
    
    // Backward traversal
    std::cout << "Backward traversal: ";
    for (int i = 4; i >= 0; i--) {
        std::cout << grades[i] << " ";
    }
    std::cout << std::endl;
    
    // Step traversal (every 2nd element)
    std::cout << "Every 2nd element: ";
    for (int i = 0; i < 5; i += 2) {
        std::cout << grades[i] << " ";
    }
    std::cout << std::endl;
}
```

### Range-Based For Loop Access (C++11+)

```cpp
void rangeBasedAccess() {
    int numbers[5] = {10, 20, 30, 40, 50};
    
    // Read-only access
    std::cout << "Read-only: ";
    for (int value : numbers) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
    
    // Modifiable access
    std::cout << "Modifiable: ";
    for (int& value : numbers) {
        value *= 2;  // Double each element
        std::cout << value << " ";
    }
    std::cout << std::endl;
    
    // Show modified array
    std::cout << "Modified array: ";
    for (int value : numbers) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
}
```

---

## ✏️ Modifying Array Elements

### Direct Assignment

```cpp
void directModification() {
    int numbers[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Original: ";
    for (int i = 0; i < 5; i++) {
        std::cout << numbers[i] << " ";
    }
    std::cout << std::endl;
    
    // Modify individual elements
    numbers[0] = 10;  // Change first element
    numbers[2] = 30;  // Change third element
    numbers[4] = 50;  // Change last element
    
    std::cout << "Modified: ";
    for (int i = 0; i < 5; i++) {
        std::cout << numbers[i] << " ";
    }
    std::cout << std::endl;
}
```

### Loop-Based Modification

```cpp
void loopModification() {
    int values[5] = {1, 2, 3, 4, 5};
    
    // Double each element
    for (int i = 0; i < 5; i++) {
        values[i] = values[i] * 2;
    }
    
    std::cout << "Doubled: ";
    for (int value : values) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
    
    // Square each element
    for (int i = 0; i < 5; i++) {
        values[i] = values[i] * values[i];
    }
    
    std::cout << "Squared: ";
    for (int value : values) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
}
```

### Conditional Modification

```cpp
void conditionalModification() {
    int scores[5] = {85, 92, 78, 95, 88};
    
    std::cout << "Original scores: ";
    for (int score : scores) {
        std::cout << score << " ";
    }
    std::cout << std::endl;
    
    // Add 5 points to scores below 80
    for (int i = 0; i < 5; i++) {
        if (scores[i] < 80) {
            scores[i] += 5;
        }
    }
    
    std::cout << "After bonus: ";
    for (int score : scores) {
        std::cout << score << " ";
    }
    std::cout << std::endl;
    
    // Cap scores at 95
    for (int i = 0; i < 5; i++) {
        if (scores[i] > 95) {
            scores[i] = 95;
        }
    }
    
    std::cout << "After capping: ";
    for (int score : scores) {
        std::cout << score << " ";
    }
    std::cout << std::endl;
}
```

---

## 🔍 Advanced Access Patterns

### Searching for Elements

```cpp
int findElement(int arr[], int size, int target) {
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            return i;  // Return index of found element
        }
    }
    return -1;  // Return -1 if not found
}

void demonstrateSearch() {
    int numbers[5] = {10, 20, 30, 40, 50};
    
    int target = 30;
    int index = findElement(numbers, 5, target);
    
    if (index != -1) {
        std::cout << "Found " << target << " at index " << index << std::endl;
    } else {
        std::cout << target << " not found in array" << std::endl;
    }
    
    target = 25;
    index = findElement(numbers, 5, target);
    
    if (index != -1) {
        std::cout << "Found " << target << " at index " << index << std::endl;
    } else {
        std::cout << target << " not found in array" << std::endl;
    }
}
```

### Finding Maximum and Minimum

```cpp
int findMax(int arr[], int size) {
    int max = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}

int findMin(int arr[], int size) {
    int min = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] < min) {
            min = arr[i];
        }
    }
    return min;
}

void demonstrateMinMax() {
    int scores[5] = {85, 92, 78, 95, 88};
    
    int highest = findMax(scores, 5);
    int lowest = findMin(scores, 5);
    
    std::cout << "Highest score: " << highest << std::endl;
    std::cout << "Lowest score: " << lowest << std::endl;
}
```

### Counting Occurrences

```cpp
int countOccurrences(int arr[], int size, int target) {
    int count = 0;
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            count++;
        }
    }
    return count;
}

void demonstrateCounting() {
    int numbers[10] = {1, 2, 3, 2, 4, 2, 5, 2, 6, 2};
    
    int target = 2;
    int count = countOccurrences(numbers, 10, target);
    
    std::cout << target << " appears " << count << " times in the array" << std::endl;
}
```

---

## 🎭 Real-World Applications

### Student Grade Processing

```cpp
void gradeProcessing() {
    const int NUM_STUDENTS = 5;
    int grades[NUM_STUDENTS] = {85, 92, 78, 95, 88};
    
    // Calculate average
    double sum = 0;
    for (int i = 0; i < NUM_STUDENTS; i++) {
        sum += grades[i];
    }
    double average = sum / NUM_STUDENTS;
    
    // Find highest and lowest
    int highest = findMax(grades, NUM_STUDENTS);
    int lowest = findMin(grades, NUM_STUDENTS);
    
    // Count grades above average
    int aboveAverage = 0;
    for (int i = 0; i < NUM_STUDENTS; i++) {
        if (grades[i] > average) {
            aboveAverage++;
        }
    }
    
    // Apply curve (add 5 points to everyone below 80)
    for (int i = 0; i < NUM_STUDENTS; i++) {
        if (grades[i] < 80) {
            grades[i] += 5;
        }
    }
    
    // Display results
    std::cout << "Grade Analysis:" << std::endl;
    std::cout << "Average: " << average << std::endl;
    std::cout << "Highest: " << highest << std::endl;
    std::cout << "Lowest: " << lowest << std::endl;
    std::cout << "Above average: " << aboveAverage << " students" << std::endl;
    
    std::cout << "Grades after curve: ";
    for (int grade : grades) {
        std::cout << grade << " ";
    }
    std::cout << std::endl;
}
```

### Inventory Management

```cpp
void inventoryManagement() {
    const int NUM_PRODUCTS = 5;
    int productIds[NUM_PRODUCTS] = {1001, 1002, 1003, 1004, 1005};
    int stock[NUM_PRODUCTS] = {15, 50, 30, 10, 25};
    double prices[NUM_PRODUCTS] = {999.99, 29.99, 79.99, 299.99, 149.99};
    
    // Process sales (reduce stock)
    int sales[NUM_PRODUCTS] = {5, 10, 8, 2, 7};
    
    for (int i = 0; i < NUM_PRODUCTS; i++) {
        stock[i] -= sales[i];
        if (stock[i] < 0) {
            stock[i] = 0;  // Can't have negative stock
        }
    }
    
    // Calculate total value
    double totalValue = 0;
    for (int i = 0; i < NUM_PRODUCTS; i++) {
        totalValue += stock[i] * prices[i];
    }
    
    // Find products that need restocking (less than 20)
    std::cout << "Products needing restock:" << std::endl;
    for (int i = 0; i < NUM_PRODUCTS; i++) {
        if (stock[i] < 20) {
            std::cout << "Product " << productIds[i] 
                     << ": " << stock[i] << " units" << std::endl;
        }
    }
    
    std::cout << "Total inventory value: $" << totalValue << std::endl;
}
```

### Data Analysis

```cpp
void dataAnalysis() {
    const int DATA_POINTS = 10;
    double data[DATA_POINTS] = {72.5, 73.2, 71.8, 74.1, 75.3, 
                               76.2, 74.8, 73.9, 72.1, 71.5};
    
    // Calculate statistics
    double sum = 0;
    double max = data[0];
    double min = data[0];
    
    for (int i = 0; i < DATA_POINTS; i++) {
        sum += data[i];
        if (data[i] > max) max = data[i];
        if (data[i] < min) min = data[i];
    }
    
    double average = sum / DATA_POINTS;
    
    // Normalize data (0-100 scale)
    double normalized[DATA_POINTS];
    for (int i = 0; i < DATA_POINTS; i++) {
        normalized[i] = ((data[i] - min) / (max - min)) * 100;
    }
    
    // Find outliers (more than 2 standard deviations from mean)
    double variance = 0;
    for (int i = 0; i < DATA_POINTS; i++) {
        variance += (data[i] - average) * (data[i] - average);
    }
    variance /= DATA_POINTS;
    double stdDev = sqrt(variance);
    
    std::cout << "Data Analysis Results:" << std::endl;
    std::cout << "Mean: " << average << std::endl;
    std::cout << "Std Dev: " << stdDev << std::endl;
    std::cout << "Min: " << min << ", Max: " << max << std::endl;
    
    std::cout << "Outliers (±2σ): ";
    for (int i = 0; i < DATA_POINTS; i++) {
        if (abs(data[i] - average) > 2 * stdDev) {
            std::cout << data[i] << " ";
        }
    }
    std::cout << std::endl;
    
    std::cout << "Normalized data: ";
    for (int i = 0; i < DATA_POINTS; i++) {
        std::cout << normalized[i] << " ";
    }
    std::cout << std::endl;
}
```

---

## ⚠️ Common Access and Modification Mistakes

### 1. Out of Bounds Access

```cpp
int arr[5] = {1, 2, 3, 4, 5};

// ❌ WRONG - Index out of bounds
int value = arr[5];   // Invalid! Valid indices: 0-4
int value2 = arr[-1]; // Invalid! Negative index

// ✅ CORRECT - Valid indices
int value3 = arr[0];  // First element
int value4 = arr[4];  // Last element
```

### 2. Off-by-One Errors in Loops

```cpp
int arr[5] = {1, 2, 3, 4, 5};

// ❌ WRONG - Goes out of bounds
for (int i = 0; i <= 5; i++) {  // i goes to 5 (invalid)
    std::cout << arr[i] << " ";
}

// ✅ CORRECT - Stays within bounds
for (int i = 0; i < 5; i++) {  // i goes to 4 (valid)
    std::cout << arr[i] << " ";
}
```

### 3. Uninitialized Access

```cpp
void function() {
    int arr[5];  // Uninitialized array
    
    // ❌ WRONG - Accessing garbage values
    std::cout << arr[0];  // Undefined behavior!
    
    // ✅ CORRECT - Initialize first
    int arr2[5] = {0};
    std::cout << arr2[0];  // Safe: prints 0
}
```

### 4. Modifying Const Arrays

```cpp
// ❌ WRONG - Cannot modify const array
const int arr[5] = {1, 2, 3, 4, 5};
arr[0] = 10;  // Compilation error!

// ✅ CORRECT - Use non-const array for modification
int arr2[5] = {1, 2, 3, 4, 5};
arr2[0] = 10;  // OK
```

---

## 🛡️ Safe Access Practices

### Bounds Checking

```cpp
bool isValidIndex(int index, int size) {
    return index >= 0 && index < size;
}

void safeAccess(int arr[], int size, int index) {
    if (isValidIndex(index, size)) {
        std::cout << "Element at index " << index << ": " << arr[index] << std::endl;
    } else {
        std::cout << "Invalid index " << index << std::endl;
    }
}

void demonstrateSafeAccess() {
    int arr[5] = {10, 20, 30, 40, 50};
    
    safeAccess(arr, 5, 2);   // ✅ Valid
    safeAccess(arr, 5, 5);   // ❌ Invalid
    safeAccess(arr, 5, -1);  // ❌ Invalid
}
```

### Safe Modification

```cpp
void safeModify(int arr[], int size, int index, int newValue) {
    if (isValidIndex(index, size)) {
        int oldValue = arr[index];
        arr[index] = newValue;
        std::cout << "Changed index " << index << " from " 
                 << oldValue << " to " << newValue << std::endl;
    } else {
        std::cout << "Cannot modify invalid index " << index << std::endl;
    }
}

void demonstrateSafeModification() {
    int arr[5] = {10, 20, 30, 40, 50};
    
    safeModify(arr, 5, 2, 99);   // ✅ Valid
    safeModify(arr, 5, 5, 99);   // ❌ Invalid
    safeModify(arr, 5, -1, 99);  // ❌ Invalid
}
```

### Using Assertions

```cpp
#include <cassert>

void assertAccess(int arr[], int size, int index) {
    assert(index >= 0 && index < size && "Index out of bounds");
    std::cout << "Element at index " << index << ": " << arr[index] << std::endl;
}

void demonstrateAssertAccess() {
    int arr[5] = {10, 20, 30, 40, 50};
    
    assertAccess(arr, 5, 2);   // ✅ Valid
    // assertAccess(arr, 5, 5);   // ❌ Will trigger assertion
}
```

---

## 🔍 Debugging Access Issues

### Printing Array Contents

```cpp
void printArray(int arr[], int size, const std::string& name) {
    std::cout << name << ": [";
    for (int i = 0; i < size; i++) {
        std::cout << arr[i];
        if (i < size - 1) std::cout << ", ";
    }
    std::cout << "]" << std::endl;
}

void debugAccess() {
    int arr[5] = {10, 20, 30, 40, 50};
    
    printArray(arr, 5, "Original");
    
    // Modify and show changes
    arr[2] = 99;
    printArray(arr, 5, "Modified");
}
```

### Tracking Modifications

```cpp
class ArrayTracker {
private:
    int* data;
    int size;
    int modificationCount;
    
public:
    ArrayTracker(int arr[], int s) : size(s), modificationCount(0) {
        data = new int[size];
        for (int i = 0; i < size; i++) {
            data[i] = arr[i];
        }
    }
    
    ~ArrayTracker() { delete[] data; }
    
    int& operator[](int index) {
        if (index >= 0 && index < size) {
            modificationCount++;
            std::cout << "Modification #" << modificationCount 
                     << " at index " << index << std::endl;
            return data[index];
        }
        throw std::out_of_range("Index out of bounds");
    }
    
    int get(int index) const {
        if (index >= 0 && index < size) {
            return data[index];
        }
        throw std::out_of_range("Index out of bounds");
    }
    
    int getModificationCount() const { return modificationCount; }
};

void demonstrateTracking() {
    int arr[5] = {10, 20, 30, 40, 50};
    ArrayTracker tracker(arr, 5);
    
    tracker[2] = 99;  // Tracked modification
    tracker[0] = 11;  // Tracked modification
    
    std::cout << "Total modifications: " << tracker.getModificationCount() << std::endl;
}
```

---

## 📊 Performance Considerations

### Access Patterns

```cpp
#include <chrono>

void compareAccessPatterns() {
    const int SIZE = 1000000;
    int arr[SIZE];
    
    // Initialize array
    for (int i = 0; i < SIZE; i++) {
        arr[i] = i;
    }
    
    // Sequential access (cache-friendly)
    auto start = std::chrono::high_resolution_clock::now();
    long sum1 = 0;
    for (int i = 0; i < SIZE; i++) {
        sum1 += arr[i];
    }
    auto sequentialTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    // Random access (cache-unfriendly)
    start = std::chrono::high_resolution_clock::now();
    long sum2 = 0;
    for (int i = 0; i < SIZE; i++) {
        sum2 += arr[rand() % SIZE];
    }
    auto randomTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    std::cout << "Sequential access: " << sequentialTime << " microseconds" << std::endl;
    std::cout << "Random access: " << randomTime << " microseconds" << std::endl;
    std::cout << "Random is " << (double)randomTime / sequentialTime 
             << "x slower" << std::endl;
}
```

### Modification Overhead

```cpp
void compareModificationOverhead() {
    const int SIZE = 1000000;
    int arr[SIZE];
    
    // Initialize with zeros
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < SIZE; i++) {
        arr[i] = 0;
    }
    auto zeroTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    // Initialize with calculations
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < SIZE; i++) {
        arr[i] = i * 2 + 1;
    }
    auto calcTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    std::cout << "Zero initialization: " << zeroTime << " microseconds" << std::endl;
    std::cout << "Calculation initialization: " << calcTime << " microseconds" << std::endl;
    std::cout << "Calculation is " << (double)calcTime / zeroTime 
             << "x slower" << std::endl;
}
```

---

## 🎯 Key Takeaways

1. **Array access** uses zero-based indexing: `arr[0]` is first element
2. **Bounds checking** is your responsibility - out-of-bounds access is undefined
3. **Loop-based access** enables processing of entire arrays
4. **Range-based for loops** provide safer, cleaner access (C++11+)
5. **Modification** changes values in place, affecting the original array
6. **Safe access patterns** improve performance and prevent crashes
7. **Debugging tools** help track and verify array operations

---

## 🔄 Complete Access and Modification Guide

| Operation | Syntax | Purpose | Example |
|-----------|---------|---------|---------|
| Read element | `arr[index]` | Access value | `int x = arr[2]` |
| Write element | `arr[index] = value` | Modify value | `arr[2] = 99` |
| Loop access | `for(int i=0; i<size; i++)` | Process all | `for(int i=0; i<5; i++) sum += arr[i]` |
| Range access | `for(type var : arr)` | Modern access | `for(int x : arr) cout << x` |
| Bounds check | `if(index >= 0 && index < size)` | Safe access | `if(i >= 0 && i < 5) arr[i]` |

---

## 🔄 Next Steps

Now that you understand how to access and modify arrays, let's explore how to perform common operations:

*Continue reading: [Array Operations](ArrayOperations.md)*
