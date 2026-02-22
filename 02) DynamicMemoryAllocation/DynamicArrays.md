# 📊 Dynamic Arrays
### "Arrays that can grow and shrink at runtime"

---

## 🎯 Core Concept

**Dynamic arrays** are arrays whose size is determined at runtime rather than compile time. They are allocated on the heap and can be resized as needed.

### The Expandable House Analogy

```
Static Array = Fixed-size house (can't add rooms)
Dynamic Array = Expandable house (can add/remove rooms)

Static House:
┌─────┬─────┬─────┐
│Room1│Room2│Room3│  ← Fixed size
└─────┴─────┴─────┘

Dynamic House:
┌─────┬─────┬─────┬─────┐
│Room1│Room2│Room3│Room4│  ← Can add rooms
└─────┴─────┴─────┴─────┘
```

---

## 🏗️ Creating Dynamic Arrays

### Basic Syntax

```cpp
// Allocate dynamic array
data_type* array_name = new data_type[size];

// Deallocate dynamic array
delete[] array_name;
```

### Simple Dynamic Array

```cpp
#include <iostream>

void basicDynamicArray() {
    std::cout << "=== Basic Dynamic Array ===" << std::endl;
    
    int size = 5;
    int* arr = new int[size];  // Allocate array of 5 integers
    
    // Initialize the array
    for (int i = 0; i < size; i++) {
        arr[i] = i * 10;
    }
    
    // Display the array
    std::cout << "Array: ";
    for (int i = 0; i < size; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    // Clean up
    delete[] arr;
}
```

### User-Sized Dynamic Array

```cpp
void userSizedArray() {
    std::cout << "=== User-Sized Dynamic Array ===" << std::endl;
    
    int size;
    std::cout << "Enter array size: ";
    std::cin >> size;
    
    // Allocate based on user input
    int* arr = new int[size];
    
    // Fill with user input
    for (int i = 0; i < size; i++) {
        std::cout << "Enter value " << (i + 1) << ": ";
        std::cin >> arr[i];
    }
    
    // Display results
    std::cout << "Your array: ";
    for (int i = 0; i < size; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    // Calculate sum
    int sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    std::cout << "Sum: " << sum << std::endl;
    
    // Clean up
    delete[] arr;
}
```

---

## 🔀 Dynamic Array Operations

### Traversal

```cpp
void traverseDynamicArray() {
    std::cout << "=== Dynamic Array Traversal ===" << std::endl;
    
    int* arr = new int[5];
    for (int i = 0; i < 5; i++) {
        arr[i] = i + 1;
    }
    
    std::cout << "Forward traversal: ";
    for (int i = 0; i < 5; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    std::cout << "Reverse traversal: ";
    for (int i = 4; i >= 0; i--) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    delete[] arr;
}
```

### Searching

```cpp
int* findElement(int* arr, int size, int target) {
    for (int i = 0; i < size; i++) {
        if (arr[i] == target) {
            return &arr[i];  // Return pointer to found element
        }
    }
    return nullptr;  // Not found
}

void searchDynamicArray() {
    std::cout << "=== Dynamic Array Search ===" << std::endl;
    
    int* arr = new int[5];
    arr[0] = 10; arr[1] = 20; arr[2] = 30; arr[3] = 40; arr[4] = 50;
    
    int target = 30;
    int* found = findElement(arr, 5, target);
    
    if (found) {
        std::cout << "Found " << target << " at index " << (found - arr) << std::endl;
    } else {
        std::cout << target << " not found" << std::endl;
    }
    
    delete[] arr;
}
```

### Sorting

```cpp
void bubbleSort(int* arr, int size) {
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

void sortDynamicArray() {
    std::cout << "=== Dynamic Array Sorting ===" << std::endl;
    
    int* arr = new int[5];
    arr[0] = 50; arr[1] = 20; arr[2] = 40; arr[3] = 10; arr[4] = 30;
    
    std::cout << "Before sorting: ";
    for (int i = 0; i < 5; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    bubbleSort(arr, 5);
    
    std::cout << "After sorting: ";
    for (int i = 0; i < 5; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    delete[] arr;
}
```

---

## 🔄 Resizing Dynamic Arrays

