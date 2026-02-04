---
title: "iostream"
publish: "false"
level: "0"
total reviews: "0"
---
## References

- [[C++]]


## Flashcards

What is the difference between `cout`, `cerr`, and `clog`?
->
- `cout`: Standard output stream, buffered, used for normal program output
- `cerr`: Standard error stream, **unbuffered**, used for error messages (immediately flushed)
- `clog`: Standard error stream, **buffered**, used for logging messages

---
What happens when you read with `cin >>` and the input type doesn't match the expected type?
->
- The stream enters a **fail state**
- The extraction fails and leaves bad data in the buffer
- Subsequent reads will fail until you call `cin.clear()` to reset the state
- You should also call `cin.ignore()` to clear the bad input from the buffer

---
What's the difference between `getline(cin, str)` and `cin.getline(buffer, size)`?
->
- `getline(cin, str)`: Free function, reads into `std::string`, no size limit, more flexible
- `cin.getline(buffer, size)`: Member function, reads into C-style char array, requires size limit

---
How do you read an entire line including spaces with cin?
->
```cpp
std::string line;
std::getline(cin, line);
// Note: cin >> line would stop at first whitespace
```

---
What does `cin.ignore(numeric_limits<streamsize>::max(), '\n')` do?
->
Ignores all characters in the input buffer up to and including the next newline character. Commonly used to clear the buffer after a failed read or to skip to the next line.

---
What are the four stream state flags and what do they mean?
->
- `good()`: No errors, stream is ready for I/O
- `eof()`: End-of-file reached
- `fail()`: Logical error (e.g., type mismatch, format error)
- `bad()`: Read/write error (e.g., hardware failure, corrupted stream)

---
How do you check if a stream operation succeeded?
->
```cpp
// Method 1: Direct boolean check
if (cin >> value) {
    // Success
}

// Method 2: Check state flags
if (cin.fail()) {
    // Handle error
    cin.clear();  // Reset state
    cin.ignore(numeric_limits<streamsize>::max(), '\n');  // Clear buffer
}
```

---
What does `std::ios::sync_with_stdio(false)` do and when should you use it?
->
- Disables synchronization between C++ streams (`cin`, `cout`) and C streams (`scanf`, `printf`)
- Makes C++ streams **faster** but you can't mix C and C++ I/O
- Use in competitive programming or performance-critical code
- Call before any I/O operations

---
What's the purpose of `cin.tie(nullptr)` and when is it useful?
->
- By default, `cin` is tied to `cout`, meaning `cout` is flushed before each `cin` operation
- `cin.tie(nullptr)` removes this tie for **performance gains**
- Useful in competitive programming when you don't need immediate output
- Be careful: output may not appear immediately

---
How do you set precision for floating-point output?
->
```cpp
#include <iomanip>

// Method 1: Manipulator (temporary)
cout << setprecision(2) << 3.14159;  // 3.1

// Method 2: With fixed (shows 2 decimal places)
cout << fixed << setprecision(2) << 3.14159;  // 3.14

// Method 3: Member function (persistent)
cout.precision(2);
cout << 3.14159;  // 3.1
```

---
What's the difference between `endl` and `'\n'`?
->
- `'\n'`: Simply adds newline character
- `endl`: Adds newline **and flushes the buffer** (forces immediate output)
- `endl` is slower due to flush operation
- Use `'\n'` for better performance unless you need immediate output

---
How do you format output with specific width and fill characters?
->
```cpp
#include <iomanip>

cout << setw(10) << setfill('0') << 42;  // "0000000042"
cout << setw(10) << left << "hello";     // "hello     "
cout << setw(10) << right << "hello";    // "     hello"

// setw() applies only to next item, setfill() persists
```

---
What does `cin.peek()` do and when is it useful?
->
- Returns the next character in the input buffer **without extracting it**
- Useful for looking ahead to decide how to parse input
- Doesn't change stream position or state
```cpp
char next = cin.peek();
if (next == '\n') {
    // Handle empty line
}
```

---
How do you read input until EOF?
->
```cpp
// Method 1: Read with while loop checking stream state
std::string line;
while (std::getline(cin, line)) {
    // Process line
}

// Method 2: Read values until stream fails
int value;
while (cin >> value) {
    // Process value
}
```

---
What's the purpose of `std::boolalpha` manipulator?
->
Makes boolean values print as "true"/"false" instead of "1"/"0"
```cpp
bool flag = true;
cout << flag;              // "1"
cout << boolalpha << flag; // "true"
```
