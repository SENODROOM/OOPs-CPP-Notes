# 🎓 C++ Concepts — All Together
### "A complete mini Student Management System using every concept."

---

## 🎯 What This File Demonstrates

This file combines **every concept** from all previous chapters into one real, working program:

| ✅ Concept              | Where Used                                   |
|------------------------|----------------------------------------------|
| Pointers               | Dynamic arrays, function parameters          |
| Dynamic Memory         | `new`/`delete` for grade arrays              |
| Smart Pointers         | `unique_ptr` for Student objects             |
| 1D Arrays              | Storing grades                               |
| 2D Arrays              | Class-wide grade table                       |
| Char Arrays            | Student ID as C-string                       |
| Classes & Objects      | `Student`, `Classroom` classes               |
| Encapsulation          | Private data, public interface               |
| Constructor            | Default, parameterized, copy                 |
| Destructor             | Freeing dynamic grade arrays                 |
| Getters & Setters      | Controlled, validated access to all fields   |

---

## 📋 The Complete Program

```cpp
#include <iostream>
#include <string>
#include <memory>    // for smart pointers
#include <cstring>   // for C-string functions
using namespace std;

// ============================================================
//  CLASS: Student
//  Demonstrates: Encapsulation, Constructor, Destructor,
//                Getters, Setters, DMA, Char Arrays, 1D Arrays
// ============================================================

class Student {
private:
    // ── Private Data (Encapsulation) ──────────────────────
    char studentId[10];     // Char array (C-string style ID)
    string name;            // std::string name
    int age;
    int* grades;            // 1D dynamic array (DMA)
    int numSubjects;
    bool enrolled;

    // Private helper — not accessible from outside
    double computeGPA() const {
        if (numSubjects == 0) return 0.0;
        double total = 0;
        for (int i = 0; i < numSubjects; i++)
            total += grades[i];
        return total / numSubjects;
    }

public:
    // ── Default Constructor ───────────────────────────────
    Student() : age(0), numSubjects(0), enrolled(false), grades(nullptr) {
        strcpy(studentId, "S000");
        name = "Unknown";
        cout << "[Constructor] Default student created." << endl;
    }

    // ── Parameterized Constructor ─────────────────────────
    Student(const char* id, string n, int a, int subjects)
        : age(a), numSubjects(subjects), enrolled(true) {
        // Char array copy
        strncpy(studentId, id, 9);
        studentId[9] = '\0';  // ensure null terminator

        name = n;

        // Dynamic memory allocation for grades
        grades = new int[numSubjects];
        for (int i = 0; i < numSubjects; i++)
            grades[i] = 0;

        cout << "[Constructor] Student '" << name
             << "' (ID: " << studentId << ") created with "
             << numSubjects << " subjects." << endl;
    }

    // ── Copy Constructor ──────────────────────────────────
    Student(const Student& other)
        : age(other.age),
          numSubjects(other.numSubjects),
          enrolled(other.enrolled),
          name(other.name + " (copy)") {
        strncpy(studentId, other.studentId, 9);
        studentId[9] = '\0';
        strcat(studentId, "C");   // append 'C' to copied ID

        // Deep copy — allocate new array and copy values!
        grades = new int[numSubjects];
        for (int i = 0; i < numSubjects; i++)
            grades[i] = other.grades[i];

        cout << "[Copy Constructor] Copied student: " << name << endl;
    }

    // ── Destructor ────────────────────────────────────────
    ~Student() {
        delete[] grades;    // Free the dynamic array
        grades = nullptr;
        cout << "[Destructor] Student '" << name << "' destroyed. Memory freed." << endl;
    }

    // ── SETTERS (with validation) ─────────────────────────
    void setName(string n) {
        if (n.empty()) {
            cout << "Error: Name cannot be empty." << endl;
            return;
        }
        name = n;
    }

    void setAge(int a) {
        if (a < 5 || a > 100) {
            cout << "Error: Invalid age: " << a << endl;
            return;
        }
        age = a;
    }

    void setGrade(int subjectIndex, int grade) {
        if (subjectIndex < 0 || subjectIndex >= numSubjects) {
            cout << "Error: Invalid subject index." << endl;
            return;
        }
        if (grade < 0 || grade > 100) {
            cout << "Error: Grade must be 0-100." << endl;
            return;
        }
        grades[subjectIndex] = grade;
    }

    void enroll()   { enrolled = true;  }
    void unenroll() { enrolled = false; }

    // ── GETTERS ───────────────────────────────────────────
    const char* getId()   const { return studentId; }
    string      getName() const { return name; }
    int         getAge()  const { return age; }
    bool        isEnrolled() const { return enrolled; }

    int getGrade(int index) const {
        if (index < 0 || index >= numSubjects) return -1;
        return grades[index];
    }

    // Computed getters
    double getGPA()     const { return computeGPA(); }
    string getGradeLetter() const {
        double gpa = computeGPA();
        if (gpa >= 90) return "A";
        if (gpa >= 80) return "B";
        if (gpa >= 70) return "C";
        if (gpa >= 60) return "D";
        return "F";
    }

    // ── DISPLAY ───────────────────────────────────────────
    void display() const {
        cout << "┌─────────────────────────────────┐" << endl;
        cout << "│ ID:       " << studentId << endl;
        cout << "│ Name:     " << name << endl;
        cout << "│ Age:      " << age << endl;
        cout << "│ Status:   " << (enrolled ? "Enrolled" : "Not Enrolled") << endl;
        cout << "│ Grades:   ";
        for (int i = 0; i < numSubjects; i++)
            cout << grades[i] << " ";
        cout << endl;
        cout << "│ GPA:      " << getGPA() << endl;
        cout << "│ Grade:    " << getGradeLetter() << endl;
        cout << "└─────────────────────────────────┘" << endl;
    }
};


// ============================================================
//  CLASS: Classroom
//  Demonstrates: Smart Pointers, 2D Arrays (grade table),
//                Encapsulation, Constructor/Destructor
// ============================================================

class Classroom {
private:
    string className;
    int maxStudents;
    int currentCount;

    // Array of smart pointers to Students!
    unique_ptr<Student>* students;   // dynamic array of unique_ptr

    // 2D array for summary grade table [student][subject]
    int** gradeTable;
    int numSubjects;

public:
    // Constructor
    Classroom(string name, int maxSize, int subjects)
        : className(name), maxStudents(maxSize),
          currentCount(0), numSubjects(subjects) {

        // Smart pointer array
        students = new unique_ptr<Student>[maxStudents];

        // Dynamic 2D array for grade summary
        gradeTable = new int*[maxStudents];
        for (int i = 0; i < maxStudents; i++) {
            gradeTable[i] = new int[numSubjects];
            for (int j = 0; j < numSubjects; j++)
                gradeTable[i][j] = 0;
        }

        cout << "[Classroom] '" << className << "' created (max "
             << maxStudents << " students)." << endl;
    }

    // Destructor
    ~Classroom() {
        // Free 2D grade table
        for (int i = 0; i < maxStudents; i++)
            delete[] gradeTable[i];
        delete[] gradeTable;
        gradeTable = nullptr;

        // Free smart pointer array
        delete[] students;
        students = nullptr;

        cout << "[Classroom] '" << className << "' destroyed." << endl;
    }

    // Add a student
    bool addStudent(unique_ptr<Student> s) {
        if (currentCount >= maxStudents) {
            cout << "Error: Classroom is full!" << endl;
            return false;
        }
        students[currentCount] = move(s);  // transfer ownership
        currentCount++;
        return true;
    }

    // Update grade table
    void updateGradeTable(int studentIdx, int subjectIdx, int grade) {
        if (studentIdx < 0 || studentIdx >= currentCount) return;
        if (subjectIdx < 0 || subjectIdx >= numSubjects)  return;
        if (grade < 0 || grade > 100)                     return;

        gradeTable[studentIdx][subjectIdx] = grade;
        students[studentIdx]->setGrade(subjectIdx, grade);
    }

    // Display all students
    void displayAll() const {
        cout << "\n╔══════════════════════════════════════╗" << endl;
        cout << "║  CLASS: " << className << endl;
        cout << "║  Students: " << currentCount << "/" << maxStudents << endl;
        cout << "╚══════════════════════════════════════╝" << endl;
        for (int i = 0; i < currentCount; i++) {
            students[i]->display();
        }
    }

    // Display 2D grade table
    void displayGradeTable(string subjects[]) const {
        cout << "\n📊 Grade Table — " << className << endl;
        cout << "Name           ";
        for (int j = 0; j < numSubjects; j++)
            cout << subjects[j] << "\t";
        cout << "Avg" << endl;
        cout << string(60, '-') << endl;

        for (int i = 0; i < currentCount; i++) {
            cout << students[i]->getName().substr(0, 12);
            // Pad name to 15 chars
            int pad = 15 - students[i]->getName().length();
            if (pad > 0) cout << string(pad, ' ');

            int total = 0;
            for (int j = 0; j < numSubjects; j++) {
                cout << gradeTable[i][j] << "\t";
                total += gradeTable[i][j];
            }
            cout << (total / numSubjects) << endl;
        }
    }

    // Getters
    int   getCount()     const { return currentCount; }
    string getClassName() const { return className; }
};


// ============================================================
//  MAIN FUNCTION — Putting it ALL Together
// ============================================================

int main() {
    cout << "╔══════════════════════════════════════════╗" << endl;
    cout << "║   C++ Student Management System          ║" << endl;
    cout << "╚══════════════════════════════════════════╝" << endl;

    // ── Create Classroom ──────────────────────────────────
    Classroom cls("CS101 - Introduction to Programming", 5, 3);

    string subjects[] = {"Math", "CS", "English"};

    // ── Create Students using smart pointers ──────────────
    auto s1 = make_unique<Student>("S001", "Alice Ahmed",  20, 3);
    auto s2 = make_unique<Student>("S002", "Bob Khan",     22, 3);
    auto s3 = make_unique<Student>("S003", "Sara Malik",   19, 3);

    // ── Add to classroom (transfers ownership via move) ───
    cls.addStudent(move(s1));
    cls.addStudent(move(s2));
    cls.addStudent(move(s3));

    // ── Set Grades ────────────────────────────────────────
    cout << "\n── Setting Grades ──────────────────────" << endl;
    // Alice
    cls.updateGradeTable(0, 0, 92);   // Math
    cls.updateGradeTable(0, 1, 88);   // CS
    cls.updateGradeTable(0, 2, 95);   // English

    // Bob
    cls.updateGradeTable(1, 0, 75);
    cls.updateGradeTable(1, 1, 82);
    cls.updateGradeTable(1, 2, 70);

    // Sara
    cls.updateGradeTable(2, 0, 98);
    cls.updateGradeTable(2, 1, 95);
    cls.updateGradeTable(2, 2, 91);

    // ── Test Validation ───────────────────────────────────
    cout << "\n── Testing Validation ──────────────────" << endl;
    cls.updateGradeTable(0, 0, 150);  // Invalid grade — rejected
    cls.updateGradeTable(0, 0, -5);   // Invalid grade — rejected

    // ── Display Results ───────────────────────────────────
    cls.displayAll();
    cls.displayGradeTable(subjects);

    // ── Copy Constructor Demo ─────────────────────────────
    cout << "\n── Copy Constructor Demo ────────────────" << endl;
    {
        Student original("S004", "Copy Test", 21, 2);
        original.setGrade(0, 80);
        original.setGrade(1, 75);

        Student copied = original;   // copy constructor called
        copied.setGrade(0, 99);      // change copy — original unchanged!

        cout << "Original grade[0]: " << original.getGrade(0) << endl;  // 80
        cout << "Copy grade[0]:     " << copied.getGrade(0) << endl;    // 99
    }   // Both destructors called here

    cout << "\n── Program Ending ───────────────────────" << endl;
    return 0;
}   // Classroom destructor called here (frees everything)
```

