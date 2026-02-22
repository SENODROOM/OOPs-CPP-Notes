# 🗺️ 2D Arrays in C++
### "A grid of boxes — like a spreadsheet or a chessboard."

---

## 🤔 Why 2D Arrays?

A 1D array is like a single row of boxes. But what if you need a **table**?

- A **seating chart** (rows and columns of seats)
- A **game board** (like tic-tac-toe or chess)
- A **spreadsheet** (rows of students, columns of their subject grades)
- A **pixel grid** (rows and columns of pixels = an image)

For all of these, you need a **2D array** — an array of arrays!

---

## 🧠 Visualizing a 2D Array

Think of it as a **table with rows and columns**:

```
        Col 0  Col 1  Col 2
Row 0:  [ 10 ] [ 20 ] [ 30 ]
Row 1:  [ 40 ] [ 50 ] [ 60 ]
Row 2:  [ 70 ] [ 80 ] [ 90 ]
```

This is a 3×3 grid (3 rows, 3 columns = 9 total elements).

To access any element, you need TWO numbers: **row** and **column**.

---

## 📝 Declaring a 2D Array

```cpp
// Syntax: DataType arrayName[rows][columns];

int grid[3][3];        // 3 rows, 3 columns (9 elements total)
int matrix[4][5];      // 4 rows, 5 columns (20 elements total)
double table[2][6];    // 2 rows, 6 columns
```

---

## 🌟 Initializing a 2D Array

### Method 1: Grouped by rows (clearest)
```cpp
int grid[3][3] = {
    {10, 20, 30},   // Row 0
    {40, 50, 60},   // Row 1
    {70, 80, 90}    // Row 2
};
```

### Method 2: All on one line
```cpp
int grid[3][3] = {10, 20, 30, 40, 50, 60, 70, 80, 90};
// C++ fills row by row automatically
```

### Method 3: All zeros
```cpp
int grid[3][3] = {0};   // every cell is 0
int grid[3][3] = {};    // every cell is 0
```

---

## 🔢 Accessing Elements

You need **two indices**: `array[row][column]`

```cpp
int grid[3][3] = {
    {10, 20, 30},
    {40, 50, 60},
    {70, 80, 90}
};

cout << grid[0][0];   // 10  (row 0, col 0)
cout << grid[0][1];   // 20  (row 0, col 1)
cout << grid[0][2];   // 30  (row 0, col 2)
cout << grid[1][0];   // 40  (row 1, col 0)
cout << grid[1][1];   // 50  (row 1, col 1)  ← CENTER
cout << grid[2][2];   // 90  (row 2, col 2)  ← BOTTOM-RIGHT
```

```
         [0]    [1]    [2]
[0]  ┌────────┬────────┬────────┐
     │  10    │  20    │  30    │
[1]  ├────────┼────────┼────────┤
     │  40    │  50    │  60    │
[2]  ├────────┼────────┼────────┤
     │  70    │  80    │  90    │
     └────────┴────────┴────────┘

grid[1][2] = 60   (row 1, column 2)
grid[2][0] = 70   (row 2, column 0)
```

---

## ✏️ Modifying Elements

```cpp
int grid[3][3] = {
    {10, 20, 30},
    {40, 50, 60},
    {70, 80, 90}
};

grid[1][1] = 999;   // Change center element

cout << grid[1][1]; // 999
```

---

## 🔁 Looping Through a 2D Array

You need **two nested loops**: one for rows, one for columns.

```cpp
int grid[3][3] = {
    {10, 20, 30},
    {40, 50, 60},
    {70, 80, 90}
};

for (int row = 0; row < 3; row++) {
    for (int col = 0; col < 3; col++) {
        cout << grid[row][col] << "\t";   // \t = tab for spacing
    }
    cout << endl;   // new line after each row
}
```

**Output:**
```
10    20    30
40    50    60
70    80    90
```

---

## 🎮 Real Example 1 — Tic-Tac-Toe Board

```cpp
#include <iostream>
using namespace std;

int main() {
    char board[3][3] = {
        {'X', 'O', 'X'},
        {'O', 'X', 'O'},
        {'X', 'O', 'X'}
    };

    // Display the board
    cout << "Tic-Tac-Toe Board:" << endl;
    cout << "-------------" << endl;

    for (int row = 0; row < 3; row++) {
        cout << "| ";
        for (int col = 0; col < 3; col++) {
            cout << board[row][col] << " | ";
        }
        cout << endl;
        cout << "-------------" << endl;
    }

    return 0;
}
```

**Output:**
```
Tic-Tac-Toe Board:
-------------
| X | O | X |
-------------
| O | X | O |
-------------
| X | O | X |
-------------
```

---

## 🎓 Real Example 2 — Student Grade Table

Imagine 3 students, each with 4 subject grades.