### Manual Resizing

```cpp
int* resizeArray(int* oldArray, int oldSize, int newSize) {
    // Allocate new array
    int* newArray = new int[newSize];
    
    // Copy elements from old array
    int copySize = (oldSize < newSize) ? oldSize : newSize;
    for (int i = 0; i < copySize; i++) {
        newArray[i] = oldArray[i];
    }
    
    // Delete old array
    delete[] oldArray;
    
    return newArray;
}

void manualResizing() {
    std::cout << "=== Manual Array Resizing ===" << std::endl;
    
    int* arr = new int[3];
    arr[0] = 10; arr[1] = 20; arr[2] = 30;
    
    std::cout << "Original array: ";
    for (int i = 0; i < 3; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    // Resize to larger array
    arr = resizeArray(arr, 3, 5);
    arr[3] = 40; arr[4] = 50;
    
    std::cout << "Resized array: ";
    for (int i = 0; i < 5; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    // Resize to smaller array
    arr = resizeArray(arr, 5, 2);
    
    std::cout << "Smaller array: ";
    for (int i = 0; i < 2; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    delete[] arr;
}
```

### Dynamic Array Class

```cpp
class DynamicArray {
private:
    int* data;
    int capacity;
    int size;
    
    void resize(int newCapacity) {
        int* newData = new int[newCapacity];
        
        for (int i = 0; i < size; i++) {
            newData[i] = data[i];
        }
        
        delete[] data;
        data = newData;
        capacity = newCapacity;
    }
    
public:
    DynamicArray(int initialCapacity = 10) 
        : capacity(initialCapacity), size(0) {
        data = new int[capacity];
    }
    
    ~DynamicArray() {
        delete[] data;
    }
    
    void add(int value) {
        if (size >= capacity) {
            resize(capacity * 2);  // Double capacity
        }
        
        data[size] = value;
        size++;
    }
    
    int get(int index) const {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of range");
        }
        return data[index];
    }
    
    void set(int index, int value) {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of range");
        }
        data[index] = value;
    }
    
    int getSize() const { return size; }
    
    void display() const {
        std::cout << "Array: ";
        for (int i = 0; i < size; i++) {
            std::cout << data[i] << " ";
        }
        std::cout << std::endl;
    }
};

void dynamicArrayClass() {
    std::cout << "=== Dynamic Array Class ===" << std::endl;
    
    DynamicArray arr(3);  // Initial capacity of 3
    
    // Add elements
    arr.add(10);
    arr.add(20);
    arr.add(30);
    arr.display();
    
    std::cout << "Size: " << arr.getSize() << std::endl;
    
    // Add more elements (will trigger resize)
    arr.add(40);
    arr.add(50);
    arr.display();
    
    std::cout << "Size: " << arr.getSize() << std::endl;
    
    // Access elements
    std::cout << "Element at index 2: " << arr.get(2) << std::endl;
    
    // Modify elements
    arr.set(1, 99);
    arr.display();
}
```

---

## 🎭 Multi-Dimensional Dynamic Arrays

### 2D Dynamic Array

```cpp
void twoDDynamicArray() {
    std::cout << "=== 2D Dynamic Array ===" << std::endl;
    
    int rows = 3;
    int cols = 4;
    
    // Allocate array of pointers
    int** matrix = new int*[rows];
    
    // Allocate each row
    for (int i = 0; i < rows; i++) {
        matrix[i] = new int[cols];
    }
    
    // Fill the matrix
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            matrix[i][j] = i * cols + j + 1;
        }
    }
    
    // Display the matrix
    std::cout << "Matrix:" << std::endl;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            std::cout << matrix[i][j] << "\t";
        }
        std::cout << std::endl;
    }
    
    // Clean up (reverse order)
    for (int i = 0; i < rows; i++) {
        delete[] matrix[i];
    }
    delete[] matrix;
}
```

### 3D Dynamic Array

