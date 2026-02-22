# 🔗 Arrays and Functions
### "Passing arrays to functions for modular programming"

---

## 🎯 Core Concept

**Arrays and functions** work together to create modular, reusable code. Functions can receive arrays as parameters, process them, and return results.

### The Workshop Analogy

```
Array = Raw materials (wood, metal, etc.)
Function = Machine/tool that processes materials
Parameters = Settings for the machine
Return value = Finished product

Workshop:
┌─────────────────────────────────────────┐
│ 📦 Array Input    │ 🔧 Function Process │ 📦 Result Output │
├─────────────────┬─────────────────────┬─────────────────────┤
│ Raw Materials   │ Saw/Drill/Paint     │ Finished Product    │
│ (Array Data)    │ (Function Logic)    │ (Processed Data)    │
└─────────────────┴─────────────────────┴─────────────────────┘
```

---

## 🏗️ Array Parameters

### Basic Array Parameter Syntax

```cpp
return_type function_name(data_type array_name[], int size) {
    // Function body
}
```

### Passing Array to Function

```cpp
#include <iostream>

// Function that takes an array parameter
void printArray(int arr[], int size) {
    std::cout << "Array: [";
    for (int i = 0; i < size; i++) {
        std::cout << arr[i];
        if (i < size - 1) std::cout << ", ";
    }
    std::cout << "]" << std::endl;
}

void demonstrateBasicParameter() {
    int numbers[5] = {10, 20, 30, 40, 50};
    
    printArray(numbers, 5);  // Pass array and size
}
```

### Const Array Parameter

```cpp
// Function that cannot modify the array
void displayArray(const int arr[], int size) {
    std::cout << "Displaying array (read-only): [";
    for (int i = 0; i < size; i++) {
        std::cout << arr[i];
        if (i < size - 1) std::cout << ", ";
    }
    std::cout << "]" << std::endl;
    
    // arr[0] = 99;  // ❌ Cannot modify const array
}

void demonstrateConstParameter() {
    int numbers[5] = {10, 20, 30, 40, 50};
    
    displayArray(numbers, 5);  // Safe - function cannot modify array
}
```

---

## 🔄 Return Types with Arrays

### Returning Array Size

```cpp
int getArraySize(int arr[]) {
    // ❌ This doesn't work! sizeof(arr) gives pointer size
    // return sizeof(arr) / sizeof(arr[0]);
    
    // ✅ Must pass size as parameter
    // This function demonstrates the limitation
    return -1;  // Cannot get size from array parameter
}

// Correct approach - pass size as parameter
int sumArray(int arr[], int size) {
    int sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    return sum;
}

void demonstrateArrayReturn() {
    int numbers[5] = {10, 20, 30, 40, 50};
    
    int total = sumArray(numbers, 5);
    std::cout << "Sum: " << total << std::endl;
}
```

### Returning Pointer to Array

```cpp
// Function that returns a pointer to dynamically allocated array
int* createArray(int size) {
    int* arr = new int[size];
    
    for (int i = 0; i < size; i++) {
        arr[i] = i * 10;
    }
    
    return arr;  // Return pointer to array
}

void demonstratePointerReturn() {
    int size = 5;
    int* dynamicArray = createArray(size);
    
    std::cout << "Dynamic array: [";
    for (int i = 0; i < size; i++) {
        std::cout << dynamicArray[i];
        if (i < size - 1) std::cout << ", ";
    }
    std::cout << "]" << std::endl;
    
    delete[] dynamicArray;  // Don't forget to free memory!
}
```

---

## 🎭 Function Types with Arrays

### Processing Functions