```cpp
#include <iostream>
using namespace std;

int main() {
    // 3 students, 4 subjects each
    int grades[3][4] = {
        {85, 90, 78, 92},   // Student 0: Math, Science, English, History
        {76, 88, 95, 70},   // Student 1
        {91, 83, 87, 94}    // Student 2
    };

    string students[] = {"Alice", "Bob", "Charlie"};
    string subjects[] = {"Math", "Science", "English", "History"};

    // Print table header
    cout << "Student\t\t";
    for (int j = 0; j < 4; j++)
        cout << subjects[j] << "\t";
    cout << "Average" << endl;
    cout << "-------------------------------------------------------" << endl;

    // Print each student
    for (int i = 0; i < 3; i++) {
        cout << students[i] << "\t\t";

        int sum = 0;
        for (int j = 0; j < 4; j++) {
            cout << grades[i][j] << "\t";
            sum += grades[i][j];
        }
        cout << (double)sum / 4 << endl;
    }

    return 0;
}
```

**Output:**
```
Student         Math    Science English History Average
-------------------------------------------------------
Alice           85      90      78      92      86.25
Bob             76      88      95      70      82.25
Charlie         91      83      87      94      88.75
```

---

## 🏗️ Dynamic 2D Arrays (Using DMA)

When rows and columns aren't known until runtime:

```cpp
#include <iostream>
using namespace std;

int main() {
    int rows, cols;

    cout << "Enter rows: ";    cin >> rows;
    cout << "Enter columns: "; cin >> cols;

    // Step 1: Create an array of ROW pointers
    int** matrix = new int*[rows];

    // Step 2: Each row pointer points to an array of COLUMNS
    for (int i = 0; i < rows; i++) {
        matrix[i] = new int[cols];
    }

    // Step 3: Fill the matrix
    cout << "Enter values:" << endl;
    for (int i = 0; i < rows; i++)
        for (int j = 0; j < cols; j++)
            cin >> matrix[i][j];

    // Step 4: Display
    cout << "\nMatrix:" << endl;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++)
            cout << matrix[i][j] << "\t";
        cout << endl;
    }

    // Step 5: FREE MEMORY (in reverse order!)
    for (int i = 0; i < rows; i++)
        delete[] matrix[i];    // free each row
    delete[] matrix;           // free the row pointers
    matrix = nullptr;

    return 0;
}
```

### Memory Layout of Dynamic 2D Array

```
matrix (pointer to pointers)
    │
    ├──► [ row 0 array ] → [val][val][val][val]
    ├──► [ row 1 array ] → [val][val][val][val]
    └──► [ row 2 array ] → [val][val][val][val]
```

---

## 🔢 Matrix Operations

### Matrix Addition
```cpp
int A[2][2] = {{1, 2}, {3, 4}};
int B[2][2] = {{5, 6}, {7, 8}};
int C[2][2];

for (int i = 0; i < 2; i++)
    for (int j = 0; j < 2; j++)
        C[i][j] = A[i][j] + B[i][j];

// C = {{6, 8}, {10, 12}}
```

### Matrix Transpose (flip rows and columns)
```cpp
int A[3][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

int T[3][3];
for (int i = 0; i < 3; i++)
    for (int j = 0; j < 3; j++)
        T[j][i] = A[i][j];   // swap row and column!

// T = {{1,4,7}, {2,5,8}, {3,6,9}}
```

---

## 📬 Passing 2D Arrays to Functions

```cpp
// Must specify the number of COLUMNS (rows can be omitted)
void printMatrix(int arr[][3], int rows) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < 3; j++)
            cout << arr[i][j] << " ";
        cout << endl;
    }
}

int main() {
    int m[2][3] = {{1, 2, 3}, {4, 5, 6}};
    printMatrix(m, 2);
}
```

---

## ⚠️ Common Mistakes

```cpp
// ❌ Accessing out of bounds
int arr[3][3];
arr[3][0] = 5;    // Row 3 doesn't exist! (0,1,2 only)
arr[0][3] = 5;    // Column 3 doesn't exist!

// ❌ Wrong order of indices
arr[col][row]     // Should be arr[row][col]!

// ✅ Always: arr[ROW][COLUMN]
```

---

## 📊 2D Array Quick Reference

| Syntax                        | Meaning                            |
|-------------------------------|------------------------------------|
| `int a[3][4]`                 | 3 rows, 4 columns                  |
| `a[0][0]`                     | First element (top-left)           |
| `a[rows-1][cols-1]`           | Last element (bottom-right)        |
| `a[i][j]`                     | Element at row i, column j         |
| Nested for loops              | Used to traverse all elements      |
| Total elements                | rows × columns                     |

---

## 🎯 Key Takeaways

1. A 2D array is an **array of arrays** — a table with rows and columns
2. Access elements with **two indices**: `arr[row][column]`
3. **Rows and columns both start at index 0**
4. Use **nested loops** to traverse (outer = rows, inner = columns)
5. For functions, you MUST specify the number of columns
6. Dynamic 2D arrays use **pointer to pointers** (`int**`)
7. Free dynamic 2D memory by deleting **each row first**, then the row array

---
*Next up: Char Arrays — storing text the old-fashioned way!* →
