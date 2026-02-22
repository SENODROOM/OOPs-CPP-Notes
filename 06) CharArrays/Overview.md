# 🔤 Char Arrays (C-Strings) in C++
### "The old way to store text — one character at a time."

---

## 🤔 What is a Character?

Before understanding char arrays, let's understand `char`.

A `char` is a data type that holds a **single character** — one letter, one digit, one symbol.

```cpp
char letter = 'A';    // Single quotes for chars!
char digit  = '9';
char symbol = '!';
char space  = ' ';

cout << letter;   // A
```

> ⚠️ Use **single quotes** `' '` for characters, and **double quotes** `" "` for strings!

---

## 🔤 What is a Char Array (C-String)?

A **char array** is simply an array of characters used to store text (a "string").

It comes from the C language (before C++ existed), which is why it's also called a **C-string**.

The most important thing: **every C-string ends with a special character `'\0'` (null terminator)**. This invisible character tells the computer "the string ends here."

```
char name[] = "Alice";

How it's stored in memory:
┌─────┬─────┬─────┬─────┬─────┬──────┐
│ 'A' │ 'l' │ 'i' │ 'c' │ 'e' │ '\0' │
└─────┴─────┴─────┴─────┴─────┴──────┘
  [0]   [1]   [2]   [3]   [4]    [5]
                                   ↑
                            null terminator
                          (invisible sentinel)
```

The null terminator `'\0'` is automatically added when you use double quotes `" "`.

---

## 📝 Declaring Char Arrays

### Method 1: From a string literal (most common)
```cpp
char name[] = "Alice";   // size = 6 (5 chars + '\0')
char city[] = "Lahore";  // size = 7 (6 chars + '\0')
```

### Method 2: Specify size manually
```cpp
char name[10] = "Alice";   // size 10, only 5+1 used, rest are '\0'
char name[10];             // uninitialized — garbage inside
```

### Method 3: Character by character (rare)
```cpp
char name[] = {'A', 'l', 'i', 'c', 'e', '\0'};   // MUST add \0 manually!
```

> ⚠️ Always make your array **big enough** to hold the text PLUS the `'\0'` at the end!

---

## 📺 Displaying a Char Array

```cpp
char name[] = "Alice";
cout << name;   // prints: Alice (automatically stops at '\0')
```

---

## ⌨️ Reading Input into a Char Array

```cpp
char name[50];

// Option 1: Single word (stops at space)
cin >> name;                // "Alice" ✅  but "Alice Smith" ❌ (only reads "Alice")

// Option 2: Entire line including spaces
cin.getline(name, 50);     // reads up to 50 chars or until Enter
```

---

## 📚 The `<cstring>` Library

C++ provides many built-in functions to work with C-strings. Include `<cstring>` to use them.

```cpp
#include <cstring>
```

### `strlen()` — Get the Length

Returns the number of characters **NOT counting** the `'\0'`.

```cpp
char name[] = "Alice";
cout << strlen(name);   // 5  (not 6 — doesn't count '\0')

char city[] = "Lahore";
cout << strlen(city);   // 6
```

### `strcpy()` — Copy a String

Copies one string into another.

```cpp
char source[] = "Hello";
char destination[20];

strcpy(destination, source);   // destination = "Hello"
cout << destination;           // Hello
```

> ⚠️ Make sure destination is large enough, or you'll overflow!

### `strcat()` — Concatenate (Join) Strings

Appends one string to the end of another.

```cpp
char first[20] = "Hello";
char second[] = " World";

strcat(first, second);    // first = "Hello World"
cout << first;            // Hello World
```

### `strcmp()` — Compare Two Strings

Compares two strings alphabetically.
- Returns `0` if they are **equal**
- Returns negative if first < second
- Returns positive if first > second

```cpp
char a[] = "Apple";
char b[] = "Banana";
char c[] = "Apple";

cout << strcmp(a, b);   // negative (A comes before B)
cout << strcmp(b, a);   // positive (B comes after A)
cout << strcmp(a, c);   // 0 (equal!)

// Common usage:
if (strcmp(a, c) == 0)
    cout << "Strings are equal!";
```

### `strchr()` — Find a Character

Returns a pointer to the first occurrence of a character.

```cpp
char str[] = "Hello World";
char* found = strchr(str, 'W');

if (found)
    cout << found;   // "World"  (prints from 'W' onwards)
```

### `strupr()` / `strlwr()` — Convert Case (non-standard)

```cpp
char name[] = "alice";
// Note: these may not work on all compilers
// Use manual loops instead for portability
for (int i = 0; name[i]; i++)
    name[i] = toupper(name[i]);

cout << name;   // ALICE
```

---

## 🔍 Accessing Individual Characters

Since it's an array, you can access each character by index!

```cpp
char name[] = "Alice";

cout << name[0];   // A
cout << name[1];   // l
cout << name[2];   // i
cout << name[3];   // c
cout << name[4];   // e
cout << name[5];   // '\0'  (null terminator)
```

### Looping Through Characters

```cpp
char word[] = "Hello";

// Method 1: Using length
for (int i = 0; i < strlen(word); i++)
    cout << word[i] << " ";   // H e l l o

// Method 2: Stop at '\0' (elegant!)
for (int i = 0; word[i] != '\0'; i++)
    cout << word[i] << " ";   // H e l l o

// Method 3: Even shorter
for (int i = 0; word[i]; i++)   // '\0' is treated as false
    cout << word[i] << " ";
```

---

## 🧪 Complete Working Example — Name Processor

