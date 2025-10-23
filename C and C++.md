# Differences with CS136

In CS136, the **C99** standard was used. In this course, the **C++20** standard is used.

## List of Changes:

- Imports are in the form `import <library_name>;` instead of `#include <library_name>;`. This speeds up the compiling time by ensuring all libraries are compiled once manually then cached for use in the future.
- Instead of `printf`, and `scanf` we use `cout` and `cin` for the `stdin` and `stdout` [[Streams]] .
- `std::` is used at the beginning of most functions to indicate that they are from the standard library to avoid [[Overloading]]. Since this is tedious, C++ uses `using namespace std` to indicate that all functions without this preface are called from the standard library.

```C
// In C
#include <stdio.h>
int main() {
	char c;
	scanf("%d", &c);
	printf("The value of c is: %d\n", c);
}
```

```C++
// In C++
import <iostream>;
using namespace std;
int main() {
	char c;
	cin >> c;
	cout << "The value of c is: " << c << endl;
}
```

## Similarities

### `int main()`

`main` must return an `int`, not `void`. The operating system calls `main`, and the return value is a status code used to indicate if a program has succeeded or not.

- `0` indicates that the program is valid. Any other output indicates an error.
- The `return` can be omitted from main by default, it will automatically return `0`

### Types of Passing Variables

**Pass-by-value** receives a copy of x and mutates the copy. The original **does not change**. This approach is simple but slow since a copy of the variable needs to be made. 

```C++
// Example of pass-by-value
void inc(int n) { ++n; }

int x = 5;
inc(x);
cout << x; // outputs 5 since inc neither mutates the original value nor returns the value to mutate.
```

**Pass-by-pointer** receives a copy of x's pointer and mutates the value inside the pointer. The original changes. This approach is faster but can be confusing.

```C++
// Example of pass-by-pointer
void inc(int *n) { ++(*n); }

int x = 5;
inc(&x);
cout << x; // outputs 6 since inc mutates the value associated with the pointer.
```

**Pass-by-reference** receives reference to x and mutates it. The original changes. This approach is faster and much more intuitive
```C++
void inc(int &n) { ++n; }

int x = 5;
inc(x);
cout << x; // outputs 6 since inc mutates x by reference
```

**Pass-by-const-ref** is preferred over **pass-by-value** for anything larger than a pointer. Unless the function needs to make a copy anyways, in which case use pass-by-value.

```C++
int f(int &n);
f(5); // invalid since 5 is not a valid memory location. The passed reference must be an l-value. You cannot change "5".

int g(const int &n);
g(5); // valid since n cannot be changed. 5 is stored in a temporary location on the stack so there is an l-value to point to
```

In the case of `istream foperator >> (istream &in, int &n);` the stream is being passed and returned by reference to save copying. This is important because stream variables are not allowed to be copied.

Generally, use the following guideline to design functions:
* **Pass-By-Const-Ref** is the default
* If changes should be reflected, then use **Pass-By-Reference**
* If passing `nullptr`, then use **Pass-By-Pointer**
* If dealing with small arguments or need a temporary argument to work with, then **Pass-By-Value**