# 🔧 Array Operations
### "Common tasks and algorithms for working with arrays"

---

## 🎯 Core Concept

**Array operations** are common tasks and algorithms performed on arrays, such as searching, sorting, filtering, and statistical calculations.

### The Toolbox Analogy

```
Array = Raw materials (wood, metal, etc.)
Operations = Tools (hammer, saw, drill, etc.)
Result = Finished product (chair, table, etc.)

Toolbox:
┌─────────────────────────────────────────┐
│ 🔍 Search    │ 📊 Statistics  │ 🔄 Sort   │
│ 🔀 Shuffle   │ 📈 Transform   │ 🎯 Filter │
│ 📝 Copy      │ 🔀 Reverse     │ 📐 Merge  │
└─────────────────────────────────────────┘
```

---

## 🔍 Searching Operations

### Linear Search

```cpp
int linearSearch(int arr[], int size, int target) {
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            return i;  // Return index if found
        }
    }
    return -1;  // Return -1 if not found
}

void demonstrateLinearSearch() {
    int numbers[10] = {5, 12, 7, 3, 9, 15, 2, 8, 11, 6};
    int target = 9;
    
    int index = linearSearch(numbers, 10, target);
    
    if (index != -1) {
        std::cout << "Found " << target << " at index " << index << std::endl;
    } else {
        std::cout << target << " not found in the array" << std::endl;
    }
}
```

### Binary Search (for sorted arrays)

```cpp
int binarySearch(int arr[], int size, int target) {
    int left = 0;
    int right = size - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (arr[mid] == target) {
            return mid;  // Found
        } else if (arr[mid] < target) {
            left = mid + 1;  // Search right half
        } else {
            right = mid - 1;  // Search left half
        }
    }
    
    return -1;  // Not found
}

void demonstrateBinarySearch() {
    int sortedNumbers[10] = {2, 3, 5, 6, 7, 8, 9, 11, 12, 15};
    int target = 9;
    
    int index = binarySearch(sortedNumbers, 10, target);
    
    if (index != -1) {
        std::cout << "Found " << target << " at index " << index << std::endl;
    } else {
        std::cout << target << " not found in the array" << std::endl;
    }
}
```

### Find All Occurrences

```cpp
int findAllOccurrences(int arr[], int size, int target, int indices[]) {
    int count = 0;
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            indices[count] = i;
            count++;
        }
    }
    return count;
}

void demonstrateFindAllOccurrences() {
    int numbers[10] = {5, 9, 3, 9, 7, 9, 2, 8, 9, 6};
    int target = 9;
    int indices[10];
    
    int count = findAllOccurrences(numbers, 10, target, indices);
    
    std::cout << target << " appears " << count << " times at indices: ";
    for (int i = 0; i < count; i++) {
        std::cout << indices[i] << " ";
    }
    std::cout << std::endl;
}
```

---

## 📊 Statistical Operations

### Sum and Average

```cpp
double calculateSum(int arr[], int size) {
    double sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    return sum;
}

double calculateAverage(int arr[], int size) {
    return calculateSum(arr, size) / size;
}

void demonstrateSumAndAverage() {
    int grades[5] = {85, 92, 78, 95, 88};
    
    double sum = calculateSum(grades, 5);
    double average = calculateAverage(grades, 5);
    
    std::cout << "Sum: " << sum << std::endl;
    std::cout << "Average: " << average << std::endl;
}
```

### Min and Max

```cpp
int findMin(int arr[], int size) {
    int min = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] < min) {
            min = arr[i];
        }
    }
    return min;
}

int findMax(int arr[], int size) {
    int max = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}

void demonstrateMinMax() {
    int temperatures[7] = {72, 75, 68, 71, 73, 69, 74};
    
    int min = findMin(temperatures, 7);
    int max = findMax(temperatures, 7);
    
    std::cout << "Minimum temperature: " << min << std::endl;
    std::cout << "Maximum temperature: " << max << std::endl;
    std::cout << "Temperature range: " << (max - min) << std::endl;
}
```

### Median