```cpp
void threeDDynamicArray() {
    std::cout << "=== 3D Dynamic Array ===" << std::endl;
    
    int x = 2, y = 3, z = 4;
    
    // Allocate 3D array
    int*** cube = new int**[x];
    for (int i = 0; i < x; i++) {
        cube[i] = new int*[y];
        for (int j = 0; j < y; j++) {
            cube[i][j] = new int[z];
        }
    }
    
    // Fill the cube
    for (int i = 0; i < x; i++) {
        for (int j = 0; j < y; j++) {
            for (int k = 0; k < z; k++) {
                cube[i][j][k] = i * y * z + j * z + k + 1;
            }
        }
    }
    
    // Display the cube
    std::cout << "3D Cube:" << std::endl;
    for (int i = 0; i < x; i++) {
        std::cout << "Layer " << i << ":" << std::endl;
        for (int j = 0; j < y; j++) {
            for (int k = 0; k < z; k++) {
                std::cout << cube[i][j][k] << " ";
            }
            std::cout << std::endl;
        }
        std::cout << std::endl;
    }
    
    // Clean up (reverse order)
    for (int i = 0; i < x; i++) {
        for (int j = 0; j < y; j++) {
            delete[] cube[i][j];
        }
        delete[] cube[i];
    }
    delete[] cube;
}
```

---

## 🎯 Real-World Applications

### Student Grade Management

```cpp
class GradeManager {
private:
    std::string* studentNames;
    double* grades;
    int* studentIds;
    int capacity;
    int count;
    
    void resize() {
        int newCapacity = capacity * 2;
        
        // Create new arrays
        std::string* newNames = new std::string[newCapacity];
        double* newGrades = new double[newCapacity];
        int* newIds = new int[newCapacity];
        
        // Copy old data
        for (int i = 0; i < count; i++) {
            newNames[i] = studentNames[i];
            newGrades[i] = grades[i];
            newIds[i] = studentIds[i];
        }
        
        // Clean up old arrays
        delete[] studentNames;
        delete[] grades;
        delete[] studentIds;
        
        // Assign new arrays
        studentNames = newNames;
        grades = newGrades;
        studentIds = newIds;
        capacity = newCapacity;
    }
    
public:
    GradeManager(int initialCapacity = 10) 
        : capacity(initialCapacity), count(0) {
        studentNames = new std::string[capacity];
        grades = new double[capacity];
        studentIds = new int[capacity];
    }
    
    ~GradeManager() {
        delete[] studentNames;
        delete[] grades;
        delete[] studentIds;
    }
    
    void addStudent(const std::string& name, double grade, int id) {
        if (count >= capacity) {
            resize();
        }
        
        studentNames[count] = name;
        grades[count] = grade;
        studentIds[count] = id;
        count++;
    }
    
    void displayStudents() const {
        std::cout << "=== Student Grades ===" << std::endl;
        std::cout << "ID\tName\t\tGrade" << std::endl;
        std::cout << "--------------------------------" << std::endl;
        
        for (int i = 0; i < count; i++) {
            std::cout << studentIds[i] << "\t" 
                     << studentNames[i] << "\t\t" 
                     << grades[i] << std::endl;
        }
    }
    
    double calculateAverage() const {
        if (count == 0) return 0.0;
        
        double sum = 0.0;
        for (int i = 0; i < count; i++) {
            sum += grades[i];
        }
        return sum / count;
    }
    
    void findHighestGrade() const {
        if (count == 0) return;
        
        int highestIndex = 0;
        for (int i = 1; i < count; i++) {
            if (grades[i] > grades[highestIndex]) {
                highestIndex = i;
            }
        }
        
        std::cout << "Highest grade: " << studentNames[highestIndex] 
                 << " (" << grades[highestIndex] << ")" << std::endl;
    }
};

void gradeManagementExample() {
    std::cout << "=== Grade Management Example ===" << std::endl;
    
    GradeManager manager;
    
    manager.addStudent("Alice", 95.5, 1001);
    manager.addStudent("Bob", 87.0, 1002);
    manager.addStudent("Charlie", 92.3, 1003);
    manager.addStudent("Diana", 88.7, 1004);
    manager.addStudent("Eve", 91.2, 1005);
    
    manager.displayStudents();
    
    std::cout << "Average grade: " << manager.calculateAverage() << std::endl;
    manager.findHighestGrade();
}
```

### Image Processing