```cpp
// Calculate average
double calculateAverage(int arr[], int size) {
    if (size == 0) return 0.0;
    
    double sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    return sum / size;
}

// Find maximum value
int findMaximum(int arr[], int size) {
    if (size == 0) return -1;  // Error code
    
    int max = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}

// Find minimum value
int findMinimum(int arr[], int size) {
    if (size == 0) return -1;  // Error code
    
    int min = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] < min) {
            min = arr[i];
        }
    }
    return min;
}

void demonstrateProcessingFunctions() {
    int grades[5] = {85, 92, 78, 95, 88};
    
    double average = calculateAverage(grades, 5);
    int highest = findMaximum(grades, 5);
    int lowest = findMinimum(grades, 5);
    
    std::cout << "Grade Analysis:" << std::endl;
    std::cout << "Average: " << average << std::endl;
    std::cout << "Highest: " << highest << std::endl;
    std::cout << "Lowest: " << lowest << std::endl;
}
```

### Modification Functions

```cpp
// Double all elements
void doubleElements(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        arr[i] *= 2;
    }
}

// Add value to all elements
void addToAll(int arr[], int size, int value) {
    for (int i = 0; i < size; i++) {
        arr[i] += value;
    }
}

// Reverse array in place
void reverseArray(int arr[], int size) {
    for (int i = 0; i < size / 2; i++) {
        int temp = arr[i];
        arr[i] = arr[size - 1 - i];
        arr[size - 1 - i] = temp;
    }
}

void demonstrateModificationFunctions() {
    int numbers[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Original: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    doubleElements(numbers, 5);
    
    std::cout << "Doubled: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    addToAll(numbers, 5, 10);
    
    std::cout << "Added 10: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    reverseArray(numbers, 5);
    
    std::cout << "Reversed: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Search Functions

```cpp
// Linear search
int linearSearch(int arr[], int size, int target) {
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            return i;  // Return index if found
        }
    }
    return -1;  // Return -1 if not found
}

// Count occurrences
int countOccurrences(int arr[], int size, int target) {
    int count = 0;
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            count++;
        }
    }
    return count;
}

// Find all indices of target
int findAllIndices(int arr[], int size, int target, int indices[]) {
    int count = 0;
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            indices[count++] = i;
        }
    }
    return count;
}