```cpp
double findMedian(int arr[], int size) {
    // First, sort the array
    for (int i = 0; i < size - 1; i++) {
        for (int j = 0; j < size - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
    
    // Find median
    if (size % 2 == 0) {
        // Even number of elements
        return (arr[size / 2 - 1] + arr[size / 2]) / 2.0;
    } else {
        // Odd number of elements
        return arr[size / 2];
    }
}

void demonstrateMedian() {
    int scores[7] = {85, 92, 78, 95, 88, 76, 83};
    
    double median = findMedian(scores, 7);
    
    std::cout << "Median score: " << median << std::endl;
}
```

---

## 🔄 Sorting Operations

### Bubble Sort

```cpp
void bubbleSort(int arr[], int size) {
    for (int i = 0; i < size - 1; i++) {
        for (int j = 0; j < size - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                // Swap elements
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}

void demonstrateBubbleSort() {
    int numbers[8] = {64, 34, 25, 12, 22, 11, 90, 88};
    
    std::cout << "Before sorting: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    bubbleSort(numbers, 8);
    
    std::cout << "After sorting: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Selection Sort

```cpp
void selectionSort(int arr[], int size) {
    for (int i = 0; i < size - 1; i++) {
        int minIndex = i;
        
        // Find minimum element in unsorted part
        for (int j = i + 1; j < size; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        
        // Swap minimum element with first unsorted element
        if (minIndex != i) {
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}

void demonstrateSelectionSort() {
    int numbers[8] = {64, 34, 25, 12, 22, 11, 90, 88};
    
    std::cout << "Before sorting: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    selectionSort(numbers, 8);
    
    std::cout << "After sorting: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Insertion Sort

```cpp
void insertionSort(int arr[], int size) {
    for (int i = 1; i < size; i++) {
        int key = arr[i];
        int j = i - 1;
        
        // Move elements greater than key to one position ahead
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];
            j--;
        }
        arr[j + 1] = key;
    }
}

void demonstrateInsertionSort() {
    int numbers[8] = {64, 34, 25, 12, 22, 11, 90, 88};
    
    std::cout << "Before sorting: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    insertionSort(numbers, 8);
    
    std::cout << "After sorting: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

---

## 🎯 Filtering Operations

### Filter by Condition

```cpp
int filterGreaterThan(int source[], int sourceSize, int threshold, int result[]) {
    int resultSize = 0;
    
    for (int i = 0; i < sourceSize; i++) {
        if (source[i] > threshold) {
            result[resultSize] = source[i];
            resultSize++;
        }
    }
    
    return resultSize;
}

void demonstrateFilter() {
    int numbers[10] = {5, 12, 7, 3, 9, 15, 2, 8, 11, 6};
    int filtered[10];
    
    int threshold = 8;
    int filteredSize = filterGreaterThan(numbers, 10, threshold, filtered);
    
    std::cout << "Original array: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    std::cout << "Filtered (> " << threshold << "): ";
    for (int i = 0; i < filteredSize; i++) {
        std::cout << filtered[i] << " ";
    }
    std::cout << std::endl;
}
```

### Remove Duplicates

```cpp
int removeDuplicates(int source[], int sourceSize, int result[]) {
    int resultSize = 0;
    
    for (int i = 0; i < sourceSize; i++) {
        bool isDuplicate = false;
        
        // Check if element already exists in result
        for (int j = 0; j < resultSize; j++) {
            if (source[i] == result[j]) {
                isDuplicate = true;
                break;
            }
        }
        
        // Add to result if not duplicate
        if (!isDuplicate) {
            result[resultSize] = source[i];
            resultSize++;
        }
    }
    
    return resultSize;
}

void demonstrateRemoveDuplicates() {
    int numbers[10] = {5, 12, 7, 5, 9, 12, 2, 7, 11, 5};
    int unique[10];
    
    int uniqueSize = removeDuplicates(numbers, 10, unique);
    
    std::cout << "Original array: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    std::cout << "Unique elements: ";
    for (int i = 0; i < uniqueSize; i++) {
        std::cout << unique[i] << " ";
    }
    std::cout << std::endl;
}
```

---

## 🔄 Transformation Operations

### Map (Apply Function to Each Element)

```cpp
void mapSquare(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        arr[i] = arr[i] * arr[i];
    }
}

void mapMultiply(int arr[], int size, int multiplier) {
    for (int i = 0; i < size; i++) {
        arr[i] = arr[i] * multiplier;
    }
}

void demonstrateMap() {
    int numbers[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Original: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    mapMultiply(numbers, 5, 3);
    
    std::cout << "Multiplied by 3: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    mapSquare(numbers, 5);
    
    std::cout << "Squared: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Normalize (Scale to 0-1 Range)

```cpp
void normalize(int arr[], int size) {
    // Find min and max
    int min = arr[0];
    int max = arr[0];
    
    for (int i = 1; i < size; i++) {
        if (arr[i] < min) min = arr[i];
        if (arr[i] > max) max = arr[i];
    }
    
    // Normalize to 0-100 range
    for (int i = 0; i < size; i++) {
        arr[i] = ((arr[i] - min) * 100) / (max - min);
    }
}

void demonstrateNormalize() {
    int numbers[5] = {15, 35, 25, 45, 30};
    
    std::cout << "Original: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    normalize(numbers, 5);
    
    std::cout << "Normalized (0-100): ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

---

## 🔀 Reordering Operations

### Reverse Array

```cpp
void reverseArray(int arr[], int size) {
    for (int i = 0; i < size / 2; i++) {
        // Swap elements
        int temp = arr[i];
        arr[i] = arr[size - 1 - i];
        arr[size - 1 - i] = temp;
    }
}

void demonstrateReverse() {
    int numbers[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Original: ";
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

### Shuffle Array

```cpp
#include <cstdlib>
#include <ctime>

void shuffleArray(int arr[], int size) {
    // Seed random number generator
    std::srand(std::time(nullptr));
    
    for (int i = size - 1; i > 0; i--) {
        // Generate random index from 0 to i
        int j = std::rand() % (i + 1);
        
        // Swap arr[i] and arr[j]
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}

void demonstrateShuffle() {
    int numbers[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    
    std::cout << "Original: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    shuffleArray(numbers, 10);
    
    std::cout << "Shuffled: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Rotate Array

```cpp
void rotateLeft(int arr[], int size, int positions) {
    positions = positions % size;  // Handle positions > size
    
    // Store first positions elements
    int* temp = new int[positions];
    for (int i = 0; i < positions; i++) {
        temp[i] = arr[i];
    }
    
    // Shift remaining elements left
    for (int i = 0; i < size - positions; i++) {
        arr[i] = arr[i + positions];
    }
    
    // Put stored elements at the end
    for (int i = 0; i < positions; i++) {
        arr[size - positions + i] = temp[i];
    }
    
    delete[] temp;
}

void demonstrateRotate() {
    int numbers[5] = {1, 2, 3, 4, 5};
    
    std::cout << "Original: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    rotateLeft(numbers, 5, 2);
    
    std::cout << "Rotated left by 2: ";
    for (int num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

---

## 📝 Copy and Merge Operations

### Copy Array

```cpp
void copyArray(int source[], int destination[], int size) {
    for (int i = 0; i < size; i++) {
        destination[i] = source[i];
    }
}

void demonstrateCopy() {
    int source[5] = {1, 2, 3, 4, 5};
    int destination[5];
    
    copyArray(source, destination, 5);
    
    std::cout << "Source: ";
    for (int num : source) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    std::cout << "Destination: ";
    for (int num : destination) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
```

### Merge Two Sorted Arrays

```cpp
int mergeSortedArrays(int arr1[], int size1, int arr2[], int size2, int result[]) {
    int i = 0, j = 0, k = 0;
    
    while (i < size1 && j < size2) {
        if (arr1[i] <= arr2[j]) {
            result[k++] = arr1[i++];
        } else {
            result[k++] = arr2[j++];
        }
    }
    
    // Copy remaining elements from arr1
    while (i < size1) {
        result[k++] = arr1[i++];
    }
    
    // Copy remaining elements from arr2
    while (j < size2) {
        result[k++] = arr2[j++];
    }
    
    return k;  // Return merged size
}

void demonstrateMerge() {
    int arr1[5] = {1, 3, 5, 7, 9};
    int arr2[4] = {2, 4, 6, 8};
    int result[9];
    
    int mergedSize = mergeSortedArrays(arr1, 5, arr2, 4, result);
    
    std::cout << "Array 1: ";
    for (int num : arr1) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    std::cout << "Array 2: ";
    for (int num : arr2) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    
    std::cout << "Merged: ";
    for (int i = 0; i < mergedSize; i++) {
        std::cout << result[i] << " ";
    }
    std::cout << std::endl;
}
```

---

## 🎭 Real-World Applications

### Student Grade Analysis

```cpp
void studentGradeAnalysis() {
    const int NUM_STUDENTS = 10;
    int grades[NUM_STUDENTS] = {85, 92, 78, 95, 88, 76, 83, 91, 79, 87};
    
    // Calculate statistics
    double average = calculateAverage(grades, NUM_STUDENTS);
    int highest = findMax(grades, NUM_STUDENTS);
    int lowest = findMin(grades, NUM_STUDENTS);
    
    // Sort grades
    bubbleSort(grades, NUM_STUDENTS);
    
    // Find median
    double median = findMedian(grades, NUM_STUDENTS);
    
    // Count grades above average
    int aboveAverage = 0;
    for (int i = 0; i < NUM_STUDENTS; i++) {
        if (grades[i] > average) {
            aboveAverage++;
        }
    }
    
    // Display results
    std::cout << "Grade Analysis:" << std::endl;
    std::cout << "Average: " << average << std::endl;
    std::cout << "Median: " << median << std::endl;
    std::cout << "Highest: " << highest << std::endl;
    std::cout << "Lowest: " << lowest << std::endl;
    std::cout << "Above average: " << aboveAverage << " students" << std::endl;
    
    std::cout << "Sorted grades: ";
    for (int grade : grades) {
        std::cout << grade << " ";
    }
    std::cout << std::endl;
}
```

### Inventory Management

```cpp
void inventoryManagement() {
    const int NUM_PRODUCTS = 8;
    int productIds[NUM_PRODUCTS] = {1001, 1002, 1003, 1004, 1005, 1006, 1007, 1008};
    int stock[NUM_PRODUCTS] = {15, 50, 30, 10, 25, 40, 20, 35};
    double prices[NUM_PRODUCTS] = {999.99, 29.99, 79.99, 299.99, 149.99, 59.99, 89.99, 39.99};
    
    // Find products with low stock
    int lowStockThreshold = 25;
    int lowStockIndices[NUM_PRODUCTS];
    int lowStockCount = 0;
    
    for (int i = 0; i < NUM_PRODUCTS; i++) {
        if (stock[i] < lowStockThreshold) {
            lowStockIndices[lowStockCount++] = i;
        }
    }
    
    // Calculate total inventory value
    double totalValue = 0;
    for (int i = 0; i < NUM_PRODUCTS; i++) {
        totalValue += stock[i] * prices[i];
    }
    
    // Sort products by stock (descending)
    for (int i = 0; i < NUM_PRODUCTS - 1; i++) {
        for (int j = 0; j < NUM_PRODUCTS - i - 1; j++) {
            if (stock[j] < stock[j + 1]) {
                // Swap stock
                int tempStock = stock[j];
                stock[j] = stock[j + 1];
                stock[j + 1] = tempStock;
                
                // Swap product IDs
                int tempId = productIds[j];
                productIds[j] = productIds[j + 1];
                productIds[j + 1] = tempId;
                
                // Swap prices
                double tempPrice = prices[j];
                prices[j] = prices[j + 1];
                prices[j + 1] = tempPrice;
            }
        }
    }
    
    // Display results
    std::cout << "Inventory Management:" << std::endl;
    std::cout << "Total inventory value: $" << totalValue << std::endl;
    std::cout << "Products with low stock (< " << lowStockThreshold << "):" << std::endl;
    
    for (int i = 0; i < lowStockCount; i++) {
        int idx = lowStockIndices[i];
        std::cout << "Product " << productIds[idx] << ": " << stock[idx] << " units" << std::endl;
    }
    
    std::cout << "Products sorted by stock (descending):" << std::endl;
    for (int i = 0; i < NUM_PRODUCTS; i++) {
        std::cout << "ID: " << productIds[i] << ", Stock: " << stock[i] 
                 << ", Price: $" << prices[i] << std::endl;
    }
}
```

---

## ⚠️ Common Operation Mistakes

### 1. Off-by-One Errors in Loops

```cpp
// ❌ WRONG - Goes out of bounds
for (int i = 0; i <= size; i++) {  // Should be i < size
    // Process arr[i]
}

// ✅ CORRECT
for (int i = 0; i < size; i++) {
    // Process arr[i]
}
```

### 2. Not Handling Empty Arrays

```cpp
// ❌ WRONG - May access arr[0] when array is empty
int min = arr[0];
for (int i = 1; i < size; i++) {
    if (arr[i] < min) min = arr[i];
}

// ✅ CORRECT - Check for empty array
if (size == 0) {
    // Handle empty array case
} else {
    int min = arr[0];
    for (int i = 1; i < size; i++) {
        if (arr[i] < min) min = arr[i];
    }
}
```

### 3. Modifying Array While Iterating

```cpp
// ❌ WRONG - Can cause issues with some operations
for (int i = 0; i < size; i++) {
    if (arr[i] == target) {
        // Remove element and shift others
        for (int j = i; j < size - 1; j++) {
            arr[j] = arr[j + 1];
        }
        size--;  // This can cause the loop to skip elements
    }
}

// ✅ CORRECT - Use separate index for result
int resultSize = 0;
for (int i = 0; i < size; i++) {
    if (arr[i] != target) {
        result[resultSize++] = arr[i];
    }
}
```

---

## 🛡️ Best Practices

### 1. Use Constants for Array Sizes

```cpp
const int MAX_SIZE = 100;
int arr[MAX_SIZE];
```

### 2. Check Array Bounds

```cpp
if (index >= 0 && index < size) {
    // Safe to access arr[index]
}
```

### 3. Handle Edge Cases

```cpp
if (size == 0) {
    // Handle empty array
    return defaultValue;
}
```

### 4. Use Meaningful Variable Names

```cpp
int studentScores[NUM_STUDENTS];
double temperatures[DAYS_IN_MONTH];
```

### 5. Document Complex Operations

```cpp
// Find median using quickselect algorithm (O(n) average case)
int findMedian(int arr[], int size) {
    // Implementation details...
}
```

---

## 📊 Performance Considerations

### Algorithm Complexity

| Operation | Time Complexity | Space Complexity |
|-----------|------------------|-------------------|
| Linear Search | O(n) | O(1) |
| Binary Search | O(log n) | O(1) |
| Bubble Sort | O(n²) | O(1) |
| Selection Sort | O(n²) | O(1) |
| Insertion Sort | O(n²) | O(1) |
| Merge Sort | O(n log n) | O(n) |

### Optimization Tips

```cpp
// Use early termination in search
int optimizedSearch(int arr[], int size, int target) {
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            return i;  // Early return when found
        }
    }
    return -1;
}

// Use efficient sorting for large arrays
// For small arrays (< 100), insertion sort is often faster
// For large arrays, use quicksort or mergesort
```

---

## 🎯 Key Takeaways

1. **Searching** - Linear search works for any array, binary search for sorted arrays
2. **Sorting** - Different algorithms have different performance characteristics
3. **Filtering** - Create new arrays with elements meeting specific criteria
4. **Transformation** - Apply functions to all elements (map operation)
5. **Reordering** - Reverse, shuffle, or rotate array elements
6. **Statistics** - Calculate sum, average, min, max, median
7. **Safety** - Always check bounds and handle edge cases
8. **Performance** - Choose appropriate algorithms based on array size and requirements

---

## 🔄 Complete Array Operations Guide

| Category | Operations | Use Case |
|----------|------------|----------|
| **Search** | Linear, Binary, Find All | Locate elements |
| **Sort** | Bubble, Selection, Insertion | Order elements |
| **Filter** | Greater Than, Remove Duplicates | Select elements |
| **Transform** | Map, Normalize, Scale | Modify all elements |
| **Reorder** | Reverse, Shuffle, Rotate | Change order |
| **Statistics** | Sum, Average, Min, Max, Median | Analyze data |
| **Merge** | Copy, Merge Sorted | Combine arrays |

---

## 🔄 Next Steps

Now that you understand array operations, let's explore how to pass arrays to functions:

*Continue reading: [Arrays and Functions](ArraysAndFunctions.md)*