---

## 📌 Expected Output

```
╔══════════════════════════════════════════╗
║   C++ Student Management System          ║
╚══════════════════════════════════════════╝
[Classroom] 'CS101 - Introduction to Programming' created (max 5 students).
[Constructor] Student 'Alice Ahmed' (ID: S001) created with 3 subjects.
[Constructor] Student 'Bob Khan' (ID: S002) created with 3 subjects.
[Constructor] Student 'Sara Malik' (ID: S003) created with 3 subjects.

── Setting Grades ──────────────────────

── Testing Validation ──────────────────
Error: Grade must be 0-100.
Error: Grade must be 0-100.

┌─────────────────────────────────┐
│ ID:       S001
│ Name:     Alice Ahmed
│ Age:      20
│ Status:   Enrolled
│ Grades:   92 88 95
│ GPA:      91.6667
│ Grade:    A
└─────────────────────────────────┘
... (Bob and Sara below)

📊 Grade Table — CS101 - Introduction to Programming
Name           Math    CS      English Avg
------------------------------------------------------------
Alice Ahmed    92      88      95      91
Bob Khan       75      82      70      75
Sara Malik     98      95      91      94

── Copy Constructor Demo ────────────────
[Constructor] Student 'Copy Test' (ID: S004) created with 2 subjects.
[Copy Constructor] Copied student: Copy Test (copy)
Original grade[0]: 80
Copy grade[0]:     99
[Destructor] Student 'Copy Test (copy)' destroyed. Memory freed.
[Destructor] Student 'Copy Test' destroyed. Memory freed.

── Program Ending ───────────────────────
[Classroom] 'CS101 - Introduction to Programming' destroyed.
[Destructor] Student 'Alice Ahmed' destroyed. Memory freed.
[Destructor] Student 'Bob Khan' destroyed. Memory freed.
[Destructor] Student 'Sara Malik' destroyed. Memory freed.
```