void demonstrateSearchFunctions() {
    int numbers[10] = {5, 12, 7, 3, 9, 15, 2, 8, 11, 6};
    
    int target = 9;
    int index = linearSearch(numbers, 10, target);
    
    if (index != -1) {
        std::cout << "Found " << target << " at index " << index << std::endl;
    } else {
        std::cout << target << " not found" << std::endl;
    }
    
    int occurrences = countOccurrences(numbers, 10, 5);
    std::cout << "Number 5 appears " << occurrences << " times" << std::endl;
    
    int indices[10];
    int count = findAllIndices(numbers, 10, 5, indices);
    
    std::cout << "Number 5 appears at indices: ";
    for (int i = 0; i < count; i++) {
        std::cout << indices[i] << " ";
    }
    std::cout << std::endl;
}
```

---

## 🔍 Advanced Array Function Concepts

### Array Decay to Pointer

```cpp
void demonstrateArrayDecay() {
    int arr[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Size of arr in main: " << sizeof(arr) << " bytes" << std::endl;
    
    // When passed to function, array decays to pointer
    std::cout << "Array passed to function becomes pointer" << std::endl;
}

// This function receives arr as a pointer, not an array
void showArrayDecay(int arr[]) {
    std::cout << "Size of arr parameter: " << sizeof(arr) << " bytes" << std::endl;
    std::cout << "This is pointer size, not array size!" << std::endl;
}
```

### Reference to Array (C++11)

```cpp
// Function that takes a reference to an array
void processArrayReference(int (&arr)[5]) {
    std::cout << "Array size in function: " << sizeof(arr) << " bytes" << std::endl;
    std::cout << "This is actual array size!" << std::endl;
    
    // Can modify the original array
    for (int i = 0; i < 5; i++) {
        arr[i] *= 2;
    }
}

void demonstrateArrayReference() {
    int numbers[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Before: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    processArrayReference(numbers);
    
    std::cout << "After: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Template Functions for Arrays

```cpp
// Template function that works with any array type
template<typename T, int N>
void printTemplateArray(T (&arr)[N]) {
    std::cout << "Array of size " << N << ": [";
    for (int i = 0; i < N; i++) {
        std::cout << arr[i];
        if (i < N - 1) std::cout << ", ";
    }
    std::cout << "]" << std::endl;
}

template<typename T, int N>
T findMaxTemplate(T (&arr)[N]) {
    T max = arr[0];
    for (int i = 1; i < N; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}

void demonstrateTemplateFunctions() {
    int intArray[5] = {10, 20, 30, 40, 50};
    double doubleArray[3] = {3.14, 2.71, 1.41};
    
    printTemplateArray(intArray);
    printTemplateArray(doubleArray);
    
    int maxInt = findMaxTemplate(intArray);
    double maxDouble = findMaxTemplate(doubleArray);
    
    std::cout << "Max int: " << maxInt << std::endl;
    std::cout << "Max double: " << maxDouble << std::endl;
}
```

---

## 🎯 Real-World Applications

### Student Management System

```cpp
class StudentManager {
private:
    static const int MAX_STUDENTS = 100;
    int studentIds[MAX_STUDENTS];
    std::string studentNames[MAX_STUDENTS];
    int grades[MAX_STUDENTS];
    int studentCount;
    
public:
    StudentManager() : studentCount(0) {}
    
    bool addStudent(int id, const std::string& name, int grade) {
        if (studentCount >= MAX_STUDENTS) {
            return false;
        }
        
        studentIds[studentCount] = id;
        studentNames[studentCount] = name;
        grades[studentCount] = grade;
        studentCount++;
        
        return true;
    }
    
    double calculateAverage() {
        if (studentCount == 0) return 0.0;
        
        double sum = 0;
        for (int i = 0; i < studentCount; i++) {
            sum += grades[i];
        }
        return sum / studentCount;
    }
    
    int findHighestGrade() {
        if (studentCount == 0) return -1;
        
        int highest = grades[0];
        int highestIndex = 0;
        
        for (int i = 1; i < studentCount; i++) {
            if (grades[i] > highest) {
                highest = grades[i];
                highestIndex = i;
            }
        }
        
        return highestIndex;
    }
    
    void displayStudent(int index) {
        if (index >= 0 && index < studentCount) {
            std::cout << "ID: " << studentIds[index]
                     << ", Name: " << studentNames[index]
                     << ", Grade: " << grades[index] << std::endl;
        }
    }
    
    void displayAllStudents() {
        std::cout << "All Students:" << std::endl;
        for (int i = 0; i < studentCount; i++) {
            displayStudent(i);
        }
    }
    
    void curveGrades(int bonusPoints) {
        for (int i = 0; i < studentCount; i++) {
            grades[i] += bonusPoints;
            if (grades[i] > 100) grades[i] = 100;  // Cap at 100
        }
    }
};

void demonstrateStudentManager() {
    StudentManager manager;
    
    // Add students
    manager.addStudent(1001, "Alice", 85);
    manager.addStudent(1002, "Bob", 92);
    manager.addStudent(1003, "Charlie", 78);
    manager.addStudent(1004, "Diana", 95);
    manager.addStudent(1005, "Eve", 88);
    
    // Display all students
    manager.displayAllStudents();
    
    // Calculate statistics
    std::cout << "Average grade: " << manager.calculateAverage() << std::endl;
    
    int highestIndex = manager.findHighestGrade();
    std::cout << "Highest grade student: ";
    manager.displayStudent(highestIndex);
    
    // Apply curve
    manager.curveGrades(5);
    std::cout << "After 5-point curve:" << std::endl;
    manager.displayAllStudents();
    
    std::cout << "New average: " << manager.calculateAverage() << std::endl;
}
```

### Data Analysis Tool

```cpp
class DataAnalyzer {
public:
    // Calculate mean
    static double calculateMean(int data[], int size) {
        if (size == 0) return 0.0;
        
        double sum = 0;
        for (int i = 0; i < size; i++) {
            sum += data[i];
        }
        return sum / size;
    }
    
    // Calculate median
    static double calculateMedian(int data[], int size) {
        if (size == 0) return 0.0;
        
        // Create a copy to sort
        int* sorted = new int[size];
        for (int i = 0; i < size; i++) {
            sorted[i] = data[i];
        }
        
        // Sort the copy
        for (int i = 0; i < size - 1; i++) {
            for (int j = 0; j < size - i - 1; j++) {
                if (sorted[j] > sorted[j + 1]) {
                    int temp = sorted[j];
                    sorted[j] = sorted[j + 1];
                    sorted[j + 1] = temp;
                }
            }
        }
        
        // Find median
        double median;
        if (size % 2 == 0) {
            median = (sorted[size / 2 - 1] + sorted[size / 2]) / 2.0;
        } else {
            median = sorted[size / 2];
        }
        
        delete[] sorted;
        return median;
    }
    
    // Calculate standard deviation
    static double calculateStandardDeviation(int data[], int size) {
        if (size == 0) return 0.0;
        
        double mean = calculateMean(data, size);
        double sumSquaredDifferences = 0;
        
        for (int i = 0; i < size; i++) {
            double difference = data[i] - mean;
            sumSquaredDifferences += difference * difference;
        }
        
        return sqrt(sumSquaredDifferences / size);
    }
    
    // Find outliers (more than 2 standard deviations from mean)
    static int findOutliers(int data[], int size, int outliers[]) {
        if (size == 0) return 0;
        
        double mean = calculateMean(data, size);
        double stdDev = calculateStandardDeviation(data, size);
        
        int outlierCount = 0;
        for (int i = 0; i < size; i++) {
            if (abs(data[i] - mean) > 2 * stdDev) {
                outliers[outlierCount++] = i;
            }
        }
        
        return outlierCount;
    }
};

void demonstrateDataAnalyzer() {
    int data[10] = {72, 75, 68, 71, 73, 69, 74, 76, 70, 120};  // 120 is an outlier
    
    std::cout << "Data Analysis:" << std::endl;
    std::cout << "Data: ";
    for (int value : data) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
    
    double mean = DataAnalyzer::calculateMean(data, 10);
    double median = DataAnalyzer::calculateMedian(data, 10);
    double stdDev = DataAnalyzer::calculateStandardDeviation(data, 10);
    
    std::cout << "Mean: " << mean << std::endl;
    std::cout << "Median: " << median << std::endl;
    std::cout << "Standard Deviation: " << stdDev << std::endl;
    
    int outliers[10];
    int outlierCount = DataAnalyzer::findOutliers(data, 10, outliers);
    
    std::cout << "Outliers (±2σ): ";
    for (int i = 0; i < outlierCount; i++) {
        std::cout << data[outliers[i]] << " ";
    }
    std::cout << std::endl;
}
```

---

## ⚠️ Common Function Mistakes

### 1. Not Passing Array Size

```cpp
// ❌ WRONG - Cannot determine array size inside function
void processArray(int arr[]) {
    int size = sizeof(arr) / sizeof(arr[0]);  // Wrong!
    // sizeof(arr) gives pointer size, not array size
}

// ✅ CORRECT - Pass size as parameter
void processArray(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        // Process arr[i]
    }
}
```

### 2. Modifying Const Array

```cpp
// ❌ WRONG - Cannot modify const array parameter
void modifyArray(const int arr[], int size) {
    arr[0] = 99;  // Compilation error!
}

// ✅ CORRECT - Use non-const parameter for modification
void modifyArray(int arr[], int size) {
    arr[0] = 99;  // OK
}
```

### 3. Returning Local Array

```cpp
// ❌ WRONG - Returning pointer to local array
int* createBadArray() {
    int localArray[5] = {1, 2, 3, 4, 5};
    return localArray;  // DANGER! Local array destroyed
}

// ✅ CORRECT - Use dynamic allocation
int* createGoodArray(int size) {
    int* dynamicArray = new int[size];
    for (int i = 0; i < size; i++) {
        dynamicArray[i] = i + 1;
    }
    return dynamicArray;  // OK - caller must delete[]
}
```

---

## 🛡️ Best Practices

### 1. Always Pass Array Size

```cpp
void processArray(int arr[], int size) {
    // Always include size parameter
}
```

### 2. Use Const for Read-Only Arrays

```cpp
void displayArray(const int arr[], int size) {
    // Function cannot modify array
}
```

### 3. Document Array Parameters

```cpp
/**
 * Calculates the sum of an integer array
 * @param arr The array to sum
 * @param size The number of elements in the array
 * @return The sum of all elements
 */
int sumArray(const int arr[], int size);
```

### 4. Handle Edge Cases

```cpp
double calculateAverage(const int arr[], int size) {
    if (size == 0) {
        return 0.0;  // Handle empty array
    }
    
    double sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    return sum / size;
}
```

### 5. Use References for Fixed-Size Arrays

```cpp
template<int N>
void processFixedArray(int (&arr)[N]) {
    // Array size is known at compile time
    constexpr int size = N;
}
```

---

## 🔍 Debugging Array Functions

### Array Bounds Checking

```cpp
void safeAccess(int arr[], int size, int index) {
    if (index >= 0 && index < size) {
        std::cout << "Element at index " << index << ": " << arr[index] << std::endl;
    } else {
        std::cout << "Error: Index " << index << " out of bounds" << std::endl;
    }
}

void demonstrateSafeAccess() {
    int arr[5] = {10, 20, 30, 40, 50};
    
    safeAccess(arr, 5, 2);   // ✅ Valid
    safeAccess(arr, 5, 5);   // ❌ Invalid
    safeAccess(arr, 5, -1);  // ❌ Invalid
}
```

### Array Content Verification

```cpp
bool verifyArrayContents(int arr[], int size, int expected[], int expectedSize) {
    if (size != expectedSize) {
        return false;
    }
    
    for (int i = 0; i < size; i++) {
        if (arr[i] != expected[i]) {
            return false;
        }
    }
    
    return true;
}

void testArrayFunction() {
    int input[5] = {1, 2, 3, 4, 5};
    int expected[5] = {2, 4, 6, 8, 10};
    
    // Test function
    doubleElements(input, 5);
    
    // Verify results
    if (verifyArrayContents(input, 5, expected, 5)) {
        std::cout << "Function test passed!" << std::endl;
    } else {
        std::cout << "Function test failed!" << std::endl;
    }
}
```

---

## 📊 Performance Considerations

### Pass by Reference vs Pass by Value

```cpp
// Arrays are always passed by reference (pointer)
// No performance penalty for large arrays

void processLargeArray(int arr[], int size) {
    // Efficient - only pointer is passed
}

// For small arrays, consider passing by value if you need a copy
void processSmallArrayCopy(std::array<int, 5> arr) {
    // Creates a copy - only for small arrays
}
```

### Function Call Overhead

```cpp
// Inline functions for small operations
inline int getFirstElement(const int arr[], int size) {
    return (size > 0) ? arr[0] : -1;
}

// Regular functions for complex operations
void complexArrayOperation(int arr[], int size);
```

---

## 🎯 Key Takeaways

1. **Arrays decay to pointers** when passed to functions
2. **Always pass array size** as a separate parameter
3. **Use const** for read-only array parameters
4. **Cannot return arrays** directly - use pointers or dynamic allocation
5. **Template functions** can preserve array size information
6. **Reference parameters** work with fixed-size arrays
7. **Edge cases** (empty arrays) must be handled explicitly
8. **Documentation** is crucial for array function parameters

---

## 🔄 Complete Array and Function Guide

| Parameter Type | Syntax | Use Case | Example |
|----------------|--------|---------|---------|
| Regular array | `type arr[]` | Modifiable array | `void sort(int arr[], int size)` |
| Const array | `const type arr[]` | Read-only array | `void display(const int arr[], int size)` |
| Array reference | `type (&arr)[N]` | Fixed-size array | `template<int N> void process(int (&arr)[N])` |
| Pointer return | `type* func()` | Dynamic array | `int* createArray(int size)` |

---

## 🔄 Next Steps

Now that you understand arrays and functions, let's explore common pitfalls and how to avoid them:

*Continue reading: [Common Array Pitfalls](CommonArrayPitfalls.md)*