```cpp
#include <iostream>
#include <cstring>
#include <cctype>    // for toupper(), tolower()
using namespace std;

int main() {
    char name[50];
    char greeting[100];

    // Get input
    cout << "Enter your name: ";
    cin.getline(name, 50);

    // Show info
    cout << "\nName: " << name << endl;
    cout << "Length: " << strlen(name) << " characters" << endl;

    // Count vowels
    int vowels = 0;
    for (int i = 0; name[i]; i++) {
        char c = tolower(name[i]);
        if (c=='a' || c=='e' || c=='i' || c=='o' || c=='u')
            vowels++;
    }
    cout << "Vowels: " << vowels << endl;

    // Convert to uppercase
    char upper[50];
    strcpy(upper, name);
    for (int i = 0; upper[i]; i++)
        upper[i] = toupper(upper[i]);
    cout << "Uppercase: " << upper << endl;

    // Build greeting
    strcpy(greeting, "Hello, ");
    strcat(greeting, name);
    strcat(greeting, "!");
    cout << greeting << endl;

    // Reverse the name
    char reversed[50];
    int len = strlen(name);
    for (int i = 0; i < len; i++)
        reversed[i] = name[len - 1 - i];
    reversed[len] = '\0';   // add null terminator!
    cout << "Reversed: " << reversed << endl;

    return 0;
}
```

**Sample Output:**
```
Enter your name: Alice

Name: Alice
Length: 5 characters
Vowels: 3
Uppercase: ALICE
Hello, Alice!
Reversed: ecilA
```

---

## ⚔️ Char Array vs `std::string`

C++ also has a modern `string` class. Let's compare:

| Feature          | Char Array (C-string)         | `std::string`                  |
|------------------|-------------------------------|-------------------------------|
| Include          | `<cstring>`                   | `<string>`                    |
| Declaration      | `char name[50] = "Alice";`    | `string name = "Alice";`      |
| Length           | `strlen(name)`                | `name.length()` or `name.size()` |
| Copy             | `strcpy(dst, src)`            | `dst = src;`                  |
| Concatenate      | `strcat(a, b)`                | `a + b` or `a += b`           |
| Compare          | `strcmp(a, b) == 0`           | `a == b`                      |
| Input (line)     | `cin.getline(name, 50)`       | `getline(cin, name)`          |
| Resize           | ❌ Fixed size                 | ✅ Automatic                  |
| Safety           | ⚠️ Can overflow               | ✅ Safe                       |
| Speed            | ✅ Slightly faster            | ⚠️ Slightly slower            |

```cpp
// C-string way (old)
char name1[20];
strcpy(name1, "Alice");
strcat(name1, " Smith");
cout << strlen(name1);   // 11

// std::string way (modern — preferred)
string name2 = "Alice";
name2 += " Smith";
cout << name2.length();  // 11
```

> **When to use which?**
> - Use `std::string` for most modern C++ code — it's safer and easier
> - Use char arrays when working with C libraries, hardware, or performance-critical code

---

## 🧩 2D Char Arrays — Array of Strings

You can store multiple strings in a 2D char array:

```cpp
char names[3][20] = {
    "Alice",
    "Bob",
    "Charlie"
};

for (int i = 0; i < 3; i++)
    cout << names[i] << endl;

// Output:
// Alice
// Bob
// Charlie
```

---

## ⚠️ Common Mistakes

```cpp
// ❌ Forgetting null terminator when building manually
char name[6] = {'A', 'l', 'i', 'c', 'e'};   // Missing '\0'!
cout << name;   // prints garbage after "Alice"

// ✅ Add null terminator
char name[6] = {'A', 'l', 'i', 'c', 'e', '\0'};

// ❌ Buffer overflow (writing beyond array bounds)
char name[5] = "Alice";    // Only 5 — no room for '\0'!

// ✅ Make room for '\0'
char name[6] = "Alice";    // 5 chars + 1 for '\0'

// ❌ Comparing with ==
char a[] = "Hello";
char b[] = "Hello";
if (a == b)   // ❌ Compares ADDRESSES not content!

// ✅ Use strcmp
if (strcmp(a, b) == 0)   // ✅ Correct
```

---

## 📋 C-String Functions Cheat Sheet

| Function           | Purpose                        | Example                        |
|--------------------|--------------------------------|--------------------------------|
| `strlen(s)`        | Get length (no `\0`)           | `strlen("Hi") = 2`            |
| `strcpy(dst, src)` | Copy src into dst              | `strcpy(a, b)`                |
| `strcat(dst, src)` | Append src to end of dst       | `strcat(a, "!")` → "Hello!"   |
| `strcmp(a, b)`     | Compare (0 = equal)            | `strcmp("a","a") = 0`         |
| `strchr(s, c)`     | Find character c in s          | `strchr("Hello", 'l')`        |
| `strstr(s, t)`     | Find substring t in s          | `strstr("Hello", "ell")`      |
| `toupper(c)`       | Convert char to uppercase      | `toupper('a') = 'A'`          |
| `tolower(c)`       | Convert char to lowercase      | `tolower('A') = 'a'`          |

---

## 🎯 Key Takeaways

1. A char array stores text as a sequence of characters
2. Every C-string ends with `'\0'` (null terminator) — added automatically with `" "`
3. Use **single quotes** for chars, **double quotes** for strings
4. Array size must be at least **text length + 1** (for `'\0'`)
5. Use `<cstring>` functions: `strlen`, `strcpy`, `strcat`, `strcmp`
6. **Don't use `==` to compare** C-strings — use `strcmp()`
7. For modern C++ code, **prefer `std::string`** over char arrays

---
*Next up: Classes & Objects — creating your own custom data types!* →