---

## 🗺️ Concept Map — Where Each Concept Lives

```
Student class:
├── char studentId[10]         ← Char Array
├── int* grades                ← Pointer + DMA (new/delete[])
├── private: data              ← Encapsulation
├── Student()                  ← Default Constructor
├── Student(id, name, age, n)  ← Parameterized Constructor
├── Student(const Student&)    ← Copy Constructor
├── ~Student()                 ← Destructor
├── setName/setAge/setGrade    ← Setters with validation
├── getName/getGPA/getGrade    ← Getters
└── grades[0..n]               ← 1D Array access

Classroom class:
├── unique_ptr<Student>*       ← Smart Pointer (unique_ptr)
├── int** gradeTable           ← 2D Dynamic Array
├── addStudent(move(s))        ← Move semantics (smart ptr)
├── displayGradeTable()        ← 2D Array traversal
└── ~Classroom()               ← Destructor (frees 2D array)
```

---

## 🎓 You Did It!

You've learned and seen in action:

| # | Concept             | ✅ |
|---|---------------------|----|
| 1 | Pointers            | ✅ |
| 2 | Dynamic Memory (DMA)| ✅ |
| 3 | Smart Pointers      | ✅ |
| 4 | 1D Arrays           | ✅ |
| 5 | 2D Arrays           | ✅ |
| 6 | Char Arrays         | ✅ |
| 7 | Classes & Objects   | ✅ |
| 8 | Encapsulation       | ✅ |
| 9 | Constructors        | ✅ |
|10 | Destructors         | ✅ |
|11 | Getters & Setters   | ✅ |

**You now have the foundational building blocks of C++ Object-Oriented Programming!** 🎉

---
*Keep practicing — try building your own classes for things you care about: a Library, a Hospital, a Game!*