```cpp
class Image {
private:
    int** pixels;
    int width;
    int height;
    
public:
    Image(int w, int h) : width(w), height(h) {
        // Allocate 2D array for pixels
        pixels = new int*[height];
        for (int i = 0; i < height; i++) {
            pixels[i] = new int[width];
        }
        
        // Initialize with gradient
        for (int y = 0; y < height; y++) {
            for (int x = 0; x < width; x++) {
                pixels[y][x] = (x + y) % 256;  // Simple gradient
            }
        }
    }
    
    ~Image() {
        for (int i = 0; i < height; i++) {
            delete[] pixels[i];
        }
        delete[] pixels;
    }
    
    void setPixel(int x, int y, int color) {
        if (x >= 0 && x < width && y >= 0 && y < height) {
            pixels[y][x] = color;
        }
    }
    
    int getPixel(int x, int y) const {
        if (x >= 0 && x < width && y >= 0 && y < height) {
            return pixels[y][x];
        }
        return 0;
    }
    
    void applyFilter(int (*filter)(int)) {
        for (int y = 0; y < height; y++) {
            for (int x = 0; x < width; x++) {
                pixels[y][x] = filter(pixels[y][x]);
            }
        }
    }
    
    void display() const {
        std::cout << "Image (" << width << "x" << height << "):" << std::endl;
        // Display first few pixels as example
        for (int y = 0; y < std::min(3, height); y++) {
            for (int x = 0; x < std::min(5, width); x++) {
                std::cout << pixels[y][x] << " ";
            }
            std::cout << std::endl;
        }
    }
};

// Example filters
int invertFilter(int pixel) {
    return 255 - pixel;
}

int brightnessFilter(int pixel) {
    return (pixel + 50) % 256;
}

void imageProcessingExample() {
    std::cout << "=== Image Processing Example ===" << std::endl;
    
    Image img(10, 8);
    img.display();
    
    std::cout << "Applying invert filter..." << std::endl;
    img.applyFilter(invertFilter);
    img.display();
    
    std::cout << "Applying brightness filter..." << std::endl;
    img.applyFilter(brightnessFilter);
    img.display();
}
```

---

## ⚠️ Common Mistakes

### 1. Forgetting to Delete

```cpp
void forgetToDelete() {
    int* arr = new int[5];
    // ❌ Forgot to delete[] arr!
    // Memory leak!
}
```

### 2. Wrong Delete

```cpp
void wrongDelete() {
    int* arr = new int[5];
    delete arr;        // ❌ Wrong! Should be delete[]
    // delete[] arr;   // ✅ Correct
}
```

### 3. Array Index Out of Bounds

```cpp
void outOfBounds() {
    int* arr = new int[5];
    arr[0] = 10;  // ✅ Valid
    arr[4] = 50;  // ✅ Valid
    arr[5] = 60;  // ❌ Out of bounds!
    arr[-1] = 5;  // ❌ Out of bounds!
    
    delete[] arr;
}
```

### 4. Memory Fragmentation

```cpp
void fragmentation() {
    int* ptr1 = new int[100];
    int* ptr2 = new int[200];
    int* ptr3 = new int[50];
    
    delete[] ptr1;  // Leaves gap in memory
    delete[] ptr3;  // Leaves another gap
    
    delete[] ptr2;
    // Memory becomes fragmented over time
}
```

### 5. Dangling Pointer After Resize

```cpp
void danglingAfterResize() {
    int* arr = new int[3];
    arr[0] = 10; arr[1] = 20; arr[2] = 30;
    
    int* oldPtr = arr;  // Save pointer to old array
    arr = resizeArray(arr, 3, 5);  // arr points to new array
    
    std::cout << oldPtr[0];  // ❌ oldPtr is dangling!
    
    delete[] arr;
}
```

---

## 🛡️ Best Practices

### Use RAII Wrappers

```cpp
#include <vector>

void raiiDynamicArrays() {
    std::cout << "=== RAII Dynamic Arrays ===" << std::endl;
    
    // vector handles memory automatically
    std::vector<int> vec;
    vec.push_back(10);
    vec.push_back(20);
    vec.push_back(30);
    
    std::cout << "Vector: ";
    for (int val : vec) {
        std::cout << val << " ";
    }
    std::cout << std::endl;
    
    // No manual delete needed!
}
```

### Use Smart Pointers

```cpp
#include <memory>

void smartPointerArrays() {
    std::cout << "=== Smart Pointer Arrays ===" << std::endl;
    
    // unique_ptr with arrays
    std::unique_ptr<int[]> arr = std::make_unique<int[]>(5);
    
    for (int i = 0; i < 5; i++) {
        arr[i] = i * 10;
    }
    
    std::cout << "Smart array: ";
    for (int i = 0; i < 5; i++) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
    
    // Automatic cleanup!
}
```

### Bounds Checking

```cpp
class SafeArray {
private:
    int* data;
    int size;
    
public:
    SafeArray(int s) : size(s) {
        data = new int[size];
    }
    
    ~SafeArray() {
        delete[] data;
    }
    
    int& operator[](int index) {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        return data[index];
    }
    
    const int& operator[](int index) const {
        if (index < 0 || index >= size) {
            throw std::out_of_range("Index out of bounds");
        }
        return data[index];
    }
    
    int getSize() const { return size; }
};

void boundsCheckingExample() {
    std::cout << "=== Bounds Checking Example ===" << std::endl;
    
    SafeArray arr(3);
    arr[0] = 10; arr[1] = 20; arr[2] = 30;
    
    try {
        arr[1] = 99;  // ✅ Valid
        std::cout << "Set arr[1] = " << arr[1] << std::endl;
        
        arr[5] = 100;  // ❌ Will throw exception
    } catch (const std::out_of_range& e) {
        std::cout << "Error: " << e.what() << std::endl;
    }
}
```

---

## 📊 Performance Considerations

### Allocation vs Reallocation

```cpp
#include <chrono>

void performanceTest() {
    const int ITERATIONS = 1000;
    
    // Test allocation speed
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < ITERATIONS; i++) {
        int* arr = new int[100];
        delete[] arr;
    }
    auto allocationTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    // Test reallocation speed
    start = std::chrono::high_resolution_clock::now();
    int* arr = new int[100];
    for (int i = 0; i < ITERATIONS; i++) {
        int* newArr = new int[200];
        for (int j = 0; j < 100; j++) {
            newArr[j] = arr[j];
        }
        delete[] arr;
        arr = newArr;
    }
    delete[] arr;
    auto reallocationTime = std::chrono::duration_cast<std::chrono::microseconds>(
        std::chrono::high_resolution_clock::now() - start
    ).count();
    
    std::cout << "Allocation time: " << allocationTime << " microseconds" << std::endl;
    std::cout << "Reallocation time: " << reallocationTime << " microseconds" << std::endl;
    std::cout << "Reallocation is " << (double)reallocationTime / allocationTime 
             << "x slower" << std::endl;
}
```

### Cache Efficiency

```cpp
void cacheEfficiency() {
    std::cout << "=== Cache Efficiency ===" << std::endl;
    
    const int SIZE = 1000;
    int* arr = new int[SIZE];
    
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
    
    delete[] arr;
}
```

---

## 🎯 Key Takeaways

1. **Dynamic arrays** are allocated on the heap with `new[]`
2. **Always delete** with `delete[]` to prevent leaks
3. **Resizing** requires allocating new array and copying data
4. **Multi-dimensional** arrays need nested allocation
5. **Bounds checking** prevents crashes but adds overhead
6. **RAII patterns** (smart pointers, containers) are safer
7. **Cache efficiency** matters for large arrays
8. **Memory fragmentation** can occur with frequent resizing

---

## 🔄 Complete Dynamic Array Guide

| Operation | Syntax | Purpose | Example |
|-----------|---------|---------|---------|
| Allocate | `new Type[size]` | Create array | `new int[10]` |
| Deallocate | `delete[] ptr` | Free array | `delete[] arr` |
| Access | `arr[index]` | Get element | `arr[0]` |
| Resize | Manual copy | Change size | `resizeArray(arr, old, new)` |

---

## 🔄 Next Steps

Now that you understand dynamic arrays, let's explore how they compare to standard containers:

*Continue reading: [Dynamic Arrays vs Standard Containers](DynamicArraysVsContainers.md)*
